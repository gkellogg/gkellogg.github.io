---
id: 42
title: Eager Finder SQL
date: 2006-11-06T11:31:22+00:00
author: gregg
layout: post
guid: http://wp.greggkellogg.railsplayground.net/wordpress/?p=42
permalink: /2006/11/eager-finder-sql/
categories:
  - Ruby
---
_EagerFinderSql_ allows custom SQL to be specified when doing eager loading of associations through the :include option to find. This allows for purpose-constructed queries to be used and still result in a fully linked object model.

### Background

**ActiveRecord** constructs SQL to satisfy the requirements of a find request. _Associations_ allow for customized SQL to be specified, using the :finder_sql option, but this has not been available when performing eager loading using the :include option. The result is that a standardized query is constructed to bring in the associated tables using `LEFT OUTER JOIN`. For some queries, this can be result in expensive queries and potentially very large result sets.

### Custom SQL

_EagerFinderSql_ addresses this problem by allowing :finder\_sql to be added to find options when the :include option is also specified. Columns in the result set are mapped to attributes in the resulting object model through the :column\_mapping option. The :column_mapping option specifies a hash containing the following entries:

  * primary_key &#8211; indicates the column alias associated with the classes _id_ attribute.
  * columns &#8211; is a hash of attribute to column alias associations.
  * associations &#8211; is a hash of association mappings for each directly included model. The _key_ for each entry is the name of the association, while the value is a hash similar to the hash for the parent class.

### Example

Consider the following query relating authors with many books:

    Author.find(:all, :include => :books)
    

This would likely produce the following SQL:

    SELECT
        authors.id AS t0_c0, authors.name AS t0_c1,
        books.id AS t1_c0, books.author_id AS t1_c1, books.title AS t1_c2
      FROM authors
      LEFT OUTER JOIN books ON books.author_id = authors.id
    

The query might be written with books as the driver, rather than authors as follows:

     SELECT
        authors.id, authors.name,
        books.id AS book_id, books.name AS book_name
      FROM books
      JOIN authors ON authors.id = books.author_id
    

The Rails query would then be written as follows:

    Author.find(:all, :include => books,
               :finder_sql => "
                    SELECT
                        authors.id, authors.name,
                        books.id AS book_id, books.name AS book_name
                      FROM books
                      JOIN authors ON authors.id = books.author_id".
               :column_mapping => {
                 :primary_key => 'id',
                 :columns => {
                      'id'  => 'id',
                      'name => 'name'
                  },
                  :associations => {
                    :books => {
                      :primary_key => book_id,
                      :columns => {
                          'id'        => 'book_id',
                          'author_id' => 'id',
                          'name'      => 'book_name
                      }
                    }
                  }
                })
    

This is more verbose, but allows for absolute control of the SQL used to return results across multiple model associations. A more complicated example would be the following:

    QUERY = "
    SELECT
        a.id AS author_id,
        a.name AS author_name,
        p.id AS post_id,
        p.title AS post_title,
        p.body AS post_body,
        p.type AS post_type,
        c.id AS comment_id,
        c.body AS comment_body,
        c.type AS comment_type
      FROM authors a
      LEFT JOIN posts p ON p.author_id = a.id
      LEFT JOIN comments c ON c.post_id = p.id"
    
    MAPPING = {
      :primary_key => 'author_id',
      :columns => {
        'id' => 'author_id',
        'name' => 'author_name'
      },                         
      :associations=> {
        :posts => {
          :primary_key  => 'post_id',
          :columns => {
            'id' => 'post_id',
            'title' => 'post_title',
            'author_id' => 'author_id',
            'body' => 'post_pody',
            'type' => 'post_type'
          },
          :associations => {
            :comments => {
              :primary_key  => 'comment_id',
              :columns => {
                'id' => 'comment_id',
                'post_id' => 'post_id',
                'author_id' => 'author_id',
                'body' => 'comment_body',
                'type' => 'comment_type'
              }
            }
          }
        }
      }
    }
    Author.find(:all,
                :include=>{:posts=>:comments},
                :order=>"authors.id",
                :finder_sql => QUERY,
                :column_mapping => MAPPING)
    

This example shows the recursive nature of associations. It can also be used to render deeper structures that may reference the same model multiple times, such as the following:

    Firm.find(:all,
              :include=>{:account=>{:firm=>:account}},
              :order=>"companies.id")
    

(Here, the query and mapping is left to the reader; or, you can look at the unit test which documents many more possibilities). Install using

script/plugin install svn://rubyforge.org/var/svn/eagerfindersql

The plugin is managed on [RubyForge](http://rubyforge.org/projects/eagerfindersql/). [Rdoc](/rdoc/eager_finder_sql/index.html) is available.