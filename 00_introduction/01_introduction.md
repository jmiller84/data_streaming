## Introduction to the Module

In data engineering we sometimes forget how much data is actually streamed. We tend to
think of daily jobs running in the morning, moving data at rest. 
However most of the time the data probably arrived as a stream, any time you are dealing 
with a website, an API, phone apps the data is probably event based, one user, one message 
coming in as part of a stream. With how universally used Kafka is as a technology
people are taking their batch data and sticking it in Kafka, then it turns into a stream.

This module will cover Spark, Kafka for the most part and is quite technical, but we'll try to 
have fun with the data we use and make things interesting.

### Real World Application

It should be noted that throughout this module we will be attempting to run Kafka and Spark locally, by using Docker. However this is not the intended, or indeed desired application of Kafka and event-driven architecture. It is an example procedure, in order for us to be better able to understand how these technologies work, but it should be clearly understood that in real world examples these technologies are - by design - used over multiple networks, servers, computers and machines. 

In real world applications of event-driven architecture, it is almost the entire point that a company or a team is able to develop products and features across the data and software space that is massively scalable. Scalability is the key here, and as such - a local version of these technologies is somewhat redundant in practice, outside of a learning exercise.

[Next Challenge](02_docker_set_up.md)

<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=00_introduction%2F01_introduction.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=00_introduction%2F01_introduction.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=00_introduction%2F01_introduction.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=00_introduction%2F01_introduction.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=00_introduction%2F01_introduction.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
