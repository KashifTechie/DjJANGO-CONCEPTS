version: "3.9"

services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.6.1
    container_name: zookeeper
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
    ports:
      - "2181:2181"
    networks:
      - devcontainer_network
    healthcheck:
      test: ["CMD", "echo ruok | nc localhost 2181"]
      interval: 5s
      retries: 5

  kafka:
    image: confluentinc/cp-kafka:7.6.1
    depends_on:
      - zookeeper
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: "zookeeper:2181"
      KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9092
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: "true"
    networks:
      - devcontainer_network

  clickhouse:
    image: clickhouse/clickhouse-server:latest
    container_name: clickhouse
    ports:
      - "8123:8123"
      - "9000:9000"
    volumes:
      - clickhouse_data:/var/lib/clickhouse
    environment:
      CLICKHOUSE_DB: leomail
      CLICKHOUSE_USER: cocoon
      CLICKHOUSE_PASSWORD: root

  leomail-redis:
    container_name: leomail-redis
    restart: unless-stopped
    image: redis:7.0.5-alpine
    ports:
      - "6378:6379"
    networks:
      - devcontainer_network

volumes:
  clickhouse_data:
    driver: local

networks:
  devcontainer_network:
    external: true
