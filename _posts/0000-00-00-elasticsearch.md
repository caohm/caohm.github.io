---
title: elasticSearch
layout: post
date: 2017-11-14 13:01:02 +0800
categories: [ES]
tags: [ES]
---


* content
{:toc}                                                                                                          
本文记录











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