## Assessment

Once you have your stream in place and ready to be read - record the session so you are able to submit your notebook and your output over to a coach for review. Note this now, as it can be a finickity process with some loading time dependent on machine memory etc - so best to have things ready to go for a recording!

Now we know how to create monster movements, let start doing some calculations.
So the monsters are going to `rampage` and destroy the countries that they're in.
So we'll need to calculate the damage that has been done based on the population of the
country that they're in.
Next let's add a field for population. We'll be using the below library and I've created a function that you can use below, I've hidden it in case you want to try your own.

```python
pip install geonamescache
```

Then to use it, you'll probably have to make this a function and put it in an udf:
```python
country_data = geonames.get_countries_by_names().get(country_name)
population = country_data.get('population')
```

### Assessment Steps

So we read the data then: 
- Create a field called population, if the country doesn't return the word "sea",
as in the monster is at sea and no population.
- Create a field called damage, if the population is greater than 1 then divide the population
by 1000 and multiple that by the str of the monster. Otherwise just return their str.
- Create one stream to console that shows the name of the country and how much damage it has taken.
- Create another stream that shows a dataset with the monsters and how much damage they have caused.

Then once you've done that send the code and your output over to your coach for review.


<details>
  <summary>Show getting population</summary>

```python
pip install geonamescache
```

```python
def get_country_population(country_name):
    geonames = GeonamesCache()
    # population = 1
    try:
        country_data = geonames.get_countries_by_names().get(country_name)
        population = country_data.get('population')
    except:
        population = 1
        print("{} country not found".format(population))
    return population
```
</details>

<!-- OMITTED -->

## Submitting Your Work

Use [this form](https://airtable.com/appJ1wvInmFyFFYlN/shrvo9ePjlwnaiLv5?prefill_Item=data_streaming_03) to submit your code and screen recording

<!-- BEGIN GENERATED SECTION DO NOT EDIT -->

---

**How was this resource?**  
[😫](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=05_assessment%2F01_assessment.md&prefill_Sentiment=😫) [😕](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=05_assessment%2F01_assessment.md&prefill_Sentiment=😕) [😐](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=05_assessment%2F01_assessment.md&prefill_Sentiment=😐) [🙂](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=05_assessment%2F01_assessment.md&prefill_Sentiment=🙂) [😀](https://airtable.com/shrUJ3t7KLMqVRFKR?prefill_Repository=makersacademy%2Fdata_streaming&prefill_File=05_assessment%2F01_assessment.md&prefill_Sentiment=😀)  
Click an emoji to tell us.

<!-- END GENERATED SECTION DO NOT EDIT -->
