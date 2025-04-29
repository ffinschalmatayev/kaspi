---
order: 1
title: docker-compose.yaml
---

Этот файл используется для настройки и запуска сервисов с помощью Docker Compose. Он содержит конфигурацию для запуска Zookeeper и Kafka.

```java
version: '3.8'
services:
  zookeeper:
    image: 'confluentinc/cp-zookeeper:7.6.0'
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
  kafka:
    image: 'confluentinc/cp-kafka:7.6.0'
    depends_on: [zookeeper]
    environment:
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    ports:
      - '9092:9092'
```

#### \#Описание:

-  Сервисы:

   -  **zookeeper**: Настроен для работы с портом 2181.

   -  **kafka**: Зависит от Zookeeper, использует порты для связи с Kafka (порт 9092).

Этот файл поможет поднять локальную среду для работы с Kafka и Zookeeper, используя Docker.