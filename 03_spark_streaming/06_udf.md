## User defined functions

In PySpark, a User-Defined Function (UDF) allows you to define custom transformations
that can be applied to Spark DataFrames or columns. UDFs are particularly useful when you need 
to apply complex or custom logic to some data that is not directly available as a built-in Spark function.

Here's an example of how to create and use a UDF in PySpark:

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import udf
from pyspark.sql.types import StringType

# Create a SparkSession
spark = SparkSession.builder \
    .appName("UDFExample") \
    .getOrCreate()

# Create a sample DataFrame
data = [("John", 25), ("Alice", 30), ("Bob", 35)]
df = spark.createDataFrame(data, ["name", "age"])

# Define a UDF to convert the name to uppercase
def uppercase_name(name):
    return name.upper()

# Register the UDF
uppercase_udf = udf(uppercase_name, StringType())

# Apply the UDF to the DataFrame
df_with_uppercase = df.withColumn("upper_name", uppercase_udf(df["name"]))

# Show the DataFrame with the uppercase name column
df_with_uppercase.show()
```

In this example, we define a UDF called `uppercase_name()` that takes a single input parameter (`name`) and returns the uppercase version of the name. The UDF uses the `upper()` method available in Python's string data type.

We then register the UDF using the `udf()` function, specifying the name of the UDF function (`uppercase_name`) and the return type (`StringType()` in this case). The registered UDF (`uppercase_udf`) can now be used as a regular Spark function.

Next, we apply the UDF to the "name" column of the DataFrame using the `withColumn` function. The UDF is invoked by passing the column `df["name"]` as the argument.

Finally, we show the resulting DataFrame `df_with_uppercase`, which includes an additional column "upper_name" 
containing the uppercase versions of the names.

UDFs provide a flexible way to apply custom logic to Spark DataFrames, allowing you to extend the capabilities 
of Spark with your own functions.

<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=03_spark_streaming%2F06_udf.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=03_spark_streaming%2F06_udf.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=03_spark_streaming%2F06_udf.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=03_spark_streaming%2F06_udf.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=03_spark_streaming%2F06_udf.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
