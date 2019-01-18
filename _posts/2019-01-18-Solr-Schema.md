---
layout : post
title : Solr Schema
comments: true
categories : Java
---

Solr 7.6

### Document

`document` just like the entity of a database table, while `field` is similar the column of a table.

### Schema

> Solr’s schema is a single file (in XML) that stores the details about the fields and field types Solr is expected to understand. 
> It describes the documents you will ask Solr to index. The Schema define a document as a collection of fields.
> The schema defines not only the field or field type names, but also any modifications that 
> should happen to a field before it is indexed. 

It is stored in file `managed-schema.xml`.

#### _root_

#### _version_

#### uniqueKey

#### copy filed

#### dynamic field

### Reference

[Apache Solr Reference Guide](https://lucene.apache.org/solr/guide/7_6/index.html)
