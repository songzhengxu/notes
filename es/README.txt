GET _search
{
  "query": {
    "match_all": {}
  }
}

# 创建索引
PUT /test
# 删除索引
DELETE /test

# 非结构话的创建索引
PUT /user/_doc/1
{
  "name":"宋正旭",
  "age":28
}

# 指定id 进行更新
PUT /user/_doc/1
{
  "name":"宋正旭",
  "age":28
}
# put修改为全量修改
PUT /user/_doc/1
{
  "name":"宋正旭"
}

# 获取文档
GET /user/_doc/1

# post 修改文档指定字段
POST /user/_update/1
{
  "doc": {
     "name":"宋有才"
  }
}

# 强制指定创建 如果文档已经存在 则失败
PUT /user/_create/4
{
  "name":"宋正江",
  "age":29
}

# 获取文档数据
GET /user/_doc/2

# 删除某个文档
DELETE /user/_doc/2

# 查询全部文档
GET /user/_search

DELETE /user

# 结构化创建索引
PUT /user 
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "age":{"type":"integer"},
      "name":{"type": "text"}
    }
  }
}

# 如果指定字段类型不对，插入数据会报错，但是没有指定的字段，是可以插入的

# 不带条件查询所有的文档
GET /user/_search
{
  "query": {
    "match_all": {}
  }
}

# 分页查询
GET /user/_search
{
  "query": {
    "match_all": {}
  },
  "from": 0,
  "size": 2
}

# 带条件的关键字的查询
GET /user/_search
{
  "query": {
    "match": {
      "name": "宋"
    }
  }
}

# 带排序查询
GET /user/_search
{
  "query": {
    "match": {
      "name": "宋正旭"
    }
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    }
  ]
}

# 带 filter 的查询 
# filter 只能作用于bool 中
# term 完全匹配
GET /user/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "term": {
            "age": "30"
          }
        }
      ]
    }
  }
}

# term 完全匹配，且是匹配分词后的token,所以下边这个是查不出数据的
GET /user/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "term": {
            "name": "宋有才"
          }
        }
      ]
    }
  }
}
# match 与term 的 区别
# match 分词匹配 term 完全匹配
GET /user/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "match": {
            "name": "宋有才"
          }
        }
      ]
    }
  }
}

# 带聚合
GET /user/_search
{
  "query": {
    "match_all": {}
  },
  "aggs": {
    "group_by_age": {
      "terms": {
        "field": "age"
      }
    }
  }
}

# analyze 分析过程
PUT /movie/_doc/1
{
  "name":"Eating an apple a day, keeps the doctor away"
}

# 我们想搜索eat 时 把上面这部电影搜索出来，因为我们认为 eat 和 eating 是差不多一样的意思
GET /movie/_search
{
  "query": {
    "match": {
      "name": "eat"
    }
  }
}

# 分词的结果
GET /movie/_analyze
{
  "field": "name", 
  "text": "eat"
}
# 分词的结果
GET /movie/_analyze
{
  "field": "name", 
  "text": "Eating an apple a day, keeps the doctor away"
}

DELETE /movie

PUT /movie
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "name":{
        "type": "text",
        "analyzer": "english"
      }
    }
  }
}

# _analyze分析的过程 (分词的过程)
# 字符过滤器(去除符号)->字符处理器(默认标准字符处理-以空格和标点进行分割内容)-> 分词过滤(变小写)

# english 分词器 
# 字符处理器(去除符号+量词+then等等无意义词)->字符处理器->分词过滤(分词转换+词干转化 复数转化为词干)


# 相关性查询


# 字段类型  
# Text 被分析索引的字符串类型
# keyword 不能被分析，只能精确匹配的字符串类型
# data 日期类型 fromat 一起使用
# 数字类型 long,integer,short,double
# 布尔类型 bool false true
# Array : ["1","2"]
# object类型 json嵌套
# ip类型
# 地理位置 geo



# 数据创建索引和导入数据的步骤
# query dls
# 查全率 查准率

# match 和 term 的区别
GET /tmdb/_search
{
  "query": {
   "match": {
     "title": "Steve Zissou"
   }
  }
}

GET /tmdb/_search
{
  "query": {
   "term": {
     "title": "Steve Zissou"
   }
  }
}

# 分词后的and 和 or 默认的是or
GET /tmdb/_analyze
{
  "field": "title", 
  "text":"basketball with cartoom aliens"
}

# 所以这句话会把 alien相关的都搜索出来
GET /tmdb/_search
{
  "query": {
   "match": {
     "title": "basketball with cartoom aliens"
   }
  }
}

