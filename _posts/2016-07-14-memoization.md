---
layout: post
title:  "Caching the Result: An Intro to Memoization"
date:   2016-07-17 19:00:00 -0400
categories: 
---
This past week I was reviewing code, and I came across a test that included the word "memoize." My first reaction was:

![](http://i.giphy.com/l2R05FZf4dVOrgIaQ.gif)

Then I thought, that's a typo, but given that I'm still quite new to programming (and the test didn't have anything to do with memorization), I decided to google "memoize." And imagine my surprise when I realized that it's actually a computer science concept. 

# Memoization: Memory for Functions
Memoization is a way of caching the results of time-consuming/resource-expensive functions (think: database calls, API calls, or complex calculations). It's helpful at speeding things up. Basically, it keeps you from being wasteful.

![](http://i.giphy.com/9N65Uk16bMubK.gif)

## Simple Ruby Memoization 
Let me demonstrate with a simple example. Let's assume that I have an application that makes a database call to determine the number of Emmy nominations received by a cast member of the show 'Unbreakable Kimmy Schmidt'. 

It may look something like this:

```ruby
class KimmySchmidtCastMember < ActiveRecord::Base
 
  def initialize(name)
    @name = name
  end

  def emmy_nominations
    @emmy_nominations ||=  actor.emmy_nomination  #=> MEMOIZATION
  end
end
```

By the by, using `||=` essentially equals this line:

`@emmy_nominations =  @emmy_nominations || actor.emmy_nomination`

By using this you are telling the method to only make the call `actor.emmy_nomination` if `@emmy_nominations` **doesn't** exist (has a falsy value). If the call has already been made, then the results are stored in the instance variable `@emmy_nominations`, and the method will return this cached result the next time it's called. 


![](http://i.giphy.com/18pjPEqqIt2k8.gif)


# Issues with Memoization

Memoization is not perfect, and there are a few downsides to be aware of:

### 1. Clearing the cache once it's set may not be straightforward.

For example, let's say we have Ellie Kemper and Tituss Burgess as instances of the class `KimmySchmidtCastMember`, and our database reflects their most recent Emmy nominations:

```ruby
Ellie = KimmySchmidtCastMember.new('Ellie Kemper')
=> #<KimmySchmidtCastMember:0x007fdb93a7c440 @name="Ellie Kemper">

Ellie.emmy_nominations
=> 1

Tituss = KimmySchmidtCastMember.new('Tituss')
=> #<KimmySchmidtCastMember:0x007fdb93aa7a00 @name="Tituss">

Tituss.emmy_nominations
=> 1
```

Now, let's pretend we're Timelords, and we go to July 17th, 2017 to find that both Ellie Kemper and Tituss Burgess get nominated again (!) for Emmys. We've already made the call that set both their `emmy_nomination` values to 1, though, and running the code again will only yield that cached value.

 ![](http://i.giphy.com/l3UcBlonRH3ImaZ0Y.gif)

### 2. The Conditional Assignment Operator ||= will not cache falsey values.
 
The `||=` operator is great for memoization because it won't reassign a variable after it's been assigned, but if the method returns `nil` or `false`, that result will never be cached. Therefore, the method will run again when it's called until the method returns a truthy value. For example:

```ruby
Anna = KimmySchmidtCastMember.new('Anna Camp')

Anna.emmy_nominations
=> nil
```

But say that you keep on making this call, or perhaps you keep making a call to an Emmy Nominations API that returns `nil`. This could get fairly expensive, thus the assignment of `nil` can really be problematic. 

# Let there be gems!
![](http://i.giphy.com/gdI59W5F10yu4.gif)

Given that memoization comes with some inherent disadvantages, Rails addressed these disadvantages in ActiveSupport::Memoizable, but this was later deprecated in v3.2.13. 

To take it's place was the [memoist gem](https://rubygems.org/gems/memoist), which was an extraction of ActiveSupport::Memoizable and addresses the issues I've presented here. 

The basic syntax looks like this:

```ruby
require 'memoist'
class KimmySchmidtCastMember < ActiveRecord::Base
 
  def initialize(name)
    @name = name
  end

  def emmy_nominations
    @emmy_nominations =  actor.emmy_nomination  
  end
  memoize :emmy_nominations
end
```

By adding `memoize :emmy_nominations`, the method `emmy_nominations` will only be called once. 

## Advantages of Memoist

### Memoize Methods with Params

```ruby
require 'memoist'
class KimmySchmidtCastMember < ActiveRecord::Base
 
  #...
  
  def salary(pay_by_episode, num_of_episode)
  	pay_by_episode * num_of_episode
  end
  memoize :salary
end
```
If we calculate Ellie's salary with one set of parameters, the result associated with those parameters will be cached. However, when you call `salary` with different parameters, the result *will* be recalculated. 

```ruby
ellie = KimmySchmidtCastMember.new('Ellie')
=> #<KimmySchmidtCastMember:0x007fa24b995440 @name="Ellie">

ellie.salary(20_000, 11) #This result with the given parameters is cached
=> 22000

ellie.salary(20_000, 11) #Won't be recalculated
=> 220000  

ellie.salary(30_000, 11) #Will be recalculated
=> 330000
```

This is a rather simple example, but it's easy to see how caculations could get time-consuming. 

### Easily Clear the Cache

Going back to my previous example of time traveling into the future, say you wanted to recalculate Ellie's Emmy nominations next year. All you would need to do is pass in `true` when calling the method on the object:

```ruby
Ellie.emmy_nominations(true)
=> 2  #New hypothetical Emmy nominiations for 2017

```
The gem also allows you to completely remove all the memoized values by calling `flush_cache` on the object:

```ruby
ellie.flush_cache
```

# Final Thoughts
Memoization can be very powerful, especially when you want to make sure that you're not making costly duplicate calls to the database. This subject is much deeper than what I've presented here, but with time I hope to find new examples and tricks to better explain memoization. 

### Thanks for reading, folks! And remember:

![](http://i.giphy.com/xT8qBnozlgVpTsU1gY.gif)
