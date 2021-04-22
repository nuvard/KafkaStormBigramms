
This module is build on top of standart storm-kafka-client spout and Trident usage. Kafka is Producer of data for this topology, and Storm gives bolts for splitting sentences, collect and count bigramms in text, saving result in Redis. 

## Usage
Start ZooKeeper from its directory
```./bin/zkServer.sh start```
 You need to set up config of Storm (storm.yaml) as:

```
 storm.zookeeper.servers:
 - "localhost"
 nimbus.seeds: ["localhost"]
 ui.port: 8082
 ```
 
 In different terminal windows start all needed Storm services.
 
```
./bin/storm nimbus
./bin/storm supervisor
./bin/storm ui

```
Now you are able to examine topologies in UI at `localhost:8082`

Start Kafka server from Kafka directory with default config
```
bin/kafka-server-start.sh config/server.properties
```

Create topic bigram-topic
```
bin/kafka-topics.sh --create --topic bigram-topic --bootstrap-server localhost:9092 
```
Build and submit topology (all patch from examples/storm-kafka-client-examples)
```
mvn clean package -Dstorm.kafka.client.version=2.7.0

../../bin/storm jar target/storm-kafka-client-examples-2.2.0.jar org.apache.storm.kafka.spout.KafkaRedisBigramCountTopology bigrams
```
Now run producer with some text, for example, with file input
```
bin/kafka-console-producer.sh --topic bigram-topic --bootstrap-server localhost:9092 < ../guards.txt
```
After all, you can check result in Redis
```
redis-cli

hgetall wordCountHashSet
```

