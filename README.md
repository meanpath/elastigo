
meanpath fork
-------------

* Adds support fields when they're requested individually.
* Fixes RegisterPercolate and Percolate methods for single percolate requests and registration.
* Fixes the Exists method.
* Implements Scan support for SearchRequest and SearchUri.
* Calls percolate on bulk index queries so new indexes can be checked against registered percolates
* Adds a NodeStats function returning a thread pool status for the ES cluster to enable endpoint selection

Original readme follows.

elastigo
========

Golang based Elasticsearch client, implements core api for Indexing and searching.   GoDoc http://godoc.org/github.com/mattbaird/elastigo

To get the Chef based Vagrantfile working, be sure to pull like so: 

git clone --recursive git@github.com:mattbaird/elastigo.git

This will pull submodules.

status updates
========================

* *2013-7-10* Improvments/changes to bulk indexor (includes breaking changes to support TTL), 
         Search dsl supports And/Or/Not   
    * *SearchDsl* should still be considered beta at this 
         point, there will be minor breaking changes as more of the 
         elasticsearch feature set is implemented.
* *2013-1-26* expansion of search dsl for greater coverage
* *2012-12-30* new bulk indexing and search dsl
* *2012-10-12* early in development, not ready for production yet.



Search Examples
-------------------------

A Faceted, ranged Search using the `Search DSL` :

    out, err := Search("github").Size("1").Facet(
      Facet().Fields("actor").Size("500"),
    ).Query(
      Query().Range(
         Range().Field("created_at").From("2012-12-10T15:00:00-08:00").To("2012-12-10T15:10:00-08:00"),
      ).Search("add"),
    ).Result()
   
A Ranged Search using the `Search DSL` :
   
    out, err := Search("github").Type("Issues").Pretty().Query(
      Query().Range(
         Range().Field("created_at").From("2012-12-10T15:00:00-08:00").To("2012-12-10T15:10:00-08:00"),
      ).Search("add"),
    ).Result()
   
A Simple Search using the `Search DSL` :

    out, err := Search("github").Type("Issues").Size("100).Search("add").Result()


A Direct Search using the api :
   
    qry := map[string]interface{}{
      "query":map[string]interface{}{
         "term":map[string]string{"user:"kimchy"},
      },
    }
    core.SearchRequest(true, "github", "Issues", qry, "", 0)

A Direct Search using the query string Api :
   
    core.SearchUri("github", "Issues", "user:kimchy", "", 0)

A Filtered search `Search DSL` :
   
    out, err := Search("github").Filter(
      Filter().Exists("repository.name"),
    ).Result()


Adding content to Elasticsearch
----------------------------------------------

examples:
    
    // add single go struct entity
    response, _ := core.Index(true, "twitter", "tweet", "1", NewTweet("kimchy", "Search is cool"))

    // you have bytes
    bytesLine, err := json.Marshall(tw)
    response, _ := core.Index(true, "twitter", "tweet", "2", bytesLine)

    // Bulk Indexing 
    core.IndexBulk("twitter", "tweet", "3", &time.Now(), NewTweet("kimchy", "Search is now cooler"))


license
=======
    Copyright 2012 Matthew Baird, Aaron Raddon, and more!

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
