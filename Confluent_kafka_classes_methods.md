
Here are the main methods of Confluent Kafka Python client classes:

## 1. **Producer Class**

```python
from confluent_kafka import Producer

# Initialization
producer = Producer(config_dict)

# Core Methods
producer.produce(topic, value=None, key=None, partition=-1, on_delivery=None, 
                timestamp=0, headers=None)
producer.poll(timeout=-1)  # Trigger callbacks, return number of events
producer.flush(timeout=-1)  # Wait for all messages to be delivered
producer.purge(in_queue=True, in_flight=True, blocking=True)  # Purge messages

# Metadata & Info
producer.list_topics(topic=None, timeout=-1)  # Get cluster metadata
producer.init_transactions(timeout=-1)  # Initialize transactions
producer.begin_transaction()  # Begin transaction
producer.send_offsets_to_transaction(positions, group_metadata, timeout=-1)
producer.commit_transaction(timeout=-1)  # Commit transaction
producer.abort_transaction(timeout=-1)  # Abort transaction

# Other
producer.len()  # Number of messages in queue
```

## 2. **Consumer Class**

```python
from confluent_kafka import Consumer

# Initialization
consumer = Consumer(config_dict)

# Subscription & Assignment
consumer.subscribe(topics, on_assign=None, on_revoke=None, on_lost=None)
consumer.unsubscribe()
consumer.assign(partitions)  # Manual partition assignment
consumer.unassign()
consumer.assignment()  # Get current partition assignment
consumer.subscription()  # Get current topic subscription

# Consuming Messages
consumer.poll(timeout=-1)  # Poll for messages, returns Message object
consumer.consume(num_messages=1, timeout=-1)  # Consume multiple messages

# Offset Management
consumer.commit(message=None, offsets=None, asynchronous=True)
consumer.committed(partitions, timeout=-1)  # Get committed offsets
consumer.position(partitions)  # Get current position
consumer.seek(partition)  # Seek to specific offset
consumer.store_offsets(message=None, offsets=None)  # Store offset

# Offset Queries
consumer.get_watermark_offsets(partition, timeout=-1, cached=False)
consumer.offsets_for_times(partitions, timeout=-1)

# Pause/Resume
consumer.pause(partitions)
consumer.resume(partitions)

# Group Management
consumer.memberid()  # Get consumer member ID
consumer.consumer_group_metadata()  # Get consumer group metadata

# Metadata
consumer.list_topics(topic=None, timeout=-1)

# Cleanup
consumer.close()  # Close consumer and leave group
```

## 3. **AdminClient Class**

```python
from confluent_kafka.admin import AdminClient, NewTopic, NewPartitions, ConfigResource

# Initialization
admin_client = AdminClient(config_dict)

# Topic Management
admin_client.create_topics(new_topics, operation_timeout=None, 
                          request_timeout=None, validate_only=False)
admin_client.delete_topics(topics, operation_timeout=None, request_timeout=None)
admin_client.create_partitions(new_partitions, operation_timeout=None, 
                               request_timeout=None, validate_only=False)

# Configuration Management
admin_client.describe_configs(resources, request_timeout=None)
admin_client.alter_configs(resources, request_timeout=None, validate_only=False)
admin_client.incremental_alter_configs(resources, request_timeout=None, 
                                       validate_only=False)

# Consumer Group Management
admin_client.list_consumer_groups(request_timeout=None)
admin_client.describe_consumer_groups(group_ids, request_timeout=None)
admin_client.delete_consumer_groups(group_ids, request_timeout=None)
admin_client.list_consumer_group_offsets(group_id, partitions=None, 
                                         request_timeout=None)
admin_client.alter_consumer_group_offsets(group_id, topic_partitions, 
                                          request_timeout=None)

# ACL Management
admin_client.create_acls(acls, request_timeout=None)
admin_client.describe_acls(acl_binding_filter, request_timeout=None)
admin_client.delete_acls(acl_binding_filters, request_timeout=None)

# Cluster Metadata
admin_client.list_topics(topic=None, timeout=-1)
admin_client.describe_cluster(request_timeout=None)

# User SCRAM Credentials
admin_client.describe_user_scram_credentials(users=None, request_timeout=None)
admin_client.alter_user_scram_credentials(alterations, request_timeout=None)
```

## 4. **Message Class**

```python
from confluent_kafka import Message

# Properties (read-only)
message.topic()  # Topic name
message.partition()  # Partition number
message.offset()  # Message offset
message.key()  # Message key
message.value()  # Message value
message.headers()  # Message headers (list of tuples)
message.timestamp()  # Returns (type, timestamp)
message.latency()  # Producer latency in seconds
message.leader_epoch()  # Leader epoch
message.error()  # KafkaError if error occurred
message.len()  # Message value length
message.set_key(key)  # Set message key
message.set_value(value)  # Set message value
message.set_headers(headers)  # Set headers
```

