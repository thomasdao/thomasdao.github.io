---
layout: post
title:  "Things I learned when creating a REST framework in Go"
date:   2015-11-12 16:22:35 +0800
categories: go
---

I heard many nice things about Go, and when I learn it I find it really exciting. I decided to do something with it: create a REST framework in Go. I always want to build something that is friendly with mobile, just like [Parse](https://parse.com). It should copy all good things about Parse: easy CRUD, query, authentication and access control. It doesn't seem like a huge task, I hope.

I started with a simple use case: create a CRUD API for app models. When a client send a `POST` request to `/api/article`, a new article will be created, populated with request data, save to database and returns json back to client.  However since framework is higher level than a application code, it does not know what is the model in advance. How do I know which model to create when client post to `/api/article`?

Naturally I think of reflection. I would extract `article` from the URL, create an object dynamically based on the string `article`, then perform the rest. It can be done with Python, where I can just instantiate a new class from string and populate the object easily. This approach however quickly turns incredibly difficult in Go. You just can't instantiate a object from a string. Reflection in Go is pretty hard, I got confused easily between all the Types, Values, pointers and interfaces. Go is strict about type, so it's not straightforward to do type conversion when you don't know the type in advance. I found myself wrestling with the language instead of making it works for me. So I give up.

But, I kept hearing good things about Go. Parse also rewrote their stack in Go. I ignore it for quite some time, and when the painful memory fades, I decided to Google again. How normally people create a REST framework in Go when it seems incredibly restrictive? It can't be that hard!

Keep searching and don't settle, I found [Sleepy](http://dougblack.io/words/a-restful-micro-framework-in-go.html) framework. This guy gets everything right, and I'm excited (you probably should read his blog). There is no reflection required, application can customize the URL as it wishes. This must be the right way to work with Go I think - just don't use reflection! But how does `Sleepy` avoid the painful path?

It turns out there IS a way to know about the type of the application model in advance. Yes, by using interface. Sleepy defines a bunch of interfaces and works with these interfaces without assuming anything about the application model. I remembered an old principle of object oriented programming: working with interface but not concrete class, but so often I don't use interface at all in other languages, probably because they are so powerful at reflection.

Go is very different from other languages where it doesn't even have usual inheritance for concrete class. In Java I usually reuse code by putting it in super class. There is no such thing in Go - I thought struct embedding somehow replaces inheritance, but the embedded struct does not have any knowledge of the parent struct.

So there is a fundamental shift in the way I design my framework in Go. I start defining more interfaces for almost every task in my framework (authentication, caching, etc). For example to support caching, I defined an interface like below:

<pre>
type Cacher interface {
	Get(string, interface{}) error
	Set(string, interface{}) error
	Delete(string) error
	Exists(string) (bool, error)
}
</pre>

So I can easily support different type of cache like Redis or Memcached, as long as they implement these above methods. The interface should be small enough so it's easier for the model to satisfy. When a model defines a method to satisfy the interface, I provide specific implementation in a function so it can be reused easily.

On a side note, I guess this is why Go's built in library is so powerful. They use interface instead of concrete class in the method signature, so different concrete classes can be used in the method. Many other languages uses concrete class inside method signature, so later the application code must define a subclass to be used inside the method. In long run, subclass is no longer feasible.

There are a few places in the framework later I need to use reflection to make the API signature looks nicer, however I consider these things to be minor. It's better to avoid them unless you know you really need them that cannot do otherwise.

In conclusion, after I shift my design approach, I find it much more pleasant to work with Go, no longer feel fighting with it. I also appreciate how simple and flexible it is with interface oriented design. If you like to know more how the final framework looks like, visit [Goal](https://github.com/thomasdao/goal) and let me know if you have any comments!
