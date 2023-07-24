## Config Options

We've just finished looking at the basics of structured streaming. Data comes in and is appended
to the input table when the trigger fires, then any actions get applied and added to the results
table then the output is generated.

Now that you've read from Kafka, written to Kafka, and created streams you have the context
to look at all the different config options.

### Offsets

You can specify a specific offset to read from when using Kafka. Maybe a system crashed
and you are restarting it and want to specify where it left off. You can even say start
there and end there.

You will have seen this first example when we created a Spark DataFrame from our streaming information:

```python

df = spark \
    .readStream \
    .format("kafka") \
    .option("kafka.bootstrap.servers", "kafka:9092") \
    .option("subscribe", "json_one") \
    .option("kafka.group.id", "myConsumerGroup") \
    .option("startingOffsets", "earliest") \
    .load() \
    .selectExpr("CAST(value AS STRING)")

# these are the options we have used already
.option("startingOffsets", "earliest")
.option("endingOffsets", "latest")
```

And these are custom versions picking up from specific points:

```python
.option("startingOffsets", """{"topic1":{"0":23,"1":-2},"topic2":{"0":-2}}""")
.option("endingOffsets", """{"topic1":{"0":50,"1":-1},"topic2":{"0":-1}}""")
```

The above are batch options since they `end`, where as a stream wouldn't, it would
keep listening.

without the `endingOffsets` option you can use as a stream.

### Sources

Spark can read from most things, it's extremely flexible.

- Files: Spark can read a directory of files, in date modified order. 
Supported file formats are text, CSV, JSON, ORC, Parquet.

- Kafka as we know

- Sockets, again this is just for testing as it isn't fault tolerant.

- Rate again for testing this can generate data for you (scala example):
```python
val df = spark.readStream.format("rate")
              .option("rowsPerSecond", 3).load()
```

- JDBC, again if your database has a driver Spark can connect to it.
```python
jdbcDF = spark.read \
    .format("jdbc") \
    .option("url", "jdbc:postgresql:dbserver") \
    .option("dbtable", "schema.tablename") \
    .option("user", "username") \
    .option("password", "password") \
    .load()

```

JDBC comes with many options itself, from `customSchemas` to lots of pushdown options, feel
free to have a google and look into it.

https://spark.apache.org/docs/latest/sql-data-sources-jdbc.html

### Streaming sinks

Above we looked at sources now let's look at sink. A sink is where data ends up.

Now the sinks are basically the same places you'd read from, but the output
options are the interesting bit.

In PySpark streaming, there are three output modes available for writing the results of a 
streaming query: "append", "complete", and "update". These output modes determine how the 
streaming DataFrame or Dataset is written to the output sink.

1. Append Mode:
    - In append mode, only the new rows added to the streaming DataFrame/Dataset since the 
last trigger will be written to the output sink.
    - This mode is suitable when the output sink is capable of handling only new data and 
does not require access to the previously processed data.
    - It is important to note that append mode assumes that the streaming query produces
only new rows and does not change the values of existing rows.

2. Complete Mode:
    - In complete mode, the entire updated result of the streaming DataFrame/Dataset is 
written to the output sink after each trigger.
    - This mode is suitable when the output sink needs to have access to the complete and
up-to-date result of the streaming query, including all the data processed so far.
    - It is important to note that using complete mode may require maintaining the entire 
state of the streaming query, which can have implications on memory usage and performance.

3. Update Mode:
    - In update mode, only the updated rows since the last trigger will be written to the 
output sink.
    - This mode is suitable when the output sink needs to receive only the changed data
, i.e., the updated rows, and does not require access to the complete result or the previous state.
    - Update mode assumes that the streaming query produces an update to the existing rows 
without adding or removing any rows.

To specify the output mode when writing a streaming DataFrame/Dataset, you can use the 
`outputMode()` method of the `DataStreamWriter` or `DataFrameWriter`. 
Here's an example of how to set the output mode:

```python
streamingDF.writeStream \
    .outputMode("append") \
    .format("console") \
    .start()
```

In this example, we set the output mode to "append" using the `outputMode()` method. 
The streaming DataFrame is then written to the console as the output sink.

It's important to choose the appropriate output mode based on your specific use case and 
the requirements of your output sink. Each mode has its own implications in terms of data 
handling, state maintenance, and performance.

Now there are `two` strange sinks to know about:

- `Foreach`
- `ForeachBatch`

They're very similar but have different use cases. Both allow you to apply logic to
the output of the stream. 

`Foreach` applies logic to each row in the output.

- foreachBatch: With foreachBatch you create a function with you parameters, one is
a DataFrame or Dataset that has the output data of a micro-batch and the unique ID 
of the micro-batch, then in the function you have the logic you want to apply.
```python
def foreach_batch_function(df, epoch_id):
    # Transform and write batchDF
    pass
  
streamingDF.writeStream.foreachBatch(foreach_batch_function).start()   
```

Imagine that the sink you are using doesn't have the a sink options such as:
```python
writeStream
    .format("parquet")        # can be "orc", "json", "csv", etc.
    .option("path", "path/to/destination/dir")
    .start()
```

You can use forEachBatch to create a custom writer yourself.

- Foreach: Has three sections and a function again.
Here's an example of how that looks because it does sound odd.

```python
class ForeachWriter:
    def open(self, partition_id, epoch_id):
        # Open connection. This method is optional in Python.
        pass

    def process(self, row):
        # Write row to connection. This method is NOT optional in Python.
        pass

    def close(self, error):
        # Close the connection. This method in optional in Python.
        pass
      
query = streamingDF.writeStream.foreach(ForeachWriter()).start()
```

So you'd create that then implement your logic yourself. You'd use this if you can't use
forEachBatch, you'd have to give your use case a test and read the driver docs 
you are using for your sinks. However you will most likely be able to connect with
``.format("kafka")`` or such like.



[Next Challenge](02_streaming_examples.md)

<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=04_streaming_continued%2F01_streaming_detailed.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=04_streaming_continued%2F01_streaming_detailed.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=04_streaming_continued%2F01_streaming_detailed.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=04_streaming_continued%2F01_streaming_detailed.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=04_streaming_continued%2F01_streaming_detailed.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
