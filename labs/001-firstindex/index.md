# Elastic Engineer II Optional Lab 001
In this lab we will be creating an index in our Elasticsearch on an Ubuntu VM. 


Let’s confirm Elasticsearch is working as expected by connecting to the API.
```bash
curl 127.0.0.1:9200
```

```bash
$ curl 127.0.0.1:9200
{
  "name" : "itdyml7",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "lRpAt5psT1Cr-u6hS_bc2Q",
  "version" : {
    "number" : "6.2.3",
    "build_hash" : "c59ff00",
    "build_date" : "2018-03-13T10:06:29.741383Z",
    "build_snapshot" : false,
    "lucene_version" : "7.2.1",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

You can also test it by loading http:<VM IP>:9200 in a browser, and if you see something like the following it’s working correctly. 
![](index/05CDF398-09D6-4AE2-BA56-7A5BAA985A2D%204.png)

## Loading data into Elasticsearch 
Now that we have Elasticsearch installed it needs some data to aggregate and index.  Let’s go ahead and load in the complete works of William Shakespeare 

Download and create the mapping 
```bash
wget http://bit.ly/es-shakes-mapping -O shakes-mapping.json
curl -H 'Content-Type: application/json' -XPUT 127.0.0.1:9200/shakespeare --data-binary @shakes-mapping.json
```

Download the data 
```bash
wget http://bit.ly/es-shakes-data -O shakespeare_6.0.json
```

Now we are going to load this data into Elasticsearch through it’s API
```bash
curl -H 'Content-Type: application/json' -X POST 'localhost:9200/shakespeare/doc/_bulk?pretty' --data-binary  @shakespeare_6.0.json
```

And finally let’s go ahead and search the data we just inserted. 
```bash
curl -H 'Content-Type: application/json' -XGET '127.0.0.1:9200/shakespeare/_search?pretty' -d '
{
"query" : {
"match_phrase" : {
"text_entry" : "to be or not to be"
}
}
}
'
```

We are searching all of the data we inserted for “to be or not to be” and our result is…   Wow, pulled it out very quickly and we now know that it came from Hamlet.
```json
{
  "took" : 153,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 13.874454,
    "hits" : [
      {
        "_index" : "shakespeare",
        "_type" : "doc",
        "_id" : "34229",
        "_score" : 13.874454,
        "_source" : {
          "type" : "line",
          "line_id" : 34230,
          "play_name" : "Hamlet",
          "speech_number" : 19,
          "line_number" : "3.1.64",
          "speaker" : "HAMLET",
          "text_entry" : "To be, or not to be: that is the question:"
        }
      }
    ]
  }
}

```

## Lab Complete