# 指定匹配模式
# 所以这句话会把 alien相关的都搜索出来
GET /tmdb/_search
{
  "query": {
   "match": {
     "title":{
       "query":"basketball with cartoom aliens",
       "operator": "or"
     }
   }
  }
}

# 最小词匹配项,指定分词至少匹配数量
GET /tmdb/_search
{
  "query": {
   "match": {
     "title":{
       "query":"basketball love",
       "operator": "or",
       "minimum_should_match": 2
     }
   }
  }
}

# 短语查询 match_phrase
GET /tmdb/_search
{
  "query": {
   "match_phrase": {
     "title": "Steve Zissou"
   }
  }
}
# 多字段查询
GET /tmdb/_search
{
  "query": {
  "multi_match": {
    "query":"basketball with cartoom aliens",
    "fields": ["title","overview"]
    }
  }
}

# 多字段查询 指定字段权重
GET /tmdb/_search
{
  "query": {
  "multi_match": {
    "query":"basketball with cartoom aliens",
    "fields": ["title^10","overview"]
    }
  }
}



# es的打分规则
# tf 词频,词出现的次数对应文档地段中的频, 比如title字段有10个词，某个单词出现了2两次,另外一个title出现三次，则第二个分数高一点
# idf 逆文档频率 代表一个分词在整个文档中出现的频率取反，，因为这个词太平常了，所以权重低,反之一个词在整个文档中只出现一次，那权重自然高
# tfnorm 词频归一化 比如待搜索的title 只有两个词 命中一个，和 title有三个词 命中一个，则 第一个结果的分数高一点

# BM25 解决词频问题


GET /tmdb/_search
{
  "explain": true, 
  "query": {
      "match": {
        "title": "steve"
      }
  }
}
GET /tmdb/_search
{
  "explain": true, 
  "query": {
  "multi_match": {
    "query":"basketball with cartoom aliens",
    "fields": ["title^10","overview"]
    }
  }
}

# max of 多字段打分取最大值 dis_max

# tie_breaker权重调整
GET /tmdb/_search
{
  "query": {
  "multi_match": {
    "query":"basketball with cartoom aliens",
    "fields": ["title^10","overview"],
    "tie_breaker": 0.3
    }
  }
}

# bool查询
# must : 必须都为true
# must not:必须都为false
# should 其中一个为true即可


# should 查询 
GET /tmdb/_search
{
  "explain": true, 
  "query": {
    "bool": {
      "must": [
        {"match": { "title": "basketball with cartoom aliens"}},
        {"match": { "overview": "basketball with cartoom aliens"}}
      ]
    }
  }
}

# should 查询 
GET /tmdb/_search
{
  "explain": true, 
  "query": {
    "bool": {
      "should": [
        {"match": { "title": "basketball with cartoom aliens"}},
        {"match": { "overview": "basketball with cartoom aliens"}}
      ]
    }
  }
}
# must 和 should 

# 不同的multi_query其实是有不同的type的
# best_fields 默认的得分方式 取得最高分的作为文档的对应分数
# most_fields 考虑所有的文档得分字段想加的结果

GET /tmdb/_search
{
  "query": {
  "multi_match": {
    "query":"basketball with cartoom aliens",
    "fields": ["title","overview"],
    "type": "best_fields"
    }
  }
}
# 等同于 

GET /tmdb/_search
{
  "query": {
    "dis_max": {
      "queries": [
        {"match": { "title": "basketball with cartoom aliens"}},
        {"match": { "overview": "basketball with cartoom aliens"}}
      ]
    }
  }
}

GET /tmdb/_validate/query?explain
{
  "query": {
  "multi_match": {
    "query":"basketball with cartoom aliens",
    "fields": ["title","overview"],
    "type": "best_fields"
    }
  }
}

# most_fields 模式
GET /tmdb/_search
{
  "query": {
  "multi_match": {
    "query":"basketball with cartoom aliens",
    "fields": ["title","overview"],
    "type": "most_fields"
    }
  }
}
# most_fields 模式 的explain 
# ~1.0 使用想加的模式
GET /tmdb/_validate/query?explain
{
  "query": {
  "multi_match": {
    "query":"basketball with cartoom aliens",
    "fields": ["title","overview"],
    "type": "most_fields"
    }
  }
}

# cross_fields 使用词中心的方式进行查询 
GET /tmdb/_validate/query?explain
{
  "query": {
  "multi_match": {
    "query":"basketball with cartoom aliens",
    "fields": ["title","overview"],
    "type": "cross_fields"
    }
  }
}



