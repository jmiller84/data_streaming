## Pub Sub


### Publish and Subscribe Messaging

In what is called pub sub messaging the sender of the data, is called the publisher,
and the data is referred to as the message.
The interesting part is that we don't send it directly to the receiver, we publish the message
to Kafka. To be specific we publish it to a topic on Kafka. Then the receiver subscribes to
topics they're interested in.
Then the subscriber doesn't poll or listen to the topic, when a new message arrives Kafka
sends the message to the subscriber.

### Publish and Subscribe Messaging in detail

1. Producer publishes a message:

- As it sounds the producer produces the message, it can be either application or 
system that creates a message and sends it to a Kafka broker.
- The message consists of a key (optional) and a value (mandatory) and is typically structured 
as a byte array or a string.

2. Kafka Broker receives the message:

- The Kafka broker is a server responsible for receiving and storing messages in topics.
Upon receiving a message, the broker appends it to the commit log (a durable, distributed, and ordered log).

3. Consumer subscribes to the topic:

- Consumers interested in consuming messages from a specific topic join a consumer group.
- The consumer group identifies the specific topic or topics it wants to consume from.

4. Consumer consumes messages:

- The assigned consumer within the consumer group starts pulling messages from its assigned partition.
- The consumer keeps track of the offset, which represents the position of the last consumed message.

5. Message Delivery:

- Kafka delivers messages to consumers based on the offset.
- Consumers pull batches of messages from the broker, starting from their last consumed 
offset to the latest available offset.

6. Processing and Acknowledgement:

- Consumers process the received messages according to their application logic.
- Once a consumer successfully processes a message, it acknowledges the broker by committing the offset.

7. Message Retention and Cleanup:

- Kafka retains messages in the commit log for a configurable retention period.

### Recap

To simplify, you send data to a central location, which is a topic in a Kafka cluster.

Then whoever needs it can subscribe to that topic and read data from it. When data is read from a topic it stays on there and isn't deleted. 

Kafka keeps track of where you have read up to, this is called the offset.





[Next Challenge](03_data.md)

<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F02_pub_sub.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F02_pub_sub.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F02_pub_sub.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F02_pub_sub.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=02_kafka%2F02_pub_sub.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
