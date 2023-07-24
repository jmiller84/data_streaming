## Let's just JSON

At the moment, you've done some Python, some PySpark, some streaming, but let's simplify things for now.
You can't overstate it enough software is complex so any time you can just decide something,
stick with it and worry about other complexities it's a good thing.
For example, you saw this line:

```python
df = df.selectExpr("CAST(value AS STRING) as Value")
```

Reading the content of a Kafka message, this is fine when it might contain a single value.
From now on, lets use JSON in the value field and when we read data, be ready to
parse JSON into a DataFrame we can use. Then we just have to worry about other complexities
like windowing and `.forEachBatch()`. (joking, kind of)


```python
import json
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, StringType, IntegerType
from pyspark.sql.functions import lit

# Create a dictionary representing the JSON object
data = {
    "name": "John Doe",
    "age": 30,
    "city": "New York"
}

# Serialize the dictionary to a JSON string
json_string = json.dumps(data)

# Print the JSON string
print(json_string)

# Writing to sample.json
with open("data.json", "w") as outfile:
    outfile.write(json_string)

spark = SparkSession.builder.appName("ReadJSON").config("spark.jars","work/data/commons-pool2-2.11.1.jar,work/data/spark-sql-kafka-0-10_2.12-3.4.0.jar,work/data/spark-streaming-kafka-0-10-assembly_2.12-3.4.0.jar").getOrCreate()


df = spark.read.load(
    './data.json', 
    format='json',
    multiLine=True, 
    schema=None)

# Add 'value' column to the DataFrame
df = df.withColumn("value", lit(json_string))

df.show()

# Write the DataFrame to Kafka
df.write.format("kafka").option("kafka.bootstrap.servers", "kafka:9092").option("topic", "json_one").save()
```

JSON can be created in many ways the above is just one of them.

```python
from pyspark.sql.types import StructType, StringType, IntegerType
from pyspark.sql.functions import from_json
from pyspark.sql import SparkSession
from pyspark.streaming import StreamingContext

# spark = SparkSession.builder.appName("ReadJSON").config("spark.jars","work/data/commons-pool2-2.11.1.jar,work/data/spark-sql-kafka-0-10_2.12-3.4.0.jar,work/data/spark-streaming-kafka-0-10-assembly_2.12-3.4.0.jar").getOrCreate()

# ^^^ We use the spark to read the JSON above so commenting this out if you're running one cell after another to look at the output in the logs ^^^

schema = StructType().add("name", StringType()).add("age", IntegerType()).add("city", StringType())
df = spark \
    .readStream \
    .format("kafka") \
    .option("kafka.bootstrap.servers", "kafka:9092") \
    .option("subscribe", "json_one") \
    .option("kafka.group.id", "myConsumerGroup") \
    .option("startingOffsets", "earliest") \
    .load() \
    .selectExpr("CAST(value AS STRING)")

# Parse the JSON data and apply the schema
df = df.select(from_json(df.value, schema).alias("data")).select("data.*")

query = df \
    .writeStream \
    .outputMode("append") \
    .format("console") \
    .start()

query.awaitTermination()
```

Now above is again one of many ways to read the value from Kafka as a string, then apply a schema to it.

## Challenge

Below we have some Spark and Kafka code to complete. Here are the steps we want to achieve:
    - Our objective is to answer the questions in the initial cell, in order to stream the information. 
    - Once we have streamed the information, we want to read it and write to individual csv files.
    - Then we should load the `.`csv`` files with our wrestler information into a combined spark DataFrame.

Before we get started we should make a new Topic:
``` python
from kafka.admin import KafkaAdminClient, NewTopic


admin_client = KafkaAdminClient(
    bootstrap_servers="kafka:9092", 
    client_id='test'
)

topic_list = []
topic_list.append(NewTopic(name="hell_in_a_cell", num_partitions=1, replication_factor=1))
admin_client.create_topics(new_topics=topic_list, validate_only=False)
```

The rest has been started but will need your expertise. First write the `JSON` and send the data with Kafka.

1. Cell One
``` python
import json
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, StringType, IntegerType
from pyspark.sql.functions import lit, col

# Questions to answer - no need to edit this section:
print("THIS HELL IN A CELL MATCH FEATURES THREE OF THE GREATEST WRESTLERS OF 2001.")
for i in range(3):
    print(f"WELCOME WRESTLER {i}!!!")
    name = input("What is your name?")
    favourite_colour = input("What is your favourite colour?")
    entrance_song = input("What track would you choose as your wrestler alter-ego entrance song?")
    feet = input("How many feet will you plummet?")

# Create a dictionary representing the JSON object
data = {
    # ???
}

# Serialize the dictionary to a JSON string
json_string = # ???

# Print the JSON string
print(json_string)

# Writing to data.json (this will save locally but likely inside the container)
with open("data.json", "w") as outfile:
    outfile.write(json_string)

spark = SparkSession.builder.appName("json_entrance_app").config("spark.jars","work/data/commons-pool2-2.11.1.jar,work/data/spark-sql-kafka-0-10_2.12-3.4.0.jar,work/data/spark-streaming-kafka-0-10-assembly_2.12-3.4.0.jar").getOrCreate()

# Use spark.read.load() to load the JSON data
df = spark.read.load(
    #???
    # )

# Add 'value' column to the DataFrame using the .lit() spark method
df = df.withColumn("value", lit(json_string))

df.show()

# Write the DataFrame to Kafka with all the required option() methods!
df.write.#???
```

You should see something like this as output:

+----------+----+---------+------+--------------------+
|    colour|feet|     name|  song|               value|
+----------+----+---------+------+--------------------+
|DuckEggRed|   4|HollyAnne|Cultus|{"name": "HollyAn...|
+----------+----+---------+------+--------------------+

2. Cell Two
``` python
rom pyspark.sql.types import StructType, StringType
from pyspark.sql.functions import from_json

schema = StructType() \
    .add(#????? SomethingType())

# Read stream with all the required .option()
df = spark \
    .readStream \
    .format(#????)



# Parse the JSON data and apply the schema with from_json()
df = df.select(from_json(df.value, schema).alias("data")).select("data.*")

# writeStream to csv - you will have to look up how to do this one! Give it a go! 
query = df \
    .writeStream \
    .option(# ??????)

# query.awaitTermination()
```

If you've got this far, well done! We'll give you the next one as a treat.

3. Cell Three

Final cell, this one's the cherry on the cake:

```python
import glob
 
# list all csv files only
csv_files = glob.glob('./work/data/wrestlercsv/*.{}'.format('csv'))

# print(csv_files) # print this variable to see csv paths of individual csv files from the stream

df = spark.read.csv(csv_files, header=False, schema=schema)

df.show()

```


## Submitting Your Work

Use [this form](https://airtable.com/shr6mk28x0fy3OrxN?prefill_Item=data_streaming_02) to submit your code and screen recording

Hold onto this notebook, you may want to refer back to it in upcoming chapters.

[Next Challenge](06_udf.md)

<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=03_spark_streaming%2F05_json_input_output.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=03_spark_streaming%2F05_json_input_output.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=03_spark_streaming%2F05_json_input_output.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=03_spark_streaming%2F05_json_input_output.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=03_spark_streaming%2F05_json_input_output.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