## 5. **TopicPartition Class**

```python
from confluent_kafka import TopicPartition

# Initialization
tp = TopicPartition(topic, partition=None, offset=None, metadata=None, 
                   leader_epoch=None)

# Properties
tp.topic  # Topic name
tp.partition  # Partition number
tp.offset  # Offset
tp.metadata  # Metadata string
tp.error  # KafkaError
tp.leader_epoch  # Leader epoch
```

## 6. **KafkaError Class**

```python
from confluent_kafka import KafkaError

# Properties
error.code()  # Error code
error.name()  # Error name
error.str()  # Error string
error.fatal()  # Is fatal error
error.retriable()  # Is retriable error
error.txn_requires_abort()  # Transaction requires abort
```

## 7. **SerializingProducer / DeserializingConsumer**

```python
from confluent_kafka import SerializingProducer, DeserializingConsumer
from confluent_kafka.serialization import StringSerializer, StringDeserializer

# SerializingProducer (same as Producer + serialization)
serializing_producer = SerializingProducer({
    'bootstrap.servers': 'localhost:9092',
    'key.serializer': StringSerializer('utf_8'),
    'value.serializer': StringSerializer('utf_8')
})

# Methods same as Producer
serializing_producer.produce(topic, key=key, value=value)
serializing_producer.flush()

# DeserializingConsumer (same as Consumer + deserialization)
deserializing_consumer = DeserializingConsumer({
    'bootstrap.servers': 'localhost:9092',
    'group.id': 'mygroup',
    'key.deserializer': StringDeserializer('utf_8'),
    'value.deserializer': StringDeserializer('utf_8')
})

# Methods same as Consumer
deserializing_consumer.subscribe(['topic'])
message = deserializing_consumer.poll()
```

## 8. **Avro Serializers (Schema Registry)**

```python
from confluent_kafka.schema_registry import SchemaRegistryClient
from confluent_kafka.schema_registry.avro import AvroSerializer, AvroDeserializer

# SchemaRegistryClient
sr_client = SchemaRegistryClient({'url': 'http://localhost:8081'})

sr_client.register_schema(subject_name, schema, normalize_schemas=False)
sr_client.get_schema(schema_id)
sr_client.get_latest_version(subject_name)
sr_client.get_subjects()
sr_client.delete_subject(subject_name, permanent=False)
sr_client.get_versions(subject_name)
sr_client.delete_version(subject_name, version)
sr_client.set_compatibility(level, subject_name=None)
sr_client.get_compatibility(subject_name=None)

# AvroSerializer
avro_serializer = AvroSerializer(schema_registry_client, schema_str, 
                                 to_dict=None, conf=None)

# AvroDeserializer  
avro_deserializer = AvroDeserializer(schema_registry_client, schema_str=None, 
                                     from_dict=None)
```

## Common Configuration Parameters

```python
# Producer Config
config = {
    'bootstrap.servers': 'localhost:9092',
    'client.id': 'my-producer',
    'acks': 'all',  # 0, 1, 'all'
    'retries': 3,
    'compression.type': 'gzip',  # none, gzip, snappy, lz4, zstd
    'batch.size': 16384,
    'linger.ms': 10,
    'enable.idempotence': True,
    'transactional.id': 'my-transactional-id',
}

# Consumer Config
config = {
    'bootstrap.servers': 'localhost:9092',
    'group.id': 'my-group',
    'client.id': 'my-consumer',
    'auto.offset.reset': 'earliest',  # earliest, latest, none
    'enable.auto.commit': True,
    'auto.commit.interval.ms': 5000,
    'session.timeout.ms': 10000,
    'max.poll.interval.ms': 300000,
    'isolation.level': 'read_committed',  # read_uncommitted, read_committed
}
```

## Example Usage

```python
from confluent_kafka import Producer, Consumer, KafkaError

# Producer Example
def delivery_callback(err, msg):
    if err:
        print(f'Delivery failed: {err}')
    else:
        print(f'Message delivered to {msg.topic()} [{msg.partition()}]')

producer = Producer({'bootstrap.servers': 'localhost:9092'})
producer.produce('my-topic', key='key', value='value', callback=delivery_callback)
producer.flush()

# Consumer Example
consumer = Consumer({
    'bootstrap.servers': 'localhost:9092',
    'group.id': 'my-group',
    'auto.offset.reset': 'earliest'
})

consumer.subscribe(['my-topic'])

try:
    while True:
        msg = consumer.poll(1.0)
        if msg is None:
            continue
        if msg.error():
            print(f'Consumer error: {msg.error()}')
            continue
        print(f'Received: {msg.value().decode("utf-8")}')
except KeyboardInterrupt:
    pass
finally:
    consumer.close()
```

These are the main methods available in Confluent Kafka Python client. For complete details, refer to the [official documentation](https://docs.confluent.io/kafka-clients/python/current/overview.html).
