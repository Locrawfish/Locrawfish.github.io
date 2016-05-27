---
layout: post
title:  "ActiveRecord Scope: What is it and how to use it"
date:   2016-05-22 18:00:35 -0400
categories: 
---

Since starting my adventure into programming the term scope has collected more meaning for me, specifically how variables cannot be called outside of their scope. This blog post isn't about that type of scope though, rather it's about the Rails Active Record scope. 

Honestly, I am rather surprised that it has taken me this long to come across scope enough times to actually ask what it is and how I should use it--perhaps this is one of the perks of working on a legacy codebase. Writing my own web apps never got me to push this far into ActiveRecord queries, so for all of you interested in learning about scopes, stay tuned! 

## Scopes are Sweet Little Query Shortcuts: The Tale of Leslie Scope
Say you have a database filled with Parks and Rec quotes. Naturally, you frequently call upon this database to return to you Leslie Knope quotes. So what do you do? You could create a class method:

```ruby
class Quote << ActiveRecord::Base
  def self.leslie
    where(character: 'Leslie Knope')
  end
end
```
Okay--that would work. To get Leslie Knope quotes you could then call:

```ruby
Quote.leslie => 'Why would anyone ever eat anything besides breakfast food?! ...'
```

Buuuuut it's rather long, isnt' it? Allow me to show you the Leslie Scope way (hehe):

```ruby
class Quote <  ActiveRecord::Base
  scope :leslie, -> { where(character: 'Leslie Knope') }
end
```

LOOK. AT. THAT. Three lines down to one (whaaat??). You don't have to write a class method. It looks pretty rad, which I believe Leslie would agree with too.  

## What else can Leslie Scope Do?
Well, a lot. Scopes are chainable suckers, which makes them darn useful. For example, if you wanted quotes from April about Jerry, you could do this:

```ruby
class Quote < ActiveRecord::Base
  scope :april, -> { where(character: 'April Ludgate') }
  scope :jerry, -> { where(quote_topic: 'Jerry') }
end

Quote.april.jerry => 'Can you photoshop your life with better decisions, Jerry?! ...' 
```

![](http://lovelace-media.imgix.net/uploads/75/0985b010-75b0-0132-4378-0ebc4eccb42f.gif?)

Also, you can have default scopes that are called on the reg: 

```ruby
class Quote < ActiveRecord::Base
  default_scope { where show: 'Parks and Rec'}
end
```

**Default scope are put before any other scopes you call.**

Another thing about scopes is that you can pass stuff into them! Like this:

```ruby
class Quote < ActiveRecord::Base
   scope :season, -> (season) { where("season = ?", season) }
end
```

Pretty cool, huh? So next time you want to run some simple WHERE or LIMIT queries, think about using scope. 

That's all folks! :) 

## Resources 
* [Rails Guide](http://guides.rubyonrails.org/active_record_querying.html#scopes)
* [Should You Use Scopes or Class Methods? by Justin Weiss](http://www.justinweiss.com/articles/should-you-use-scopes-or-class-methods/)



![](https://media.giphy.com/media/2AilMg2L8rTAA/giphy.gif)
