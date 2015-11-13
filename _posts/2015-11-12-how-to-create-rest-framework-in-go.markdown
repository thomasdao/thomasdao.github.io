---
layout: post
title:  "How to create a REST framework in Go"
date:   2015-11-12 16:22:35 +0800
categories: go
---

I heard many nice things about Go, and when I learn it I find it really exciting. I decided to do something with it: create a REST framework in Go. I always want to build something that is friendly with mobile, just like [Parse](https://parse.com). It should copy all good things about Parse: easy CRUD, query, authentication and access control. It doesn't seem like a huge task, I hope.

I started with a simple use case: create a CRUD API for app models. When a client send a `POST` request to `/api/article`, a new article will be created, populated with request data, save to database and returns json back to client.  However since framework is higher level than a application code, it does not know what is the model in advance. How do I know which model to create when client post to `/api/article`?

Naturally I think of reflection. I would extract `article` from the URL, create an object dynamically based on the string `article`, then perform the rest. It can be done with Python, where I can just instantiate a new class from string and populate the object easily. This approach however quickly turns incredibly difficult in Go. You just can't instantiate a object from a string. Reflection in Go is pretty hard, I got confused easily between all the Types, Values, pointers and interfaces. Go is strict about type, so it's not straightforward to do type conversion when you don't know the type in advance. I found myself wrestling with the language instead of making it works for me. So I give up :(.

But, I kept hearing good things about Go. Parse also rewrote their stack in Go. I ignore it for quite some time, and when the painful memory fades, I decided to Google again. How normally people create a REST framework in Go when it seems incredibly restrictive? It can't be that hard!

Luckily I found [Sleepy](http://dougblack.io/words/a-restful-micro-framework-in-go.html) framework.
