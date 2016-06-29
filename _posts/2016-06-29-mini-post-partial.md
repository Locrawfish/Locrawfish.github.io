---
layout: post
title:  "Mini Blog Post: Partials and Cache keys"
date:   2016-06-29 8:00:35 -0400
categories: 
---

## Hello there readers! Wonderful Wednesday!

![](http://i.giphy.com/26tPd9QiB0qSPKSzK.gif=100x20)

This blog post is a bit out of the norm from my #BlogBuddies series, but I really want to make sure that I'm documenting what I'm learning on this blog. I often do this by carrying a notebook around work *all* the time and writing down things that I've learned or want to follow up on later (i.e. MySQL Engines). But this morning I was particularly inspired to get something out, and this is the result.

# So what have I learned this week?
Well it's only Wednesday, so there is still more learning to yet to happened, but I learned a few things thus far that I wanted to share:

## Options for Rails Partials

This one is something I stumbled upon while refactoring some code with my pair. 

In rails you can render partials using haml like this:

```ruby
= render :partial => "coffee_account" 
```

### But maybe you want ...
![](http://i.giphy.com/KpB7H0EPBWdDW.gif)

Don't worry, you have options. :) 

### the `locals` option
You may be familiar with the `locals` option. The `locals` option is a hash that allows you to pass in a variable the partial can use:

```ruby
= render :partial => "coffee_account", locals: { user: @coffee_aficionado }
```
So this above allows the partial to have access to the local variable `@coffee_aficionado` as the variable `user`.

### the `object` option
The other option, `object`, wasn't something I had used before. But's it's also very useful. It's similar to the `locals` option in that it allows you to pass in an object to the partial, but it does something a little different. 

```ruby
= render :partial => "coffee_account", object: @coffee_aficionado } 
```

This `object` option here gives you access to the local variable `@coffee_aficionado` under the name of the parital itself, `coffee_account`. 

So, this would be the equivalent to: 


```ruby
= render :partial => "coffee_account", locals: { coffee_account: @coffee_aficionado } 
```

### the `as` option
I haven't come across the `as` option yet, so I cannot speak to it's usefulness, but it's implementation is a little something like this:

```ruby
= render :partial => "coffee_account", object: @coffee_aficionado, as: 'user' 
``` 
This approach is equivalent to:

```ruby
= render :partial => "coffee_account", locals: { user: @coffee_aficionado } 
```
Your partial gains access to the object `@coffee_aficionado` as the variable `user`. Thus, using the `as` and `object` options together is just like using the `locals` option, but less DRY.

![](http://i.giphy.com/aMs2phB9gvHji.gif)

## Cache keys
Coming soon!