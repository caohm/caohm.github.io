---
title: ElasticSearch
layout: post
date: 2017-04-22 00:36:04 +0800
categories: ES
tags: ES
---


* content
{:toc}                                                                         

本文介绍开源搜索引擎elasticsearch
                                 









##### install


#### run

elasticsearch -d

port 9300 程序端口
port 9200 rest端口

plugin head web url: http://localhost:9200/_plugin/head/

mapping 文件路径： vi  elasticsearch-1.7.5/config/templates/datafusion.json

sudo elasticsearch/bin/plugin install mobz/elasticsearch-head



curl -XPUT 'http://localhost:9200/mdc/originData/data1' -d '{
    "cpu" : 10,
    "mem" : 10,
    "time": 2339 
    }'


curl -XDELETE 'http://localhost:9200/mdc/originData/data1'
curl -XGET 'http://localhost:9200/mdc/originData/data1'


### 聚合


``` json
{
  "query": {
    "bool": {
      "must": {
        "range": {
          "time": {
            "from": 1506787200000,
            "to": 1508746873436
          }
        }
      }
    }
  },
  "aggregations": {
    "exception": {
      "terms": {
        "field": "exception"
      }
    },
    "checkSubmitCode": {
      "terms": {
        "field": "checkSubmitCode"
      }
    }
  }
}
```