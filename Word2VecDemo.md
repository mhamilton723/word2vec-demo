

```python
import urllib2
# If you are using Python 3+, import urllib instead of urllib2

import json 

def answer_query(query):
    data =  {

            "Inputs": {

                    "input1":
                    {
                        "ColumnNames": ["query", "Column 1"],
                        "Values": [ [ query, "value" ] ]
                    },        },
                "GlobalParameters": {
            }
    }

    body = str.encode(json.dumps(data))

    url = 'https://ussouthcentral.services.azureml.net/workspaces/59d75323e660433c8776d535887aefc3/services/82b00459d38a477392793b272ceac4c2/execute?api-version=2.0&details=true'
    api_key = '<CENSORED>' # Replace this with the API key for the web service
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

    req = urllib2.Request(url, body, headers) 


    try:
        response = urllib2.urlopen(req)

        # If you are using Python 3+, replace urllib2 with urllib.request in the above code:
        # req = urllib.request.Request(url, body, headers) 
        # response = urllib.request.urlopen(req)

        result = response.read()
        return json.loads(result)['Results']['output1']['value']['Values'][0] 
    except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))

        # Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
        print(error.info())

        print(json.loads(error.read()))                 
    
    


    
```

# Fun with word2vec!
### Mark Hamilton, Azure Machine Learning, marhamil@microsoft.com

* The function "answer\_query" takes in any expression involving the standard mathematical operations on vectors and scalars. It returns the 5 nearest neighbors of the resulting vector in order.

* The function v(\_) : String $ \rightarrow$ Vector takes a word and brings you to its respective embedding. The broadcasting is taken care of by numpy. 
* This demo only uses the top 300k words, the full file contains several million.


![title](http://www.socher.org/uploads/Main/MultipleVectorWordEmbedding.png)

## Automatic Synonym detection


```python
answer_query("v(happy)")
```




    [u'glad', u'pleased', u'ecstatic', u'overjoyed', u'thrilled']




```python
answer_query("v(huge)")
```




    [u'enormous', u'big', u'massive', u'tremendous', u'gigantic']




```python
answer_query("v(soldier)")
```




    [u'solider', u'serviceman', u'soldiers', u'airman', u'guardsman']



## Analogy tasks

![title](https://www.tensorflow.org/images/linear-relationships.png)


```python
answer_query("v(superman)-v(man)+v(woman)")
```




    [u'superwoman', u'superhero', u'Superwoman', u'heroine', u'mere_mortal']




```python
answer_query("v(fries)-v(America)+v(France)")
```




    [u'French_fries', u'frites', u'french_fries', u'fried_potatoes', u'baguettes']




```python
answer_query("v(Windows)-v(Microsoft)+v(Apple)")
```




    [u'Macs', u'Mac_OS', u'OS_X', u'iMac', u'iPhone']



## Be careful, word embeddings contain human biases


```python
answer_query("v(computer_programmer)-v(man)+v(woman)")
```




    [u'homemaker',
     u'schoolteacher',
     u'graphic_designer',
     u'mechanical_engineer',
     u'electrical_engineer']



### You now have the ability to compare apples and oranges!


```python
answer_query("v(batman)-v(apples)+v(oranges)")
```




    [u'superman', u'Caped_Crusader', u'villian', u'superhero', u'SUPERMAN']



## These queries can be compounded:


```python
 answer_query("v(king)-v(man)+v(woman)-v(king)+v(kings)")
```




    [u'queens', u'queen', u'princes', u'monarchs', u'emperors']



## Not just analogies: Qualitative arithmatic


```python
answer_query("v(superman)-v(man)")
```




    [u'superhuman', u'superhero', u'Galactus', u'mere_mortal', u'Superwoman']




```python
answer_query("v(life)-v(death)")
```




    [u'career',
     u'lifestyles',
     u'everyday',
     u'lifestyle',
     u'interpersonal_relationships']




```python
answer_query("(v(China)+v(river))/2")
```




    [u'Yangtze_River', u'Yangtze', u'Yangtze_river', u'rivers', u'lake']




```python
answer_query("v(one)+v(one)")
```




    [u'only', u'two', u'three', u'five', u'four']



## These vectors can be used off the shelf in most algorithims and tend to improve performance. Some examples include:

* ### Clustering on vectors for topic detection
* ### Inputs for encoder-decoder translation
* ### Input features for knowledgebase completion
* ### Language modeling
* ### Joint word/image or word/link embeddings for search

## Method can be generalized to:

* ### Yield embeddings for phrases
* ### Yield multiple embeddings for words with multiple meanings
* ### Fall back on character embeddings when the word is OOV

### Thanks to Ratan, Ilya, AK for help and bugfixes! 

 
