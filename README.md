# SpringKafkaDemo

Running console based Kafka 
===========================
Cant do much withthis but helps to test the setup. Below is the step to run after installation-
.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties
.\bin\windows\kafka-server-start.bat .\config\server.properties
.\bin\windows\kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic promotion
.\bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test
.\bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test --from-beginning
--------------

Theory
-------------
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

Configurations(Important ones)
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




