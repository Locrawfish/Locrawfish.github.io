---
layout: post
title:  "The Trials and Tribulations of Altering a Big Database
"
date:   2016-08-21 19:00:00 -0400
categories: 
---

Before starting my first (and current) job as a web developer, I had no experience with older databases. My experience mostly came from generating migrations in Rails for a relatively small database. 

I have now gained some solid experience working with a **BIG** database, and I can say that they can be quite ornery and problematic.

This blog post centers around a particular issue I was tasked with addressing, namely determining how to do a database migration with our biggest table. 

## What's a Rails Migration?

Some of you reading may have worked with Rails, which means you’ve probably done this in one fashion or another:

```ruby 
bin/rails generate migration SomethingClever name:string 
```
And then: 

```ruby 
rails db:migrate
```
### If you're thinking... 

![](http://i.giphy.com/hmgLbLVuvUti.gif)

#### Allow me to explain (briefly).  

`rails generate migration` creates an ActiveRecord::Migration that gives directions on how to change a schema in the database, and `db:migrate` actually carries out that change. 

For example, say I have a table full of 90s shows. This table has many columns, among them is a column for a TV channel. But, if I'm honest, I don't even know what a TV channel is anymore, so I decide to remove it from the table. I could go about this by creating a simple migration like this:

```ruby 
bin/rails generate migration RemoveChannelFromShows channel:integer
```
This lovely little command then generates a class and a `change` method: 

```ruby
class RemoveChannelFromShows < ActiveRecord::Migration[5.0]
  def change
    remove_column :shows, :channel, :integer
  end
end
```

Next, by running `rails db:migrate` the magic of `ActiveRecord::Migration` actually changes the schema, and the channel column in my shows table will be removed in the database.

![](http://i.giphy.com/d2Z0bXYnj4Ma5VxC.gif)

### Want to know more? [Here ya go!](http://guides.rubyonrails.org/active_record_migrations.html) ;) 


## Problem: Your table has a bagillion records and you need to change something

Rails migrations work well, but if your tables are really big and you'd like to change things, the 	mySQL underneath can get a little sticky.

![](http://i.giphy.com/hR6SWNCMoRaFi.gif)

How, you ask, does it get sticky? Well, for me I discovered that whilst running a migration (think changing the table schema), mySQL actually creates a temporary table of the original table*. This temporary copy is stored in a temporary file directory, but the sticky part comes when that tmp directory doesn't have enough space for the temporary copy. Simply, if it doesn't have the space, you can't run the migration. 



##### *Sidenote: this is pretty sweet because it allows you to edit the copy, while the original table is still readable by other sessions. 


## mySQL Variables 
It may be of interest to you to know that mySQL has two types of variables: session and global. Session variables can be set just for the mySQL session that you are currently in 


```
	--innodb_tmpdir=path
```

### Pro tip: [documentation is your friend.](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_tmpdir)


### Have a fabulous week, friends!

![](http://i.giphy.com/10LNj580n9OmiI.gif)

![](http://i.giphy.com/lfHyE7kWURQNW.gif)
