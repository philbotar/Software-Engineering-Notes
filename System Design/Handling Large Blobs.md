When should you use blob storage?
	As a general rule of thumb, if it's over 10MB and doesn't need SQL queries, it should probably be in blob storage.

What should we avoid when uploading large files when communicating with a server? 
	Avoid sending the object straight to the backend. Use a presigned URL from the backend to upload straight to storage. The headers will dictate what and there will be a hash to tell you where to upload. Same goes for downloads.
![[assets/Screenshot 2025-12-14 at 1.21.44 pm.png]]

Whats the process for uploading files?
	User sends a request to upload to the server. The server generates a pre-signed url, creates a metadata row in a database to correspond to the upload (set as pending) and returns the presigned url to the user. The user then uploads the data straight to the object storage, and the storage will emit an event (Think SQS) to notify its completion. The Server will then set the row from pending to complete. 
![[assets/Screenshot 2025-12-14 at 1.24.50 pm.png]]


Whats inside a presigned URL?
	File Size, Content type, credentials, date, expiry time, signature (hashed credentials), host. 

How should we be doing downloads? 
	We request to download from the server. The server checks the credentials and returns a presigned url to the user, with all necessary credentials and scoped perms. The user then downloads from that url, which is straight from the blob storage, or the CDN if stored there.

Whats the difference between Blob Storage Signatures and CDN Signatures? 
	**Blob storage signatures** (like S3 presigned URLs) are validated by the storage service using your cloud credentials. The storage service has your secret key and can verify that you generated the signature.
	**CDN signatures** (like CloudFront signed URLs) are validated by the CDN edge servers using public/private key cryptography. You hold the private key and sign the URL. The CDN has the corresponding public key and validates signatures at edge locations worldwide - no need to call back to your servers or the origin storage service.

How does resumable uploads work for large files?
	It works through Chunked Uploads APIs. Each 5MB part gets its own presigned URL. The client uploads these parts, tracking completion checksums returned by the storage service. These are just hashes of the uploaded data. If the connection drops, we only start from the last hashed value, maintaining the state. Once complete, the "chunks" disappear and the file is kept as its whole.

When to use handling large blob strategies?
	YouTube/Video Platforms - Video uploads are the perfect use case. A user uploads a 2GB 4K video file. The web app generates a presigned S3 URL, the client uploads directly to S3 (often using multipart for resumability), and S3 events trigger transcoding workflows. Downloads work similarly - CloudFront serves video segments with signed URLs, enabling adaptive bitrate streaming without your servers ever touching the video bytes.
	Instagram/Photo Sharing - Photo uploads bypass the API servers entirely. The mobile app gets presigned URLs for original images (which can be 50MB+ from modern cameras), uploads directly to S3, then S3 events trigger async workers to generate thumbnails, apply filters, and extract metadata. The feed serves images through CloudFront with signed URLs to prevent hotlinking.
	Dropbox/File Sync - File uploads and downloads are pure serverless data access. When you drag a 500MB video into Dropbox, it gets presigned URLs for chunked uploads, uploads directly to blob storage, then triggers sync workflows. File shares generate time-limited signed URLs that work without the recipient having a Dropbox account.
	WhatsApp/Chat Applications - Media sharing (photos, videos, documents) uses presigned URLs. When you send a video in WhatsApp, it uploads directly to storage, then the chat system only passes around the file reference. Recipients get signed download URLs that expire after a period, ensuring media can't be accessed indefinitely.


How do you prevent abuse of Large Uploads? 
	Don't let users immediately access what they uploaded. By using a processing pipeline where the upload goes into a quarantine bucket first, and gets analysed for virus and content. Once all the checks pass, does it get moved to the published bucket and database status is set to Available. 

How do you ensure downloads are fast in Large File Uploads?
	Use Content Delivery Networks (CDNs): Solves geographic latency by caching content at edge locations worldwide. This reduces request time from ~200ms (direct blob storage) to single-digit milliseconds for subsequent users.​
	Enable HTTP Range Requests: Essential for large files (5GB+), this allows clients to request specific byte chunks (e.g., Range: bytes=0-10485759). This mechanism enables resumable downloads, so users only retry missing parts if the connection breaks.​
	 Parallel Chunk Downloads (Advanced): For massive datasets, splitting files into parts and downloading 4–6 chunks simultaneously can increase speeds by 3–4x by maximizing bandwidth usage.​
	 Pragmatic Strategy: Default to serving files via CDN with proper cache headers and Range Request support. Reserve complex parallel downloading logic only for extreme multi-gigabyte use cases.

