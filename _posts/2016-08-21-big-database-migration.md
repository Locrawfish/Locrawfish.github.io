---
layout: post
title:  "Large Migrations: Rails, MySQL, and A Big Database"
date:   2016-08-25 19:00:00 -0400
categories: 
---

Before starting my first (and current) job as a web developer, I had no experience with older databases. My experience mostly came from generating migrations in Rails for a relatively small database. 

I have now gained some solid experience working with a **big** database, and I can say that they can be quite ornery and problematic.

This blog post centers around a particular issue I was tasked with addressing, namely determining how to do a Rails migration with our biggest table. 

## What's a Rails Migration?

Some of you reading may have worked with Rails, which means you’ve probably done this in one fashion or another:

```ruby 
bin/rails generate migration AddSomethingCleverToATable something_clever:string 
```
And then: 

```ruby 
rails db:migrate
```

### If you're thinking... 

![](http://i.giphy.com/hmgLbLVuvUti.gif)

#### Allow me to explain (briefly).  

`rails generate migration` creates an `ActiveRecord::Migration` that gives directions on how to change a schema in the database, and `db:migrate` actually carries out that change. 

For example, say I have a table full of 90's shows. This table has many columns, among them is a column for a TV channel. But, if I'm honest with myself, I don't even know what a TV channel is anymore, so I decide to remove it from the table. I could go about this by creating a simple migration like this:

```ruby 
bin/rails generate migration RemoveChannelFromShows channel:integer
```
This lovely little command generates a class and a `change` method describing the removal of the channel column from the table of shows: 

```ruby
class RemoveChannelFromShows < ActiveRecord::Migration
  def change
    remove_column :shows, :channel, :integer
  end
end
```

Rails allows developers to write all this code in Ruby, but ultimately converts it into raw SQL to make changes to the database. So, by running `rails db:migrate` the magic of `ActiveRecord::Migration` actually changes the schema, and the channel column in my shows table will be removed in the database.

![](http://i.giphy.com/d2Z0bXYnj4Ma5VxC.gif)


## Problem: Your table has a bagillion records and you need to change something

Let's say that my parents let me watch A LOT of TV and my shows table takes is a really big table, but I still want to remove that channel column. I could use a standard Rails migration, but I discovered that whilst running a migration, MySQL under the hood actually creates a temporary copy of the original table that can be read while the original table is altered. This temporary copy is stored in a temporary file directory, but this causes a problem when that temporary directory doesn't have enough space for the temporary copy. Simply put, if it doesn't have the space to fit the copy of the table you want to change, you can't run the migration. 

![](http://i.giphy.com/3o6gbdvDgqBrsDX52o.gif)

# Solution: Bypass Rails and Use Raw SQL

So in the midst of despair I turned to the only thing I could turn to: [DOCUMENTATION](https://dev.mySQL.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_tmpdir)!

What the documentation revealed is that MySQL 5.7.11 has the variable `innodb_tmpdir` that allows you to point temporary files that are created by an ALTER TABLE operation to another directory during a particular MySQL session. It looks a bit like this:

```
ALTER TABLE shows SET SESSION innodb_tmpdir=/the/path/to/another/dir
DROP COLUMN channel
```

## Why is this a good solution?

This option is great because it's not a permanent change, like setting the `tmpdir` global variable, because let's face it, you may not have that option. It also allows your application to continue to read from the temporary table while the original table is altered, and ensures that your table won't lock.


### Thanks for reading, folks!


## Want to know more? I got you covered:

### [Rails migrations](http://guides.rubyonrails.org/active_record_migrations.html) 

### [Session vs Global variables in MySQL](https://dev.mysql.com/doc/refman/5.7/en/using-system-variables.html)

![](http://i.giphy.com/10LNj580n9OmiI.gif)
