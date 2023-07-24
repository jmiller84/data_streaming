## Spark Walkthrough

We'll be using PySpark in this course it's become the most popular way
to use Spark. Spark actually runs on the JVM, which is the Java runtime.
So you have the choice of writing code in Java or Scala. 

You of course don't have to use either and that is where `pyspark` comes in handy as lots of data engineers and data scientists are more familiar with python and it's more forgiving syntax-lite approach to code. 

Let's create a simple Spark script and see how it interacts with the cluster, it's 
hard to visualise all this theory without at least seeing what Spark looks like.

### Running Spark
You can find the data for this example in the `datasets` folder under `dnd_monsters.csv`.

In the first cell in Jupyter place the code that imports and start a Spark Session.

```python
from pyspark import SparkContext

# Create a SparkContext
sc = SparkContext("local", "WordCountApp")
```
Create a new cell as we don't want to create multiple session each time we run the code.
``` python
# Load the input text file
lines = sc.textFile("./work/data/dnd_monsters.csv")

# What type is lines?
print(type(lines))

# Split file into rows
rows = lines.flatMap(lambda line: line.split("\\n"))

# Loop through rows, split on commas and pick the first column
for row in rows.collect():
    print(row.split(",")[0])
```

When you run the above code it will:

- Create a spark context.
- Read a local file called dnd_monsters.csv.
- Split the whole file up into rows.
- Collect the rows (more on collect later).
- Loop through the rows picking the first column.

### Spark Abstractions

Now above if you noticed the line that printed the type, you'll see the type was
RDD (Resilient Distributed Datasets), there are two other data abstractions that
Spark uses, DataFrames, and Datasets.Each of these abstractions provides 
different features and APIs for data processing. 
Let's look at each one:

1. RDDs (Resilient Distributed Datasets):
   RDDs are the fundamental building blocks of Apache Spark. They represent an immutable 
distributed collection of objects that can be processed in parallel across a cluster 
of machines. RDDs are fault-tolerant and allow for efficient distributed processing. 
They can be created from various data sources such as Hadoop Distributed File System
(HDFS), local file system, and distributed datasets from other systems.

RDDs offer two types of operations: transformations and actions. Transformations are 
operations that create a new RDD from an existing one, such as `map`, `filter`, and `reduceByKey`. 
Actions are operations that return a value or produce a side effect, like `count`, `collect`, and `saveAsTextFile`. 
RDDs provide a resilient nature by enabling lineage tracking, which allows them to recover lost 
partitions by recomputing them from the original data.

Although RDDs provide a flexible programming model, they lack the optimizations and type safety 
offered by DataFrames and Datasets.

2. DataFrames:
   DataFrames are a distributed collection of data organized into named columns. They provide a 
higher-level API and are conceptually similar to tables in a relational database or a data frame 
in R or Python pandas. DataFrames are designed to work with structured and semi-structured data. 
They bring the benefits of strong typing, optimization, and query optimization to Spark.

DataFrames can be created from various data sources, including RDDs, structured data files (e.g., `CSV`, `Parquet`), 
Hive tables, and external databases. DataFrames support a wide range of transformations and actions, allowing 
for efficient data manipulation, filtering, aggregation, and joining operations. 
They also offer a rich ecosystem of functions and libraries for data processing, such as Spark SQL, MLlib, and GraphX.

One advantage of DataFrames is that Spark can optimise DataFrames at runtime for you.

3. Datasets:
   Datasets are an extension of DataFrames, introduced in Spark 1.6, that combine the benefits 
of RDDs and DataFrames. Datasets provide a type-safe, object-oriented programming interface 
by adding the concept of a strongly-typed JVM object to a DataFrame.

Datasets offer the advantages of static typing, compile-time type checking, and improved optimization 
while maintaining the benefits of the DataFrame API. With Datasets, you can write Spark code in a more 
expressive and safer manner.

Datasets can be created from DataFrames or by manually creating them using a schema.
They provide transformations and actions similar to DataFrames but with the additional type safety.

It's worth noting that while DataFrames and Datasets have overlapping functionalities, 
Datasets are generally recommended when you need type safety and are working with structured or semi-structured data.

Overall, RDDs, DataFrames, and Datasets in Apache Spark provide different levels of abstraction 
and APIs for distributed data processing, allowing developers to choose the most appropriate one based 
on their requirements and use cases.

### Spark Sessions

In our above example we didn't create a Spark session because one was already running for us
which was created in the background by Jupyter.

``` python
from pyspark.sql import SparkSession
# Create a SparkSession
spark = SparkSession.builder.appName("ExampleSparkSession").master("local").getOrCreate()
# Set the master URL for local mode
```

Now that code will create a Spark session that we can work with, create a name for our app, 
which you'd see in log files and also run Spark in what we call local mode.

### Spark Submit

Things change fast in IT, when we started out we had to install servers in data centres,
then it took a while to create Spark clusters from these servers. It was tough managing 
the resources on the cluster and we'd have to reboot nodes now and then replace hard-drives
when they go down.

These days, as you can see above, you don't always even need to create a session to use Spark, but
for completeness we'll show you what it looks like to manually send your app 
to a cluster.

**You don't have to run this following command, this is simply to illustrate what it would look like in your job running spark from a python file without Jupyter**

``` bash
spark-submit --class com.example.MyApp --master local[4] --deploy-mode client myapp.py

```

The above code tells a Spark cluster to run in local mode with four threads.

``` bash
spark-submit --class com.example.MyApp --master yarn --deploy-mode cluster myapp.py

```

- Cluster Mode:
In cluster mode, the driver program runs on a worker node in the cluster. The cluster manager (e.g., YARN, 
Apache Mesos, or Spark Standalone) takes care of allocating resources and launching the driver program 
on one of the worker nodes. The driver then coordinates the execution of tasks on the worker nodes.
To run in cluster mode, you need to specify the --deploy-mode cluster option when using spark-submit.
The driver program's output and logs are typically written to the cluster's shared file system.

Cluster mode is suitable for larger deployments and when you don't need to interactively monitor the 
driver program's logs and output.

- Client Mode:
In client mode (also known as local mode or client deploy mode), the driver program runs on the 
machine where spark-submit is executed. It connects to the cluster manager and requests resources to 
launch executor processes on the worker nodes.
To run in client mode, you need to specify the --deploy-mode client option when using spark-submit. 
The driver program's output and logs are written to the console where spark-submit was executed.

Client mode is useful for development, debugging, and situations where you want to see the driver 
program's logs and output directly on the console.

In addition to these modes, spark-submit accepts various other options to configure the Spark application,
such as specifying the cluster manager, setting the number of executor instances, memory settings, and 
application-specific configurations.

Now having said that, most of the time you'll be testing code on your local machine in local mode,
and the rest of your time your cloud provider will take create of the rest for you, you'll probably
just need to start a session.

[Next Challenge](03_spark_coding.md)

<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F02_spark_api.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F02_spark_api.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F02_spark_api.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F02_spark_api.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F02_spark_api.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
