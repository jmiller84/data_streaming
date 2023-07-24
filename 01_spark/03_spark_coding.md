## Spark Development

### Recap

We've read a lot of theory, but let's simplify.

When you run a Spark program it will run on the driver, the driver well tell the executors
to run some calculations and when the executors finish it will send the results back 
to the driver for us to use.

We'll go over more theory now, so you have more complete knowledge, then we'll practice
with the different APIs.

### Actions and Transformations

In Apache Spark, actions and transformations are two fundamental types of operations 
that can be performed on distributed datasets (RDDs, DataFrames, or Datasets). 
They serve different purposes and have distinct characteristics. Let's explore 
the difference between actions and transformations:

- Transformations:
Transformations in Spark are operations that create a new dataset from an existing one.
They are lazy operations, meaning they don't immediately compute the result but 
- instead build a lineage graph to track the transformations applied to the original dataset.
- Transformations are only executed when an action is triggered.

Some common transformations in Spark include `map`, `filter`, `groupBy`, `join`, 
`flatMap`, and `select`. These operations are used to modify, filter, aggregate, 
or combine the data in the dataset. When a transformation is applied to a dataset, 
it returns a new dataset representing the transformed data.

Transformations are immutable, meaning they do not modify the original dataset but
create a new dataset with the desired transformation. This immutability enables Spark
to optimize the execution plan by applying transformations in a lazy and efficient manner.

- Actions:
Actions, on the other hand, are operations that trigger the execution of the transformations
and produce a result or side effect. When an action is called on a dataset, Spark evaluates 
the lineage graph, executes all the preceding transformations, and returns the result to the 
driver program or performs a side effect, such as writing the data to an output location or printing to the console.

Examples of actions in Spark include `count`, `collect`, `first`, `take`, `save`, 
`foreach`, and `reduce`. These operations either return a value to the driver program or
perform an action on the dataset.

Unlike transformations, actions are eager operations, meaning they immediately trigger the 
computation of the dataset. The evaluation of actions results in executing the entire lineage 
graph of transformations required to produce the final result.

It's important to note that actions are the boundary between the Spark driver program 
(the program that defines and controls the Spark application) and the Spark cluster
(where the distributed computation takes place). Actions initiate the execution of transformations 
on the distributed data across the cluster and bring the results back to the driver program.

In summary, transformations create a list of things that Spark will do in the future, when you
use an action Spark jumps into action and generally will return the result to the driver. It
isn't unusual to return results to the driver multiple times in the lifetime of a Spark
script.
It is also worth noting that if you tell Spark to download a 1 gig file and print that file
to screen it will be sent to the driver and if it doesn't have 1 gig or memory or hard-disk 
space Spark will struggle to do anything. Also if you partition a file across multiple executors
and one gets too much data, again you could have a problem.

### Creating RDDs from scratch

Create a new workbook and paste the below in and run it:
```python
from pyspark import SparkContext

# Create a SparkContext
sc = SparkContext(appName="ExampleRDD")

# Create an RDD from a list of elements
data = [1, 2, 3, 4, 5]
rdd = sc.parallelize(data)

# Perform operations on the RDD
squared_rdd = rdd.map(lambda x: x**2)
filtered_rdd = squared_rdd.filter(lambda x: x > 10)
sum_value = filtered_rdd.sum()

# Print the RDD elements and the sum
print("RDD Elements:")
for element in filtered_rdd.collect():
    print(element)

print("Sum:", sum_value)

```

### Create a DataFrame from scratch

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, StringType, IntegerType

# Create a SparkSession
spark = SparkSession.builder.appName("ExampleDataFrame").getOrCreate()

# Define the schema for the DataFrame
schema = StructType([
    StructField("name", StringType(), nullable=False),
    StructField("age", IntegerType(), nullable=False),
    StructField("city", StringType(), nullable=False)
])

# Create the data for the DataFrame
data = [
    {"name": "Alice", "age": 25, "city": "New York"},
    {"name": "Bob", "age": 30, "city": "San Francisco"},
    {"name": "Charlie", "age": 35, "city": "Los Angeles"}
]

# Create the DataFrame
df = spark.createDataFrame(data, schema)

# Show the DataFrame
df.show()

```
### Create a DataSet from scratch

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col

# Create a SparkSession
spark = SparkSession.builder.appName("DataFrameToDataset").getOrCreate()

# Define a class representing the structure of your data
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Create a DataFrame
data = [("Alice", 25),
        ("Bob", 30),
        ("Charlie", 35)]

columns = ["name", "age"]
df = spark.createDataFrame(data, columns)

# Convert DataFrame to Dataset using alias method
dataset = df.select(col("name"), col("age")).alias("Person")

# Show the Dataset
dataset.show()
```


In the above examples it's `very` important to note the data is a list of tuples/rows
but you need a schema with that.

### DataType Review

Now from the above three methods, creating an RDD and it's style of use is called
functional programming, using map and reduce to chain transformation together,
it feels much lower level than the others. 
With DataSet we have to specify the types, which can be handy so when we are
writing code our IDE we are using can point use in the right direction, when we
think we have an integer but after a transformation it might have become a 
decimal, it can warn up, but coming from Python most PySpark developers don't use
DataSets.
Having said that DataFrames are very popular.
You can also use Spark with SQL which we'll show below.

```python
from pyspark.sql import SparkSession

# Create a SparkSession
spark = SparkSession.builder.appName("ExampleDataFrameSQL").getOrCreate()

# Create a DataFrame
data = [("Alice", 25, "New York"),
        ("Bob", 30, "San Francisco"),
        ("Charlie", 35, "Los Angeles")]

columns = ["name", "age", "city"]
df = spark.createDataFrame(data, columns)

# Register the DataFrame as a temporary table
df.createOrReplaceTempView("people")

# Perform SQL queries on the DataFrame
sql_query = """
    SELECT name, age
    FROM people
    WHERE age >= 30
"""

result = spark.sql(sql_query)

# Show the query result
result.show()

```

Now here is basically the same code without SQL

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col

# Create a SparkSession
spark = SparkSession.builder.appName("ExampleDataFrameNoSQL").getOrCreate()

# Create a DataFrame
data = [("Alice", 25, "New York"),
        ("Bob", 30, "San Francisco"),
        ("Charlie", 35, "Los Angeles")]

columns = ["name", "age", "city"]
df = spark.createDataFrame(data, columns)

# Perform DataFrame operations
result = df.select("name", "age").where(col("age") >= 30)

# Show the result
result.show()

```

[Next Challenge](04_advanced.md)

<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F03_spark_coding.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F03_spark_coding.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F03_spark_coding.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F03_spark_coding.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F03_spark_coding.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
