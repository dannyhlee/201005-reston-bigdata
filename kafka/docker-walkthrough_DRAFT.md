pache sample at https://kafka.apache.org/quickstart-docker is garbage.

The kafka docker image - https://github.com/wurstmeister/kafka-docker#kafka-docker
The intro to follow - https://jaceklaskowski.gitbooks.io/apache-kafka/content/kafka-docker.html
Microservices, Docker, Kubernetes by James Quigley https://www.youtube.com/watch?v=1xo-0gCVhTU


### Set up Docker on local machine
1. Download and install Docker desktop https://covid.ourworldindata.org/data/owid-covid-data.csv
2. Signup for hub.docker.com account (remember password to login in Docker Desktop)
3. Clone `https://github.com/wurstmeister/kafka-docker`
4. Run `docker-compose up`
5. Open a new terminal window
6. get the contaner names: 
```
$ docker ps --format "{{.Names}}"
kafka-docker_kafka_1
kafka-docker_zookeeper_1
```
7. Get the port number: 
```
$ docker inspect --format='{{range $k, $v := .NetworkSettings.Ports}}{{range $v}}{{$k}} -> {{.HostIp}} {{.HostPort}}{{end}}{{end}}' kafka-docker_kafka_1

9092/tcp -> 0.0.0.0 32771
```
8. Check connection to single kafka broker
```
$ nc -vz 0.0.0.0 32771
Connection to 0.0.0.0 32771 port [tcp/*] succeeded!
```

// Print out the topics
// You should see no topics listed
$ docker exec -t kafka-docker_kafka_1 \
  kafka-topics.sh \
    --bootstrap-server :9092 \
    --list

// Create a topic t1
$ docker exec -t kafka-docker_kafka_1 \
  kafka-topics.sh \
    --bootstrap-server :9092 \
    --create \
    --topic t1 \
    --partitions 3 \
    --replication-factor 1

// Describe topic t1
$ docker exec -t kafka-docker_kafka_1 \
  kafka-topics.sh \
    --bootstrap-server :9092 \
    --describe \
    --topic t1
Topic:t1    PartitionCount:3    ReplicationFactor:1 Configs:segment.bytes=1073741824
    Topic: t1   Partition: 0    Leader: 1001    Replicas: 1001  Isr: 1001
    Topic: t1   Partition: 1    Leader: 1001    Replicas: 1001  Isr: 1001
    Topic: t1   Partition: 2    Leader: 1001    Replicas: 1001  Isr: 1001

// Connect with the Kafka console consumer in another terminal
$ docker exec -t kafka-docker_kafka_1 \
  kafka-console-consumer.sh \
    --bootstrap-server :9092 \
    --group jacek-japila-pl \
    --topic t1

// Connect with the Kafka console producer in one terminal
$ docker exec -it kafka-docker_kafka_1 \
  kafka-console-producer.sh \
    --broker-list :9092 \
    --topic t1

// Type a message in producer window to see the message printed out in consumer's
// Observe the logs of the running docker-compose up (with no -d)
// Or use docker logs -f kafka-docker_kafka_1

// Ctrl-C to shut down all the processes

// You may also consider removing the containers
// so you always start afresh
// docker-compose rm

