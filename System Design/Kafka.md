# Kafka Queues

Kafka is a

- Open source
- Distributed
- Event Streaming

playform that can be used either as a message queue or as a stream processing system.

Kafka excels in

- Delivering High performance,
- Scalability,
- Durability

### An Example

Imagine we have a queue that take in football stats for x teams.

We would have

- Producers: the services that send data to the queue to be processed
- Consumers: The services waiting to take in the data from the queue.

But imagine if our queue wasn't big enough, and we had alot of data coming in to be processed, too much for a single queue to handle.

So how could we deal with this?

Kafka allows us to split out the data to have their own queue per game, to ensure the order of values is correct. **Messages sent and recieved through kafka require a user specified distribution strategy**.

So now we dealt with the queues managing alot of data, but what about our consumers? How can we manage so they dont get overloaded with data and dont deal with data more than once?

We can group the consumers together into consumer groups. With these groups each event is guaranteed to only be processed by one consumer in the group.

Lastly, what if we wanted to expand out of just football, and also include basketball? This is where Topics come in, allowing us all of the benefits of before with extra queues and consumer groups, but grouped again by a topic. This allows consumers to onyl subscribe to the topic they want to consume from.

### Terminology and Architecture

1. **Broker**: Individual servers (Physical or Virtual) that are responsible for storing data and serving clients. The more brokers you have, the more clients you can serve and more data you can store.
2. **Partitions**: Each broker has a number of partitions. Partitions are ordered, immuatable sequence of messages that is continually appened to -- think like a log file. Partitions is how kafka scales as they allow fo messages to be consumed in parallel.
3. **Topics**: A logical grouping of partitions. Topics is how we pub / sub to data in kafka. When you publish a message, you publish it to a topic, and when you consume a message, you consume it from a topic. Topics are always multi producer, allowing more than one to write to it.
4. **Producers** They write data to topics
5. **Consumers**: They read data from topics.

A topic is a logical grouping of messages. A partition is a physical grouping of messages. A topic can have multiple partitions, and each partition can be on a different broker. Topics are just a way to organize your data, while partitions are a way to scale your data.


## How Kafka Works

When events happen, the producer formats a

- Value (Required)
- Key (Optional)
- Timestamp (Optional)
- Headers (Optional)

Keys arent strictly required but are used to determine which partition gets the message. Likely you want o define it during an interview to ensure it gets processed in order, adn the choice of that key is important.

#### What happens when a message is published to a Kafka Topic?

1. Producer sends message to the client class locally.
2. The client class uses a partitioning algorithm to find out which partition needs the data. If a key is provided it uses that.
3. Once the partition has been decided, kafka uses the Kafka Cluster Metadata (which is maintained by the controller) to identify which broker has the partition.
4. Once the broker has been identified, the client will send the data to that broker.


So the 2 steps to finding out where to send request.

1. **Partition Determination**: Uses a partitioning algorithm that hashes the message key to find out which partition to send it to, if it doesnt have a key, kafka can round robin or follow different partition logic.
2. **Broker Assignment**: Kafka identifies which broker holds that particular partition. The mapping of partitions is on the cluster metadata handled by the controller (which is a role within the broker cluster). The producer uses this metdata to send the message directly to the broker that hosts the target partition.
