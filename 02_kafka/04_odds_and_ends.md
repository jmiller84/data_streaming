## Schema registry

Schema registry, are crucial for managing the schema evolution and compatibility of 
data produced and consumed by Kafka messages. The schema registry is typically used
in conjunction with Apache AVRO, a binary serialisation format, to provide schema 
evolution capabilities and ensure compatibility between producers and consumers.
Note the below examples aren't for following I just thought I'd add them in so you
can gain more exposure to Kafka.

1. Schema Registry Basics:
    - The schema registry is a separate service that runs alongside Kafka and is responsible
for managing schemas.
    - It stores AVRO schemas in a central repository and assigns a unique identifier 
(schema ID) to each schema.
    - Producers and consumers use the schema registry to retrieve and register schemas.

2. Installing Dependencies:
    - To work with the schema registry in Python, you'll need to install the `confluent-kafka`
library, which provides a Python client for interacting with Kafka and the schema registry.
    - You can install it using pip: `pip install confluent-kafka`.

3. Producing Messages with Schema Registry:
    - To produce AVRO encoded messages with the schema registry, you need to define 
your AVRO schema and register it in the registry.
    - Here's an example of producing a message with a registered schema:

```python
from confluent_kafka import avro
from confluent_kafka.avro import AVROProducer

# Define the AVRO schema
schema = """
{
  "type": "record",
  "name": "MyRecord",
  "fields": [
    {"name": "id", "type": "int"},
    {"name": "name", "type": "string"}
  ]
}
"""

# Create an AVROProducer with the schema registry configuration
producer = AVROProducer(
    {
        "bootstrap.servers": "kafka:9092",
        "schema.registry.url": "http://kafka:8081",
    },
    default_value_schema=avro.loads(schema),
)

# Produce a message with the schema
message = {"id": 1, "name": "John Doe"}
producer.produce(topic="my_topic", value=message)
producer.flush()
```

4. Consuming Messages with Schema Registry:
    - To consume AVRO encoded messages with the schema registry, you need to configure 
the consumer to use the schema registry and provide the schema ID.
    - Here's an example of consuming messages with a registered schema:

```python
from confluent_kafka import avro
from confluent_kafka.avro import AVROConsumer

# Create an AVROConsumer with the schema registry configuration
consumer = AVROConsumer(
    {
        "bootstrap.servers": "kafka:9092",
        "group.id": "my_consumer_group",
        "schema.registry.url": "http://kafka:8081",
    }
)

# Subscribe to the topic
consumer.subscribe(["my_topic"])

# Consume messages with the registered schema
while True:
    message = consumer.poll(1.0)
    if message is None:
        continue
    if message.error():
        print(f"Error: {message.error()}")
        continue

    # Get the AVRO-decoded message value
    value = message.value()

    # Access the fields in the value
    id_value = value["id"]
    name_value = value["name"]

    print(f"Received message - ID: {id_value}, Name: {name_value}")

consumer.close()
```

These examples demonstrate how to produce and consume AVRO encoded messages using the 
schema registry in Python. Ensure that you have a running Kafka cluster and a schema 
registry configured to run the code successfully.

### Workflow order

Define AVRO Schema: Start by defining your AVRO schema for the data you want to 
produce or consume. The schema describes the structure and data types of the messages.

Register Schema: Register the AVRO schema in the schema registry. This involves 
sending the schema to the registry, which will assign a unique schema ID to it. 
The schema is then stored in the registry for future reference.

Produce Messages: When producing messages, you encode your data using the AVRO 
schema and send it to Kafka. The producer includes the schema ID in the message
headers or payload, indicating the schema to be used for deserialisation by consumers.

Consume Messages: When consuming messages, the consumer retrieves the messages 
from Kafka and checks the schema ID associated with each message. It then retrieves
the corresponding AVRO schema from the schema registry based on the schema ID.

Deserialise Data: Using the retrieved AVRO schema, the consumer deserialises the 
message payload, decoding it into the original data format based on the schema.

The idea being you can change the data you keep in Kafka, slightly, and the many
(could be hundreds)  of users don't have to get an email from you describe
what to do and then have to go edit their code. they should be able to read it automatically.

[Next Challenge](05_topics.md)

<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F04_odds_and_ends.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F04_odds_and_ends.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F04_odds_and_ends.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F04_odds_and_ends.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F04_odds_and_ends.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
