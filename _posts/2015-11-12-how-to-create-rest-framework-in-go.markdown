---
layout: post
title:  "How to create a REST framework in Go"
date:   2015-11-12 16:22:35 +0800
categories: go
---

As a mobile developer, I use Parse frequently and I really enjoy it as the backend for my app. Parse mobile SDK are easy to store data to both local datastore as well as the server, so without a dedicated backend developer I am still productive. However it has some drawback as well: the query can be slow, it's difficult to analyze data without downloading the whole table as json file and process it with Python, cannot add memcached or redis or websocket because Parse does not support it.

Since I'm learning Go, I am curious if I can use it to replicate the features offered by Parse. First, I want to use SQL database to store my data! It is much more convenient to fire up some SQL query than to analyze with Python!

SQL database is super fast if the model are indexed appropriately, so one more objective is that my framework needs to be flexible to add and modify index. I don't really trust Parse with their [Smart Indexing](http://blog.parse.com/learn/engineering/smart-indexing-at-parse/), I'd rather them to let me control it myself than their half baked algorithm. (Why do I think their algorithm is half baked? Well most of my queries run quite slow even though I have less than 3K records in my table, but this is my opinion anyway.)

But Parse supports basic authentication, CRUD and query really well, and you can do all that from mobile SDK instead of having a dedicated API endpoint for each query. When it's time to tight up security, Parse offer ACL to let only certain role to read/write to a record. It's really convenient so let see how to replicate that.
