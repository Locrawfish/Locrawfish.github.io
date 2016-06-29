---
layout: post
title:  "Mid-Week Blog Post: Partials, Cache keys, & MySQL Engines"
date:   2016-06-29 20:00:35 -0400
categories: 
---

## Hello there readers! Wonderful Wednesday!

![](http://i.giphy.com/26tPd9QiB0qSPKSzK.gif=100x20)

This blog post is a bit out of the norm from my #BlogBuddies series, but I really want to make sure that I'm documenting what I'm learning on this blog. I often document what I learn by obsessively carrying a notebook around work *all* the time and writing down things that I've learned or want to follow up on later (i.e. MySQL Engines). But this morning I was particularly inspired to get something on my blog (instead of hoarding it--and possibly forgetting about it--in my notebook), and this is the result.

# So what have I learned this week?
Well it's only Wednesday, so there is still more learning to yet to happen, but I learned a few things thus far that I wanted to share:

## Options for Rails Partials

This one is something I stumbled upon while refactoring some code with my pair. 

In Rails you use can render partials using haml like this:

```ruby
= render partial: "coffee_account" 
```

### But maybe you want ...
![](http://i.giphy.com/KpB7H0EPBWdDW.gif)

Don't worry, you have options. :) 

### the `locals` option
You may be familiar with the `locals` option. The `locals` option is a hash that allows you to pass in a variable the partial can use:

```ruby
= render partial: "coffee_account", locals: { user: @coffee_aficionado }
```
So the code above allows the partial to have access to the local variable `@coffee_aficionado` using the variable `user`.

### the `object` option
The other option, `object`, wasn't something I had used before. But it's also very useful. It's similar to the `locals` option in that it allows you to pass in an object to the partial, but it does something a little different. 

```ruby
= render partial: "coffee_account", object: @coffee_aficionado } 
```

This `object` option here gives you access to the local variable `@coffee_aficionado` under the name of the partial itself, `coffee_account`. 

So, this would be the equivalent to: 


```ruby
= render partial: "coffee_account", locals: { coffee_account: @coffee_aficionado } 
```

### the `as` option
I haven't come across the `as` option yet, so I cannot speak to it's usefulness, but it's implementation is a little something like this:

```ruby
= render partial: "coffee_account", object: @coffee_aficionado, as: 'user' 
``` 
This approach is equivalent to:

```ruby
= render partial: "coffee_account", locals: { user: @coffee_aficionado } 
```
Your partial gains access to the object `@coffee_aficionado` as the variable `user`. Thus, using the `as` and `object` options together is just like using the `locals` option, but less DRY.

![](http://i.giphy.com/aMs2phB9gvHji.gif)

# Cache keys
Woohoo!!! Cache keys!! 

## What are these keys I speak of???
Cache keys are pretty rad. They are a unique page key associated with content that is stored so the content on your page can be rendered quickly. 

An example of cache keys in javascript is:

```javascript
cache.keys(request,{options}).then(function(keys) {
});
```

Rails also has it's own that looks a bit like this:

```ruby
Rails.cache.read(site: "Lorem Ipsum", owners: [me1, me2])
```
Check out this [guide](http://guides.rubyonrails.org/caching_with_rails.html#cache-keys) for more info on the Rails cache keys. 

![](http://i.giphy.com/iC2JWHL1MhG7e.gif)

# MySQL Storage Engines
This topic will be it's own blog post in the future, but it's something I wanted to mention here. I was unaware of MySQL engines, or if I had heard of them I didn't do a `git commit` in my head. 

MySQL storage engines are table engines that your database uses to do all those fun CRUD operations, that is *create, read, update, and destroy*. 

There are two main engines for MySQL: MyISAM and InnoDB. 

## A very brief compare and contrast

| **MyISAM** | **InnoDB** |
| ------ | ----------- |
| Not ACID* compliant| ACID* compliant |
| it's non-transactional| it's transactional |
| provides table-level locking |  provides row-level locking|


**[What's ACID???](https://en.wikipedia.org/wiki/ACID)*

There is much more to come on this topic, so stay tuned!

![](http://i.giphy.com/GroNK2MPxjjeU.gif)

