## Schema registry

Schema registry, are crucial for managing the schema evolution and compatibility of 
data produced and consumed by Kafka messages. The schema registry is typically used
in conjunction with Apache AVRO, a binary serialisation format, to provide schema 
evolution capabilities and ensure compatibility between producers and consumers.
Note the below examples aren't for following I just thought I'd add them in so you
can gain more exposure to Kafka.

Setting up Avro, Binary encoding and decoding and serialisation is probably overkill for such a small message, or dataset that we are sending - it is good to see as an example, but bear in mind that a real world scenario here may opt for AVRO with Schema Registry when the data gets into the terabyte range or larger, due to the speed of reading/writing efficiency compared to something like JSON.

This is meant more as an example of what it might look like, and not a practical example.

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
    - To produce AVRO encoded messages with a schema registry, you need to define your AVRO schema and register it in the registry.
    - Here's an example of producing a message with a registered schema:

**N.B. This example has been moved to the folder `avro_schema_registry_example` found [here](../avro_schema_registry_example/avro_shema_registry.ipynb). You will need to pull down the all of the tools specified in the `docker-compose.yml`. It will take a while, and there are a lot of them. This step is 100% entirely optional to do, as the amount of connecting, downloading, and set-up - is incredibly underwhelming to send a two line AVRO message. It has been included for completeness as Schema Registry is widely used on larger systems.**


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
