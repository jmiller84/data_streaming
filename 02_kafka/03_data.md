## Producer

This will probably be the simplest section conceptually, as we are just sending
data to a topic - but with certain structures like AVRO or ProtoBuf, they can be a little trickier to encode and decode. Essentially, we are still just sending some data to a topic. That's it. Now in the real world it can get more much complex as there are things calls schema registries which we'll touch on briefly for completeness. Otherwise, we are going to have fun writing some python that sends our message to Kafka.

### Producing Data

```python
from kafka import KafkaProducer

# Kafka broker address
bootstrap_servers = 'kafka:9092'

# Create a Kafka producer instance
producer = KafkaProducer(
    bootstrap_servers=bootstrap_servers,
    key_serializer=str.encode,  # Key serializer
    value_serializer=str.encode,  # Value serializer
)

# Topic to send messages to
topic = 'example_topic'

# Send messages with keys
for i in range(0,4):
    producer.send(topic, key="{}".format(i), value="message number {}".format(i))
    print(f"Sent message: Key={i}, Value=message number {i}")

# Close the producer
producer.close()

```

We should see the following output if our producer sent the messages correctly:
``` bash 
Sent message: Key=0, Value=message number 0
Sent message: Key=1, Value=message number 1
Sent message: Key=2, Value=message number 2
Sent message: Key=3, Value=message number 3 
```

As you can see it's pretty simple to create a producer and send a few messages.

### Consuming Data

```python
from kafka import KafkaConsumer

# Create a Kafka consumer instance
consumer = KafkaConsumer(
    'example_topic',
    bootstrap_servers='kafka:9092',
    group_id='my_consumer_group',
    auto_offset_reset='earliest'
)

# Continuously poll for new messages
for message in consumer:
    # Decode the message value assuming it's in bytes
    message_value = message.value.decode('utf-8')
    print(message_value)

# Close the consumer connection
consumer.close()
```

Now when you run the above there can expect a delay since we are running, 
on our local laptops, you can even see at the bottom of Jupyter notebook
in the task bar it says busy, once it prints your message press the 
stop button at the top of Jupyter so it doesn't sit there waiting 
holding up the process. If things are still taking too long you 
can press the restart kernel button at the top.

### Message Types

Kafka supports different payloads, let's have a look. Copy these examples into cells *above* your `Consumer` cell if you want to run the Kafka Consumer to see the output.

- String is what we've been using recently and with all messages you can send
with or without a key. 

- JSON is another type and very often used, here is an example:
```python
import json
from kafka import KafkaProducer

# Kafka broker address
bootstrap_servers = 'kafka:9092'

producer = KafkaProducer(bootstrap_servers=bootstrap_servers,
value_serializer=lambda v: json.dumps(v).encode('utf-8'))


# Topic to send messages to
topic = 'example_topic'

# Create a Python dictionary representing your message
message = {
'id': 1,
'name': 'John Doe',
'age': 30
}

# Send the message in JSON format
producer.send(topic, value=message)

# Close the producer
producer.close()
```

**(Copy/Move then Run the Consumer cell after this example to see the JSON message!)**

- AVRO is another format popular for being quick to write with and you can
define a schema, it's less popular as a format for querying, generally
more used for writing, here is an example over multiple cells:

**Cell One - Topic**
```python
from kafka.admin import KafkaAdminClient, NewTopic


admin_client = KafkaAdminClient(
    bootstrap_servers="kafka:9092", 
    client_id='test'
)

topic_list = []
topic_list.append(NewTopic(name="avro_topic_test", num_partitions=1, replication_factor=1))
admin_client.create_topics(new_topics=topic_list, validate_only=False)
```

**Cell Two - Producer**
```python
from kafka import KafkaProducer
from avro import schema, io
import avro.schema
import avro.io 
from io import BytesIO

# Configure Kafka producer
bootstrap_servers = 'kafka:9092'  # Update with your Kafka broker address
topic = 'avro_topic_test'

# Load Avro schema from a file or define it programmatically
avro_schema = avro.schema.parse(open('./work/data/user.avsc', 'rb').read())


# Create a Python dictionary representing your message
message = {
    'first_name': 'John',
    'last_name': 'Doe',
    'age': 30
}

producer = KafkaProducer(
    bootstrap_servers=bootstrap_servers,
    # key_serializer=str.encode,  # Key serializer
    # value_serializer=str.encode,  # Value serializer
)

avro_bytes_writer = BytesIO()
avro_writer = io.DatumWriter(avro_schema)
encoder = io.BinaryEncoder(avro_bytes_writer)
avro_writer.write(message, encoder)
avro_bytes = avro_bytes_writer.getvalue()

# Send the Avro message to Kafka topic
producer.send(topic=topic, value=avro_bytes)
```

**Cell Three - Consumer**
```python
from kafka import KafkaConsumer
from avro.io import DatumReader, BinaryDecoder
from io import BytesIO
import avro.schema
import io

# Create a Kafka consumer instance
consumer = KafkaConsumer(
    'avro_topic_test',
    bootstrap_servers='kafka:9092',
    group_id='tester',
    auto_offset_reset='earliest'
)

avro_schema = avro.schema.parse(open('./work/data/user.avsc', 'rb').read())

for message in consumer:
    # Process the Avro message
    avro_reader = DatumReader(avro_schema)
    avro_bytes_reader = BytesIO(message.value)
    decoder = BinaryDecoder(avro_bytes_reader)
    decoded_message = avro_reader.read(decoder)
    print("working")
    # print(message)
    print(decoded_message)
```

- ProtoBuf: ProtoBuf (Protocol Buffers) is a language-agnostic binary 
serialisation format developed by Google. 
It allows for efficient serialisation and deserialisation of structured data.
I don't often see this used, but here's an example to read:

```python
from kafka import KafkaProducer
import my_protobuf_pb2

# Configure Kafka producer
bootstrap_servers = 'localhost:9092'  # Update with your Kafka broker address
topic = 'my_topic'

# Create Kafka producer instance
producer = KafkaProducer(bootstrap_servers=bootstrap_servers,
                         value_serializer=lambda v: v.SerializeToString())

# Create a Protobuf message using your defined schema
message = my_protobuf_pb2.MyMessage()
message.id = 1
message.name = 'John Doe'
message.age = 30

# Send the message in Protobuf format
producer.send(topic, value=message)

```

The key take away and the biggest thing to remember is basically all you send is
a key and a value. If you want to send the data, name, address and postcode, they'll
all go in a field called value, then when you read it you will have to parse the values
back out again. Sometimes the key that goes with the value will have a code representing
the schema and tell you how to read it, or what to expect in the value field.
There aren't in hard and fast rules, just you really only have the value field to 
work with.


[Next Challenge](04_odds_and_ends.md)

<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F03_data.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F03_data.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F03_data.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F03_data.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F03_data.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
