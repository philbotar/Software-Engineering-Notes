These notes are in the form of flashcards

What are the 4 common Strategies to scale writes? 1. Vertical Scaling and database choices 2. Sharding and partitioning 3. Handling Bursts with Queues and Load Shedding 4. Batching and Hierarichal aggregation.

When running into Write Scaling issues, whats the first line of defence?
Vertical Scaling, whilst we think that most computers are 8 cores with disk, what we have at our disposal with cloud is way larger, and is a good starting point before increasing complexity.

Whats an example of a write heavy database?
Cassandra. Cassandra achieves superior write throughput through its append-only commit log architecture. Instead of updating data in place (which requires expensive disk seeks), Cassandra writes everything sequentially to disk. This lets it handle 10,000+ writes per second on modest hardware, compared to maybe 1,000 writes per second for a traditional relational database doing the same work. Its way slower at reads as it has to check multiple files and aggregate results.

What are some databases that are better for write heavy workloads? - Time Series Databases liuke InfluxDB are built for high volume sequential writes with timestamps - Log Structured Databases like LevelDB append new data rather than updating in place - Column Stores like ClickHouse can batch writes efficiently for analytics workloads.

What are some things we can do to our database to optimise for writes? - Disable expensive features like foreign key constraints, complex triggers, or full text search indexing during high-write periods. - Tune Write ahead logging - Databases like PostgresSQL can batch multiple transactions before flushing to disk - Reduce Index Overhead - fewer indexes mean faster writes, though youll pay for it on reads.

When mentioning sharding in order to improve write performance, what must you mention?
Selecting a good partitioning key. Something like an ID that allows you to spread the data efficiently across shards. Ensure you know about consistent hashing, Virtual nodes and slot assignment schemes.

What is Vertical Partitioning and how can you leverage seperation to improve writes?
We can split out tables from monolithic structures into seperate DBS/Tables that leverage the different usecases of database types. Think if you have a post, you know that post will be read heavy, so you store the data in a postgres db with indexing. Then for likes and comments, you use a Key-Value store to leverage high frequency updates. Lastly for analytics, you use a time series DB for quick append. This covers all your bases efficiently.

How do you handle bursty traffic?
You implement a write queue. This acts as a buffer between your system and the bursty traffic. Keep in mind that queues introduce latency, and to not use them when theres no tolerance for delays or inconsistent reads.

What is load shedding?
Load shedding tries to make a determination of which writes are going to be most important to the business and which are not. If we can drop the less important writes, we can keep the system running and the more important writes will still be processed.

How can Batching help us perform writes efficiently?
By batching multiple writes together, we can amortize the time to write and is supported by postgres as a better way to implement. There is the catch, if the application isn't the source of truth, this is fine as in case of failure the data can be recovered (think kafka), but if the application is the source of truth then this becomes not viable as we can lose the whole batch.

What is intermediate processing in the case of batching?
Think if we had a bunch of requests with likes coming in, instead of writing each one individually, we aggregated the results as they came in, processing them in batches once a condition has been fulfilled. If we have 100 likes a minute, we can reduce the number of writes to 1 instead of 100!

What is hierarchical aggregation, and how does it reduce the fan-in/fan-out problem for live comments/likes?
Build aggregated views in stages: shard incoming events to write processors by comment ID (or liked comment ID), batch/aggregate counts over a time window, send compact updates to a root processor to merge, then fan out to viewers via consistent-hash broadcast nodes. This cuts per-system write load from “to every viewer” to “to a smaller set of nodes,” trading off a bit of extra latency and eventual consistency.
![[assets/Screenshot 2025-12-14 at 10.15.40 am.png]]

What is a strong candidate response when identifying that therell be write scaling issues? - "With millions of users posting content, we'll quickly hit write bottlenecks. Let me see what kind of write throughput we're dealing with ...Ok this is significant! I'll come back to that in my deep dives." - "For the posting writes, I think it's sensible for us to partition our database by user ID. This will spread the load evenly across our shards. We'll still need to handle situations where a single user is posting a lot of content, but we can handle that with a queue and rate limits."

What are some common system design questions where we need to scale writes ?
Instagram/Social Media - Perfect for demonstrating sharding by user ID for posts, vertical partitioning for different data types (user profiles, posts, analytics), and hierarchical storage for older posts.
News Feeds - News feeds require careful tuning between write volume for celebrity posts which need to be written to millions of followers and read volume when those millions of use come to consume their feed.
Search Applications - Search applications are often write-heavy with substantial preprocessing required in order to make the search results quick to retrieve. Partitioning and batching are key to making this work.
Live Comments - Live comments are a great example of a system which can benefit from hierarchical aggregation to avoid an intractable all-to-all problem where millions of viewers need a shared view of the activities of millions of their peers.

"How do you handle resharding when you need to add more shards?"
This is the classic operational challenge with sharding. You started with 8 shards, but now you need 16. How do you migrate data without downtime?
The naive approach is to take the system offline, rehash all data, and move it to new shards. But this creates hours of downtime for large datasets.
Production systems use gradual migration which targets writes to both locations (e.g. the shard we're migrating from and the shard we're migrating to). This allows us to migrate data gradually while maintaining availability.
The dual-write phase ensures no data is lost during migration. You write to both old and new shards, but read with preference for the new shard. This allows you to migrate data gradually while maintaining availability.

"What happens when you have a hot key that's too popular for even a single shard?" 1. Split all keys, splitting keys a fixed number of k times. Instead of post1Likes key, we can have post1Likes-0, post1Likes-1, post1Likes-2, all the way through to post1Likes-k-1. This means that each shard will only have a subset of the data for a given post, but the write volume for a given shard for a given post is reduced by k times.Downsides are that we are increasing the size of our overall dataset by k times, and multiplied the read volume by k, as in order to get the number of likes for a post, we need to reach each partition with a key. 2. Split Hot Keys Dynamically. Another solution is breaking the hot key into multiple sub-keys dynamically based on whether the key is hot or not. For the viral tweet example, you might split the like count across 100 sub-keys, each handling 1,000 likes per second. When reading, you aggregate the counts from all sub-keys. Both of these approaches work for metrics that can be aggregated (likes, views, counts, balances) but don't work for data that must remain atomic (user profiles).
We have two main solutions here: We can have the readers *always* check all the sub-keys. This means the same read amplification as the key split approach, but it's simple to implement and easy to understand. When a writer detects a key may be hot (they can keep local statistics), they can conditionally write to the sub-keys for that key. Another, more burdensome approach is to have the writers *announce* the split to the readers. All readers would need to receive this announcement before the split is executed. This is more complex to implement and understand, but it's more efficient and keeps readers from reading splits that don't exist.
