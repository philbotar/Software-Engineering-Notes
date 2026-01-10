**Questions**
1. What should be your default for real time updates? 
	1. You should default to Polling. Chances are you won't need the complexity of Websockets.

2. What does TCP require to establish a connection?
	1. it requires a 3 step Handshake. Client sends Syn, server sends Syn-Ack and then client finally responds with Ack to initiate the connection.

3. What are the issues with using long polling for high frequency real time updates ? 
	1. Using long polling for high frequency real time updates has issues because as the request is being fulfilled, if more changes on the server side during the time its being fulfilled, it has to wait for a completely new request from the Client in order to notify it. 

4. What is SSE Built on ? How does it use HTTP ?
	1. SSE is built on HTTP, by using a special type of encoding. The client sends a request to the server, and because of the headers, it maintains an open connection / stream, where the server can send packets. Its essentially a http request that never gets fulfilled. 

5. What are websockets an abstraction of ? 
	1. Websockets are an abstraction of TCP. It utilises that connection to send data 2 ways. 

6. What layer load balancer should you use for Websockets? 
	1. Layer 4. Since websockets are an abstraction of TCP, we can't really utilise all of the benefits of a layer 7 load balancer, and it makes more sense to use layer 4.

7. Can you redirect web socket packages to different services ? 
	1. No, you cannot easily redirect WebSocket packets to different services once the connection is established. This is because:
		1. **Stateful connection** - WebSockets maintain a persistent, stateful TCP connection between client and specific server
		2. **Connection affinity** - Once the WebSocket handshake completes, that connection is bound to that particular server instance
		3. **No request-level routing** - Unlike HTTP where each request can be routed independently, WebSocket frames flow through an established connection that can't be dynamically rerouted

		**Workaround:** You need an **intermediary/proxy server** (like a WebSocket gateway) that:
		- Maintains the connection with the client
		- Routes messages internally to different backend services via other protocols (HTTP, RPC, message queues)
		- Aggregates responses and sends them back through the same WebSocket connection

8. In what situations should you be using websockets? 
	1. Only when you need real time, 2 way communication between client and server. 

9. What is WebRTC? What does it stand for ?
	1. Web Real Time Communication: Its a way to facilitate peer to peer communication. It works by pinging a port to force it open, and then allow it to get open. 

10. When should you use WebRTC?
	1. You should use it for peer to peer things such as gaming and google meet. Outside of this it shouldnt really be used. 

11. What is the tree of questions i should answer in order to know what to use for real time updates ?
	1. Ask yourself:
		1. How real time is it, if not very use polling
		2. IF real time, do you need 2 way communication? if no use SSE.
		3. Else if you need 2 way, use WebSockets, otherwise if you need Peer To Peer use WebRTC.

12. How can we have low latency server side polling ? (2 Ways)
	1. Consistent hashing to servers, and Pub Sub Systems. 

13. What is a good application to use for consistent hashing?
	1. Zookeeper

14. What are some cons of consistent hashing? 
	1. **Cons of Consistent Hashing:**
		1. **Rebalancing overhead** - When nodes are added or removed, keys need to be remapped to new servers, causing disruption
		2. **Hotspots / Uneven load distribution** - If hash distribution isn't uniform, some servers can become overloaded while others are underutilized
		3. **Cascading failures** - When a server goes down, all its load shifts to the next server in the ring, which can overload that server and cause it to fail too (this is what you meant by "dumping too many connections")
		4. **Virtual nodes complexity** - To solve uneven distribution, you need virtual nodes (vnodes), which adds implementation complexity
		5. **Connection loss during rebalancing** - When servers are removed or connections are remapped, active connections (like WebSockets) are dropped and need to be re-established

15. Give a walkthrough of how a pub sub system works in a backend? 
	1. We have a server that manages connections, and then we have our pub sub system to deliver the packets. 

16. Whats the diffference between kafka and redis for pub sub systems? 
	1. Kafka stores the messages created in an append only log, whereas redis is temporal. 

17. What is the decision tree for server side real time updates? 
	1. Latency Sensitive? If no, use Server Side Polling
	2. If stateRRRRR