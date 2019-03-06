---
title: Active Record and PostgreSQL 字段为json
layout: post
date: '2019-03-06 11:05:25'
categories: postgresql
---

[参考链接：](https://guides.rubyonrails.org/active_record_postgresql.html#hstore)

This guide covers PostgreSQL specific usage of Active Record.

* How to use PostgreSQL's datatypes. 如何使用PostgreSQL字段的数据类型
* How to use UUID primary keys. 生成uuid主见
* How to implement full text search with PostgreSQL.  如何实现文本搜索
* How to back your Active Record models with database views. 

1.字段设置为json类型
```
# db/migrate/20131220144913_create_events.rb
# ... for json datatype:
create_table :events do |t|
  t.json 'payload'
end
# ... or for jsonb datatype:
create_table :events do |t|
  t.jsonb 'payload'
end
 
# app/models/event.rb
class Event < ApplicationRecord
end
 
# Usage
Event.create(payload: { kind: "user_renamed", change: ["jack", "john"]})
 
event = Event.first
event.payload # => {"kind"=>"user_renamed", "change"=>["jack", "john"]}
 
## Query based on JSON document
# The -> operator returns the original JSON type (which might be an object), whereas ->> returns text
Event.where("payload->>'kind' = ?", "user_renamed")
```
2.字段为array
   
 ```
# db/migrate/20140207133952_create_books.rb
create_table :books do |t|
t.string 'title'
t.string 'tags', array: true
t.integer 'ratings', array: true
end
add_index :books, :tags, using: 'gin'
add_index :books, :ratings, using: 'gin'

# app/models/book.rb
class Book < ApplicationRecord
end

# Usage
Book.create title: "Brave New World",
					tags: ["fantasy", "fiction"],
					ratings: [4, 5]

## Books for a single tag
Book.where("'fantasy' = ANY (tags)")

## Books for multiple tags
Book.where("tags @> ARRAY[?]::varchar[]", ["fantasy", "fiction"])

## Books with 3 or more ratings
Book.where("array_length(ratings, 1) >= 3")
```