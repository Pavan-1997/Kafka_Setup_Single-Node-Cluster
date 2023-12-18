## Types of Data:

- Structured Data - Organised data
- Semi-Structured Data - JSON, XML
- Un-Structured Data - No structure defined
- Quasi Structured - Textual data

`Collect Data -> Tranfer Data -> Store Data -> Analyse

## Data Transfer Considerations:

- Reliability
- Speed
- Availability
- Scalability
- Security 

## Kafka Origin:

- LinkedIn discovered in Leo webpages by connecting to backend DB
- Service Oriented Architechture
- Custom Datapipeline - Stream, Queuing
- Kafka - Distributed System
- Later became open source in 2011
- Later was adapted by Apache and kept in incubator at 2012
- Many organizations started using Kafka


## Kafka: Distributed Stream Platform

- We want to publish or subscribe the stream of records
- You can process/modify the records (data, key values, timestamp)
- Topics- collection of records
- Fast, reliable, performance, durable and scalable
- Store streams of data in Fault Tolerant way
- Single machine / Cluster - a collection of Kafka brokers
- Kafka competitors are AMQP which is open source, Rabbit MQ is an asynchronous and open source, JMS, Amazon Kinesis, ActiveMQ, IBM MQ, Microsoft MQ
- Used in E-commerce, Risk Management, Market Analysis, and Monitoring network activities we use Kafka
- Producers and Consumers are automatically recovered
- Every Kafka server is a Bootstrap server like connected to one server which means your connected to all the servers
- Kafka client conects to Broker0 and requests a metadata requests


## Topics: 

- Its a stream of data
- Its identified by its name
- Any number of topics
- It can have any number of partitions and starts with 0
- Every message in the partition for a topic gets an incremental ID which is called Offset
- Every partition is independent to itself
- Data in topic remains for 7 days by default
- Data is immutable in Partition
- Data is assigned randomly to a partition


## Brokers:

- Broker is a server
- Its not valid to have 3 brokers in one server for a production
- We identify broker with a number like Broker0, Broker1 ....
- If we connect to one broker in a cluster then it automatically connects to all brokers in the same cluster

```
Topic A -> 3 Partitions
Topic B -> 2 Partitions

Broker0 Broker1 Broker2 

TopicA	TopicA	TopicA
Part.0	Part.2	Part.1
Part.4		Part.5

TopicB	TopicB
Part.0	Part.1
```

## Topic Replication Factor:

- `>1`
- Normal its 2
- 3 which is good
- If one broker is down then another broker can serve the request
- Replication factor =2 -> If Broker1 is down then topicA Part.0 and Part.1 are available in Broker0 and Broker2

```
Broker0 Broker1 Broker2 

TopicA	TopicA	TopicA
Part.0	Part.1	Part.1
	
	TopicA
	Part.0	
```


## Leader for Partition:

- Leader is elected by Zookeeper
- Anyone Partition in a topic for a broker can be leader which can receive and server data for the parition
- ISR (In Sync Replicas) in the Partition recieves the next data immediate from the leader 


## Producers:

- Write data to topics
- No programming required to explicit give
- It will do load balancing for the messages in Topics
- It will have acknowledgements (acks=0 - no waiting for acknowledgement which have chances for data loss) (acks=1 - default will wait for acknowledgement from the leader partition, limited data loss)
- It have message keys which means a message is sent along with key (num, string) which is sent to particular Broker1 - TopicA - Partition0 only which reduces the latency


## Consumers:

- Consumers read data from the topics
- One consumer can read data from multiple brokers
- Consumers read data in consumer groups which have consumers in it 


## Consumer Offsets:

- If a disconnect happens during the consumer down it commits the offset and tries to read messages from it when the consumer is back online
- Delivery symantics - At most once, At least once, Exactly once


## Zookeeper:

- Manage all the brokers
- Broker Leader elections are occured by help of Zookeeper
- It sends notifications if there is a change like a broker is not wokring or topic is deleted
- Kafka cant work without Zookeeper
- It operates in odd number of Servers (3 compulsory)
- Zookepers has leaders and followers





