Running console based Kafka 
===========================
.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties
.\bin\windows\kafka-server-start.bat .\config\server.properties
.\bin\windows\kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic promotion
.\bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test
.\bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test --from-beginning

======================
Theory
--------
Kafka Server = Kafka Broker
Kafka Cluster - Multiple Kafka Servers co-ordinated through Zookeeper.
Topic can be devided into multiple Partition
Each Partition can run on different machine.
A partition cant be devided further into mutiple machine.
Each Partition has offset.

Replication factor is associated with Topic.
For a given Topic, one broker is nominated a leader. Only leader talks to Producer and Consumer for that Topic and sends acknowledgement to producer. 
Leader maintains first copy of replication, followers maintain rest.

Sending message requires properties - bootstrap.servers, serializer(to convert object into byte stream)
3 approach for sending - fire and forget, wait till acknowledgement(Future.get), async.
Serialization - Use default or Custom. When message object changes, need to update custom serializer accordingly. 
	But then old message cant be read with new Serializer. Avro helps in these situations.

If order is important then there are two ways to achieve it-
	1. Use Synchronous send
	2. set max.in.flight.request.per.connection = 1
	Ofcourse, then guaranty comes at the cost of throughput.

Consumer - There is a construct consumer group which are set of related consumers working together to achieve high throughput. 
	A consumer is assigned to a partition. Only single consumer can be assigned to partition.
	One of the Kafka broker is made Group co-ordinator. Group co-ordinator maintains list of consumers and initiates a rebalancing when a member enter/exit.
	First consumer of the group becomes the group leader, performs the rebalancing when a member enters/exits.
	One cosumer can subscribe to list of topics.
	Consumer polls the topic in infinite loop. Optionally it can be given a scheduler so that it pools based on sheduler settings.
	
Offset
	Current Offset - Maintains current position of consumer read.
	Commited ofset - Consumer has confirmed about processing.
	These offsets play a role in rebalancing

Common Configurations
--------------
broker.id - Unique identifier of broker.
port - Port on which broker runs.
log.dirs - the path where logs of broker will be written.
zookeeper.connect - using this broker knows where is zookeeper running.
delete.topic.enable
autocreate.topic.enable
default.replication.factor
num.partition
log.retention.ms - How long to keep message(Default 7 days)
log.retention.bytes - Max capacity of size of message a partition will hold. Beyond that, Kafka will do the cleanup.

Producer specific configuration
--------------------------------
ack - Acknowledgment is required from broker to producer. If set to all, then the leader broker will confirm from all 
	the follower broker whenther message was successfully received, then only send the acknowledgement message.(slowest)
retries
max.in.flight.request.per.connection - how many in flight request is allowed that are still not acknowledged.
