## Challenge

Now we've seen that you can create a SparkSession, read from a file
paradise the data to create an RDD use the `createDataFrame()` function
to get a nice DataFrame to work with, then we can create some transformations, then collect or show the data, then Spark will leap into action, send the instructions to our executors then back to the driver for you to read.

Now let's put that into practice with a short assessment to send to your coach.

Navigate to the DND Monsters dataset [here](../datasets/dnd_monsters.csv) and import it into a DataFrame using PySpark.

Here is the challenge. Print the top 20 monsters which are:
1. Legendary,
2. Order them by the column dex (for Dexterity), 
3. If the monster is a flying type - subtract `2` from it's `dex`.

Now this is tricky and you'll need to google. One piece of advice, when you
order something make sure it's a number and not a word.

Also some of the imports you might need are:

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, when
```

Good Luck.


## Submitting Your Work

Use [this form](https://airtable.com/shr6mk28x0fy3OrxN?prefill_Item=data_streaming_01) to submit your code and screen recording

Hold onto this notebook, you may want to refer back to it in upcoming chapters.


<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F05_spark_assessment.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F05_spark_assessment.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F05_spark_assessment.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F05_spark_assessment.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=01_spark%2F05_spark_assessment.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
