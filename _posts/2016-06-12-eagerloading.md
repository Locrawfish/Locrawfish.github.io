---
layout: post
title:  "A Brief Intro to Eagerloading in Rails"
date:   2016-06-12 12:00:35 -0400
categories: 
---


ActiveRecord is the Rails component that takes care of all of your database needs. It's pretty spiffy, but a little complex. I'm still uncovering what ActiveRecord can do, as well as how generally awesome it is. One of the cool things I discovered this week is the different types of eager loading.

## What's Eager Loading?
Essentially, it allows you to grab a lot of data at once instead of making a lot of individual queries.

This is important because ActiveRecord in Rails defaults to lazy loading when grabbing data from your database. 

For example, if you had a Rails application that cataloged recipes and bakers and wanted to grab recipes along with the bakers who created them, your query would look like this:

```
Started GET "/recipes" for ::1 at 2016-06-12 13:51:09 -0400
Processing by RecipesController#index as HTML
  Recipe Load (0.4ms)  SELECT "recipes".* FROM "recipes"  ORDER BY "recipes"."created_at" DESC
  Baker Load (0.1ms)  SELECT  "bakers".* FROM "bakers" WHERE "bakers"."id" = ? LIMIT 1  [["id", 4]]
  Baker Load (0.1ms)  SELECT  "bakers".* FROM "bakers" WHERE "bakers"."id" = ? LIMIT 1  [["id", 3]]
  Baker Load (0.1ms)  SELECT  "bakers".* FROM "bakers" WHERE "bakers"."id" = ? LIMIT 1  [["id", 2]]
  Rendered recipes/index.html.erb within layouts/application (5.7ms)
Completed 200 OK in 20ms (Views: 19.2ms | ActiveRecord: 0.7ms)
```

As you can see, you have indidivual queries, which is okay if you only have a handful of things to grab and display, but what if you had 1,000 recipes or 2,000? Loading them lazily decreases performance.

Which could end up sort of like this:

![](https://67.media.tumblr.com/f26941cd5e8379771dcba73098a749fe/tumblr_nd57ifxaeW1rs8ltio1_500.gif)

This is where eager loading comes in. 

For example eager loading with `includes` executes only two queries to grab all of the data, instead of individual queries for each baker: 

```ruby
def index
  @recipes = Recipe.order(created_at: :desc).includes(:baker)
end
```

```
Started GET "/recipes" for ::1 at 2016-06-12 13:56:02 -0400
Processing by RecipesController#index as HTML
  Recipe Load (0.3ms)  SELECT "recipes".* FROM "recipes"  ORDER BY "recipes"."created_at" DESC
  Baker Load (0.5ms)  SELECT "bakers".* FROM "bakers" WHERE "bakers"."id" IN (4, 3, 2)
  Rendered recipes/index.html.erb within layouts/application (12.7ms)
Completed 200 OK in 39ms (Views: 35.3ms | ActiveRecord: 1.3ms)
```

## Types of Eager Loading
So now what type of eager loading should you choose? 


#### preload()
`preload()` makes separate queries for data. For example, in my recipes and bakers example using this code:

```ruby
def index
  @recipes = Recipe.order(created_at: :desc).preload(:baker)
end
```
Will result in this:

```
Started GET "/recipes" for ::1 at 2016-06-12 13:53:29 -0400
Processing by RecipesController#index as HTML
  Recipe Load (0.6ms)  SELECT "recipes".* FROM "recipes"  ORDER BY "recipes"."created_at" DESC
  Baker Load (0.2ms)  SELECT "bakers".* FROM "bakers" WHERE "bakers"."id" IN (4, 3, 2)
  Rendered recipes/index.html.erb within layouts/application (33.4ms)
Completed 200 OK in 69ms (Views: 62.7ms | ActiveRecord: 1.5ms)
```
As you can see, `preload()` loads the recipes and then the bakers. 

#### eager_load()

`eager_load` is similar, but utilizes a `LEFT OUTER JOIN` table under the hood which results in only ONE query, not two like `preload()`:

```
Started GET "/recipes" for ::1 at 2016-06-12 13:55:14 -0400
Processing by RecipesController#index as HTML
  SQL (0.2ms)  SELECT "recipes"."id" AS t0_r0, "recipes"."title" AS t0_r1, "recipes"."baker_id" AS t0_r2, "recipes"."ingredients" AS t0_r3, "recipes"."directions" AS t0_r4, "recipes"."created_at" AS t0_r5, "recipes"."updated_at" AS t0_r6, "bakers"."id" AS t1_r0, "bakers"."name" AS t1_r1, "bakers"."created_at" AS t1_r2, "bakers"."updated_at" AS t1_r3 FROM "recipes" LEFT OUTER JOIN "bakers" ON "bakers"."id" = "recipes"."baker_id"  ORDER BY "recipes"."created_at" DESC
  Rendered recipes/index.html.erb within layouts/application (21.7ms)
Completed 200 OK in 58ms (Views: 53.9ms | ActiveRecord: 1.0ms)
```
 
A benefit of the `LEFT OUTER JOIN` table is that it allows you to sort based on information in the other table (here the bakers table). 

#### includes()
`includes()` allows you to grab additional data on a particular model without executing an additional query. It is Rails' smarty britches eager loading method, because it delegates eager loading to either `eager_loading()` or `preload()` depending on what would provide the best performance. 

For example, using includes here:

```ruby
def index
  @recipes = Recipe.order(created_at: :desc).includes(:baker)
end
```
Resulted in a `preload` being utilized, as you can see that 

```
Started GET "/recipes" for ::1 at 2016-06-12 13:56:02 -0400
Processing by RecipesController#index as HTML
  Recipe Load (0.3ms)  SELECT "recipes".* FROM "recipes"  ORDER BY "recipes"."created_at" DESC
  Baker Load (0.5ms)  SELECT "bakers".* FROM "bakers" WHERE "bakers"."id" IN (4, 3, 2)
  Rendered recipes/index.html.erb within layouts/application (12.7ms)
Completed 200 OK in 39ms (Views: 35.3ms | ActiveRecord: 1.3ms)
```



### Happy Sunday, folks!


![](http://i.giphy.com/l65tlPFwZXacU.gif)