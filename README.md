# run-kafka-docker
Start a kafka Service:

docker pull spotify/kafka
docker run -d -p 2181:2181 -p 9092:9092 --env ADVERTISED_HOST=kafka --env ADVERTISED_PORT=9092 --name kafka spotify/kafka

##ADVERTISTED_HOST was set to kafka, which will allow other containers to be able to run Producers and Consumers.

##Setting ADVERTISED_HOST to localhost, 127.0.0.1, or 0.0.0.0 will work great only if Producers and Consumers are started within the kafka container itself, or if you are using DockerForMac (like me) and you want to run Producers and Consumers from OSX. These are far less interesting use cases though, so we'll start Producers and Consumers from other containers.

##We need to use an IP address or hostname in order for the kafka service to be reachable from another container. IP address is not known before the container is started, so we have to choose a hostname, and I chose kafka in this example.

Create a topic
docker exec kafka /opt/kafka_2.11-0.10.1.0/bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test

List topics
docker exec kafka /opt/kafka_2.11-0.10.1.0/bin/kafka-topics.sh --list --zookeeper localhost:2181

Start a producer (in a new terminal window)
docker run -it --rm --link kafka spotify/kafka /opt/kafka_2.11-0.10.1.0/bin/kafka-console-producer.sh --broker-list kafka:9092 --topic test

Start a consumer (in a new terminal window)
docker run -it --rm --link kafka spotify/kafka /opt/kafka_2.11-0.10.1.0/bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic test --from-beginning
