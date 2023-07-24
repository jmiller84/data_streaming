## Advanced topics

This is just a quick piece on more advanced bits, that you might want to know.

Partitions, skew, and shuffling are important concepts in Apache Spark that relate
to data distribution and processing efficiency. Let's discuss each of them:

- Partitions:
Partitions are the basic units of data distribution and parallelism in Spark. 
- A dataset, such as an RDD, DataFrame, or Dataset, is divided into smaller partitions, 
- each containing a subset of the data. Partitions allow Spark to process data in parallel 
- across a cluster of machines or processor cores.

The number of partitions determines the degree of parallelism in Spark. By default, Spark tries 
to determine the optimal number of partitions based on the input data size and cluster configuration.
However, you can also explicitly specify the number of partitions using methods like `repartition` or `coalesce`.

- Skew:
Skew refers to an imbalance in the distribution of data across partitions. 
It occurs when a few partitions contain significantly more data or require more processing
time compared to others. Skew can negatively impact performance and resource utilization in Spark applications.

Skew can arise due to various factors, such as data characteristics, data skewness in the input,
or uneven partitioning during transformations. For example, in a group by operation, if certain keys 
have a much higher frequency than others, it can lead to skew.

Skew can cause a few partitions to become hotspots, leading to slower processing times and potential
resource bottlenecks. Identifying and mitigating skew is crucial to achieve optimal performance in Spark applications.

- Shuffling:
Shuffling is the process of redistributing data across partitions during certain operations, 
such as group by, join, or aggregation. It involves a data exchange and reorganization to ensure that
- records with the same key are brought together in the same partition or across different partitions as needed.

Shuffling can be an expensive operation in terms of time and resources since it involves data movement 
across the network. It requires coordination between the nodes in the cluster and can impact the overall
performance of Spark applications, especially when dealing with large datasets.

Shuffling can exacerbate skew-related issues, as data skewness may lead to uneven data distribution 
during shuffling. If a few keys have a much higher frequency than others, the data associated with 
those keys may become concentrated in a small number of partitions, causing performance degradation 
and potential out-of-memory errors.

To address skew and optimize shuffling, Spark provides techniques such as dynamic partitioning, salting, 
bucketing, and tuning shuffle-related configurations (e.g., `spark.sql.shuffle.partitions`). 
These techniques help evenly distribute data and minimize the impact of skew and shuffling on application performance.

Understanding partitions, skew, and shuffling is important for designing and optimizing Spark applications. 
By carefully managing partitions and mitigating skew, you can improve data processing 
efficiency, parallelism, and overall performance in your Spark workflows.

### Trickier Examples

Now in the future section we'll look at coding for streaming but I thought I'd cover a simple but important
pieces here. Streaming is the same as the examples in the last section, the reason Spark Structured
Streaming is so popular is that you just write normal Spark queries but on a stream. Most of the
time you will probably want to group and sum the data, or even more advanced actions, if you are
struggling with future examples and assessments, the internet is your friend, all programmers
around the world use google all day to remind themselves of syntax or how to do something tricky for
the first time.

Below is a quick example of a group by sum, you could do the same to add up values arriving
in a stream.

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import sum

# Create a SparkSession
spark = SparkSession.builder.appName("GroupBySumExample").getOrCreate()

# Create a DataFrame
data = [("Alice", 100), ("Bob", 200), ("Alice", 150), ("Bob",  300)]
df = spark.createDataFrame(data, ["Name", "Amount"])

# Group by "Name" and calculate the sum of "Amount"
result = df.groupBy("Name").agg(sum("Amount").alias("TotalAmount"))

# Show the result
result.show()
```


Next is a very tricky example, try to go through it line by line, google pieces if you need to.
This shows data arriving for different users then you find that users' previous event and use it.


```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import lag, col
from pyspark.sql.window import Window

# Create a SparkSession
spark = SparkSession.builder.appName("LagExample").getOrCreate()

# Create a DataFrame with timestamp, name, and dollars columns
data = [
    ("2022-01-01 09:00:00", "Alice", 1),
    ("2022-01-01 09:10:00", "Alice", 2),
    ("2022-01-01 09:20:00", "Bob", 20),
    ("2022-01-01 09:30:00", "Alice", 3),
    ("2022-01-01 09:30:00", "Bob", 10),
    ("2022-01-01 09:50:00", "Bob", 30)
]
df = spark.createDataFrame(data, ["timestamp", "name", "dollars"])

# Convert the "timestamp" column to timestamp type
df = df.withColumn("timestamp", col("timestamp").cast("timestamp"))

# Add a new column "previous_dollars" with the previous value of "dollars" for each user
df = df.withColumn("previous_dollars", lag("dollars").over(Window.orderBy("timestamp").partitionBy("name")))

# Calculate the difference between the current and previous dollar amounts
df = df.withColumn("amount_difference", col("dollars") - col("previous_dollars"))

# Show the result
df.show()
```

[Next Challenge](05_spark_assessment.md)

<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F04_advanced.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F04_advanced.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F04_advanced.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F04_advanced.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F04_advanced.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
