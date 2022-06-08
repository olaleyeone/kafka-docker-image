# kafka docker image

## Download kafka
`curl "https://dlcdn.apache.org/kafka/3.2.0/kafka_2.13-3.2.0.tgz" > kafka_2.13-3.2.0.tgz`

## Build docker image
`docker build . -f Dockerfile-3.2.0 -t olaleye/kafka:3.2.0`

## use image for zookeeper and kafka
```
version: "3.8"

services:

  zookeeper:
    image: olaleye/kafka:3.2.0
    restart: unless-stopped
    entrypoint: bin/zookeeper-server-start.sh config/zookeeper.properties
    volumes:
      - ./server/data/zookeeper:/tmp/zookeeper

  kafka:
    image: olaleye/kafka:3.2.0
    restart: unless-stopped
    command: >
      --override broker.id=1 
      --override zookeeper.connect=zookeeper:2181
      --override listeners=INTERNAL://kafka:9000,EXTERNAL://:9092 
      --override advertised.listeners=INTERNAL://kafka:9000,EXTERNAL://kafka:9092
      --override inter.broker.listener.name=INTERNAL 
      --override listener.security.protocol.map=INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT
    environment:
      KAFKA_HEAP_OPTS: "-Xmx256M -Xms128M"
    volumes:
      - ./server/data/kafka-logs:/tmp/kafka-logs
    depends_on:
      - zookeeper
```