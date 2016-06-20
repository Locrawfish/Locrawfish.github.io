---
layout: post
title:  "One is the Loneliest Number: The Singleton Design Pattern"
date:   2016-06-19 21:00:35 -0400
categories: 
---

This week a colleague of mine shared his copy of the book [*Design Patterns: Elements of Reusable Object-Oriented Software*](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612) by the **The Gang Of Four**. The reason for this is that a design pattern came up at work this week, namely the Singleton Class Design Pattern.

This design pattern is pretty straight forward, but if you don't know what it is or want a quick refresher: 
###Get. Ready.

![](http://i.giphy.com/3oD3YFdLN5TK5mLVjW.gif)

# What *is* a Singleton Class? 
The purpose of the Singleton Class is (** **SPOILER ALERT** **) to create only one globally available instance of itself and ensure that no other instances are created.  

#####Pro-tip: Ruby actually has a [Singleton Module](http://ruby-doc.org/stdlib-1.9.3/libdoc/singleton/rdoc/Singleton.html) you can use to implement this pattern. 
<!--![](http://i.giphy.com/Q6gPyUYrCk76g.gif)-->

# Why should you use this design pattern? 
Sometimes you need a class with exactly one instance. A prime example of this would be a creating a Logger singleton, if you wanted all your application's logging to go through that only Logger. Or you may also need to have only one instance of your Configuration Class. 

The Gang of Four also documents a few benefits of using this pattern, including: 

* increased control over the one instance of the Singleton Class
* it allows one to avoid polluting the global namespace
* you can totally change your mind and have more than one instance of the class (weird, I know)

## *****Important Note*****
Some programmers really hate this pattern--indeed it seems like it may be the Ramsay Bolton of Design Patterns. Here are some reasons: [Why Singletons Are Evil](https://blogs.msdn.microsoft.com/scottdensmore/2004/05/25/why-singletons-are-evil/). 

![](http://i.giphy.com/3o7qDWZchVN4R4X5zG.gif)

# How do you use this pattern? 
If you're writing in Ruby, you can use the Singleton Module like this:

```ruby
require 'singleton'

class Throne 
 include Singleton
end 
```
And Ruby, being the cool thang that it is, won't allow you to instantiate an new instance of you Singleton class using the `new` method. If you do, you get this:

```ruby
Throne.new
NoMethodError: private method `new' called for Throne:Class
```
The `new` method is private, which is good for a Singleton, given you don't want to instantiate an instance willy nilly. 


To instantiate a new class, you require the `instance` method the Singleton module gives you:

```ruby
Throne.instance

=> #<Throne:0x007fdd8a401b30>
```

# So... what now? 
Well, this is a rather controversial design pattern. It seems that ever since the Gang of Four wrote about it, it has been criticized. Indeed many of my Google searches prepping for this post yielded results on how to *avoid* using this pattern. One reason for this seems to be that it's difficult to test. They also violate the principle of single responsibility and lead to tightly coupled code.

Despite this, however, you'll probably come across this design pattern (just as I did this week!), and it's important to know what it is and why it is used. 

In future weeks I hope to dig deeper into other patterns that aren't so controversial. :) 

Now--for Game of Thrones!

Happy Sunday! 

![](http://i.giphy.com/3oEjHGlyH5CGNxWjxm.gif)
