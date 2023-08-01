## Streaming Example

### Monster Tracking

Next we'll take some of the functions and techniques we've used before and generate some data
and apply some logic to create a simple monster tracker.
Right now some monsters have landed on Earth and we need to track their movements.
What we'll try to do is hide the examples so you can work it out yourself, but you can open it up if you get stuck.
But don't be scared to google anything you don't know or any syntax you've forgotten.

### The Topic

To work with in this section let's create a topic called: `monster_movement`
<details>
  <summary>Show Topic creation</summary>

```python
from kafka import KafkaAdminClient
from kafka.admin import NewTopic

# Kafka broker configuration
bootstrap_servers = 'kafka:9092'

# Create an instance of KafkaAdminClient
admin_client = KafkaAdminClient(bootstrap_servers=bootstrap_servers)

# Define the topics to be created
topics = [
    NewTopic(name='monster_movements', num_partitions=1, replication_factor=1)
]

# Create the topics
admin_client.create_topics(new_topics=topics)

# Close the admin client
admin_client.close()
```
</details>

### The Data

Next we'll generate the data.
We want to use the functions we create in our Spark streaming practice examples.
What we want to achieve is:
1. Create 200 events.
2. The event should pick five random rows from our dnd_monsters.csv and return the monster's name and str characteristic.
3. Along with the name and str we need the latitude and longitude of the monster and a timestamp.
Latitude is between -90 and 90 and longitude is -180 to 180.
4. Add a timestamp field called ts.
5. Remember to put everything into JSON then into a field called value for the kafka message.
6. Don't write to Kafka yet.

> *N.B. The `.collect()` function in PySpark is notoriously slow as it has to query the entire dataFrame and store it in memory. You may find that using python's `random` library and PySpark's `.collect()` function in order to return your random monsters is too slow. 

> You may try using `df = df.cache()` in order to cache the data for faster querying but this has varying degrees of success, and largely depends on memory, docker and seemingly factors that would be far too complex to get into for a simple random selection task.

> As such, for ease, there is a `rand()` function in PySpark that can be combined with `groupby()` and `limit()` to retrieve your monster selection.

Remember Kafka events have to have a field called value.
<details>
  <summary>Show data code</summary>

```python
import random
def choose_random_item(lst):
    return random.choice(lst)

def random_int(fro, to):
    return random.randint(fro, to)
```

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import rand
import random
import datetime
import json
def choose_random_item(lst):
    return random.choice(lst)

def random_int(fro, to):
    return random.randint(fro, to)
# Create a SparkSession
spark = SparkSession.builder.getOrCreate()

# Read the file into a DataFrame
df = spark.read.csv("dnd_monsters.csv",  header=True).where("str is not null").select("name", "str")

# Randomly select 5 lines
random_lines = df.orderBy(rand()).limit(5)

# Show the selected lines
random_lines.show(truncate=False)
list_tuples = random_lines.rdd.map(tuple).collect()


for monster in range(1,200):
    random_tuple = random.choice(list_tuples)
    data = {
        "name": random_tuple[0],
        "str": random_tuple[0],
        "ts": str(datetime.datetime.now()),
        "lat": random_int(-90, 90),
        "long": random_int(-180, 180)
    }

    # Serialize the dictionary to a JSON string
    json_string = json.dumps(data)

    # Print the JSON string
    print(json_string)
```

So the above reuses a lot of code we've used before, the only `gotcha` is a Kafka
event needing a field called value, which I've put all our fields into.
</details>

*N.B. If you use the above code, put it in a cell, and remember to skip this cell when running your `monster_tracker` to write to kafka.*
> The reason for this seems that we are making multiple `sparkSessions` and We are trying to write to the wrong one. I have not yet worked out why `spark.stop()` doesn't close the session for us to make a new one that uses the .jar files we need, so for now make sure to skip this cell - or put it in another notebook entirely!

### Let's add more

Now that we have our JSON monster locations printing and ready to use, but first let's change those latitudes
and longitudes to countries. Again try this yourself first and don't give up too quickly if you find it hard, use
the internet. Here's the library and how to use it:

```
pip install geopy
```
Once it's installed we can do this to get the country.
```python
from geopy.geocoders import Nominatim

# Create a geocoder instance
geolocator = Nominatim(user_agent="my_geocoder")

# Define the latitude and longitude coordinates
latitude = 51.5074
longitude = -0.1278

# Reverse geocode to get the location information
location = geolocator.reverse((latitude, longitude), language='en')

# Extract the country from the location information, with a random lat and long it might not have a country
country = "Sea"
try:
    country = location.raw['address']['country']
except:
    print("out to sea")
print(country)

# Print the country
print(country)
```

Now let's use the above code to add a country field to our JSON to write to our Kafka topic.

<details>
  <summary>Show Writing to Kafka</summary>

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import rand
import random
import datetime
import json

from geopy.geocoders import Nominatim

# Create a geocoder instance
geolocator = Nominatim(user_agent="my_geocoder")


def choose_random_item(lst):
    return random.choice(lst)

def random_int(fro, to):
    return random.randint(fro, to)
# Create a SparkSession
spark = SparkSession.builder.appName("Monsters") \
.config("spark.jars","commons-pool2-2.11.1.jar,spark-sql-kafka-0-10_2.12-3.4.0.jar,spark-streaming-kafka-0-10-assembly_2.12-3.4.0.jar").getOrCreate()

# Read the file into a DataFrame
df = spark.read.csv("dnd_monsters.csv",  header=True).where("str is not null").select("name", "str")

# Randomly select 5 lines
random_lines = df.orderBy(rand()).limit(5)

# Show the selected lines
random_lines.show(truncate=False)
list_tuples = random_lines.rdd.map(tuple).collect()


for monster in range(1,200):
    random_tuple = random.choice(list_tuples)
    lat = random_int(-90, 90)
    long = random_int(-180, 180)

    # Reverse geocode to get the location information
    location = geolocator.reverse((lat, long), language='en')

    # Extract the country from the location information
    country = "Sea"
    try:
        country = location.raw['address']['country']
    except:
        print("out to sea")
    print(country)
    data = {
    "name": random_tuple[0],
    "str": random_tuple[1],
    "ts": str(datetime.datetime.now()),
    "lat": lat,
    "long": long,
    "country": country
    }

    json_string = json.dumps(data)

    # Print the JSON string
    print(json_string)
    data = [(json_string,),]
    df = spark.createDataFrame(data, ["value"])
    df.show(1, False)
    # # Write the DataFrame to Kafka
    df.write \
        .format("kafka") \
        .option("kafka.bootstrap.servers", "kafka:9092") \
        .option("topic", "monster_movements") \
        .save()
```
</details>

Now if you managed to get through all that yourself well done. We'll be using that Kafka topic next
to do some more monster tracking.


## Moving On

Once you have done this, move onto the Streaming Continued folder and work through those files.

[Link here](https://github.com/makersacademy/data_streaming/blob/main/05_assessment/01_assessment.md)

<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=04_streaming_continued%2F02_streaming_examples.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=04_streaming_continued%2F02_streaming_examples.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=04_streaming_continued%2F02_streaming_examples.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=04_streaming_continued%2F02_streaming_examples.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=04_streaming_continued%2F02_streaming_examples.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
