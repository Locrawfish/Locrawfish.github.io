---
layout: post
title:  "Ruby’s Anonymous Class: The Eigenclass"
date:   2016-06-05 19:00:35 -0400
categories: 
---

You may have heard of Ruby's Eigenclass before, I know I had seen it in others' code far sooner than knowing what it was actually called, and you may have, too. 

Ruby's use of the Eigenclass can look like this:

```ruby
class Cogsworth
  class << self # <- the Eigenclass 
    def pun
    	puts "If it's not Baroque, don't fix it!"
    end
  end
end
```
Or like this:

```ruby
class Cogsworth
  def self.pun
	puts "If it's not Baroque, don't fix it!"
  end
end
```
When I orignially saw this I thought: **"Oh, that's how you declare class methods!"** And that's true, but I didn't quite realize that in the example above `self` actually refers to the Eigenclass, or the anonymous class, that is inserted into the hierarchy that inherits from the class `Cogsworth`. 

Take this example:

```ruby
Cogsworth.pun # => If it's not Baroque, don't fix it!
```

This is possible because `pun` is held onto by the Eigenclass, which acts like separate class that holds onto class methods as instance methods, but not interfering with any instance methods that may be created on the actual class (more on this in a minute.) Therefore, when you call `Cogsworth.pun` it's really something like this: `Cogsworth.CogsworthsEigenclass.pun`. 

![](http://25.media.tumblr.com/tumblr_ly19a98C3Q1qduy16o1_500.gif)

In the case where you only created an instance method (no Eigenclass), you couldn't call the method on the class:


```ruby
class Lumiere
  def welcome
	puts "Be our guest!"
  end
end

Lumiere.welcome # => NoMethodError: undefined method `welcome' for Lumiere:Class

```


## Why is it important? 

It’s important because using this allows you to make methods private that otherwise couldn't be made private.

Take this for example:

```ruby
class Cogsworth
  def self.advice
  	puts "Well, there's the usual things: flowers..., chocolates..., promises you don't intend to keep..."
  end 
  
  private
  def self.pun
	puts "If it's not Baroque, don't fix it!"
  end
end

Cogsworth.advice 	# => "Well, there's the usual things: flowers..., chocolates..., promises you don't intend to keep..."
Cogsworth.pun		# => "If it's not Baroque, don't fix it!"

```
As you can see instead of getting a `NoMethodError` , I'm able to access the `pun` method when I call it on `Cogsworth`. If it were truly private, I would not have been able to get the result. The reason for this is because the `private` method can only be used for *instance methods*. 

### This begs the question: how can you make a class method private? 
![](http://i.giphy.com/xT9DPFPfULYJHHrqN2.gif)

#### THE EIGENCLASS!
Because the methods created under the Eigenclass are *technically* **instance** methods on the Eigenclass, you can make them private! Like this:

```ruby
class Cogsworth
  class << self 
    def advice
      puts "Well, there's the usual things: flowers..., chocolates..., promises you don't intent to keep..."
    end 
  
    private
    def pun
      puts "If it's not Baroque, don't fix it!"
    end
  end
end

Cogsworth.advice 	# => "If it's not Baroque, don't fix it!"
Cogsworth.pun		# => NoMethodError: private method `pun' called for Cogsworth:Class

```


## So go forth, friend, and use the glorious Eigenclass! 

![](http://i.giphy.com/GDdlesaysfPvG.gif) 

