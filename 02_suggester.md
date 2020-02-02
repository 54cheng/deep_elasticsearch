# 1、term_suggester
```
POST library/_search
{
  "suggest":{
  "first_suggestion":{
    "text":"wordl",
    "term":{
      "field":"title"
    }
  }
  }
}

POST library/_search
{
  "suggest": {
    "text" : "wordl fyodro cremi",
    "my-suggest-1" : {
      "term" : {
        "field" : "title"
      }
    },
    "my-suggest-2" : {
       "term" : {
        "field" : "otitle"
       }
    },
     "my-suggest-3" : {
       "term" : {
        "field" : "author"
       }
    }
  }
}
```

# 2、phrase_suggester
```
POST library/_search
{
  "suggest": {
    "my-suggestion": {
      "text": "The orows of Yung Werther",
      "phrase": {
        "field": "title.keyword",
        "highlight": {
          "pre_tag": "<em>",
          "post_tag": "</em>"
        }
      }
    }
  }
}
```

# 3、completion suggester 参见Elasitcsearch社区大神讲解
参考：https://elasticsearch.cn/article/142

在ES7.2下实战：

```
PUT /blogs
{
  "mappings": {
      "properties": {
        "body": {
          "type": "text"
        }
      }
    }
}

POST _bulk/?refresh=true
{ "index" : { "_index" : "blogs" } }
{ "body": "Lucene is cool"}
{ "index" : { "_index" : "blogs" } }
{ "body": "Elasticsearch builds on top of lucene"}
{ "index" : { "_index" : "blogs" } }
{ "body": "Elasticsearch rocks"}
{ "index" : { "_index" : "blogs" } }
{ "body": "Elastic is the company behind ELK stack"}
{ "index" : { "_index" : "blogs" } }
{ "body": "elk rocks"}
{ "index" : { "_index" : "blogs" } }
{  "body": "elasticsearch is rock solid"}

POST _analyze
{
  "text": [
    "Lucene is cool",
    "Elasticsearch builds on top of lucene",
    "Elasticsearch rocks",
    "Elastic is the company behind ELK stack",
    "elk rocks",
    "elasticsearch is rock solid"
  ]
}

POST /blogs/_search
{ 
  "suggest": {
    "my-suggestion": {
      "text": "lucne rock",
      "term": {
        "suggest_mode": "missing",
        "field": "body"
      }
    }
  }
}

POST /blogs/_search
{ 
  "suggest": {
    "my-suggestion": {
      "text": "lucne rock",
      "term": {
        "suggest_mode": "always",
        "field": "body"
      }
    }
  }
}

POST /blogs/_search
{
  "suggest": {
    "my-suggestion": {
      "text": "lucne and elasticsear rock",
      "phrase": {
        "field": "body",
        "highlight": {
          "pre_tag": "<em>",
          "post_tag": "</em>"
        }
      }
    }
  }
}

PUT /blogs_completion/
{
  "mappings": {
      "properties": {
        "body": {
          "type": "completion"
        }
      }
    }
}

POST _bulk/?refresh=true
{ "index" : { "_index" : "blogs_completion"} }
{ "body": "Lucene is cool"}
{ "index" : { "_index" : "blogs_completion"} }
{ "body": "Elasticsearch builds on top of lucene"}
{ "index" : { "_index" : "blogs_completion" } }
{ "body": "Elasticsearch rocks"}
{ "index" : { "_index" : "blogs_completion" } }
{ "body": "Elastic is the company behind ELK stack"}
{ "index" : { "_index" : "blogs_completion" } }
{ "body": "the elk stack rocks"}
{ "index" : { "_index" : "blogs_completion"} }
{ "body": "elasticsearch is rock solid"}

POST blogs_completion/_search?pretty
{ "size": 0,
  "suggest": {
    "blog-suggest": {
      "prefix": "elastic i",
      "completion": {
        "field": "body"
      }
    }
  }
}

PUT /blogs_completion_02/
{
  "mappings": {
      "properties": {
        "body": {
          "type": "completion",
          "analyzer": "english"
        }
      }
    }
}

POST blogs_completion_02/_search?pretty
{ "size": 0,
  "suggest": {
    "blog-suggest": {
      "prefix": "elastic",
      "completion": {
        "field": "body"
      }
    }
  }
}

POST _analyze
{
  "analyzer":"english",
  "text": "elasticsearch is rock solid"
}
```
