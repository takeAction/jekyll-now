---
layout : post
title : Solr Get Updated Data
comments: true
categories : Java
---

In the [Apache Solr MySql Hello World](https://takeaction.github.io/Apache-Solr-MySql-Hello-World/), we import data from MySQL to Solr
which is `full import`, that is import all data from DB.

However, we just need to import updated data to Solr in following cases:

1. if there is new record inserted into the MySQL
2. one record is updated in DB
3. one record is deleted

We want Solr insert/update/delete index if there is changes in DB.

This is called `Delta Import` in Solr.

### Delta Import

In order to achieve delta import, we need to modify `data-config.xml` which is located in `<solr home>/server/solr/<core name>/conf/`.

Change it to following:

```xml
<dataConfig>

  <dataSource type="JdbcDataSource" driver="com.mysql.cj.jdbc.Driver"
    url="jdbc:mysql://localhost:3306/test" user="root" password="123456"/>
  <document>
  
    <entity name="CUSTOMER" pk="CUSTOMER_ID"
            query="select CUSTOMER_ID, CUSTOMER_CODE, NAME from CUSTOMER"
            deltaImportQuery="select CUSTOMER_ID, CUSTOMER_CODE, NAME from CUSTOMER where CUSTOMER_ID='${dih.delta.CUSTOMER_ID}'"
            deltaQuery="select CUSTOMER_ID from CUSTOMER where update_datetime &gt; '${dih.last_index_time}'"
            >      
    </entity>
  </document>
</dataConfig>
```

Then execute `delta import`:

![_config.yml]({{ site.baseurl }}/images/solr-delta-import.png)

If success, the GUI will show:

```
Indexing completed. Added/Updated: 1 documents. Deleted 0 documents.
Requests: 2 , Fetched: 2 , Skipped: 0 , Processed: 1 
```

#### Commit

The `commit` of above screenshot has to be checked, otherwise we cannot query the updated data in Solr.

For example, we update `name` from `abc` to `123` in DB, then do delta import without `commit` checked.

Then query with `name:123`, solr will return no result.

#### Do delta import from code

`Delta Import` also can be done from code like:

```java
DefaultHttpClient httpClient = new DefaultHttpClient();
HttpPost httpPost = new HttpPost("http://localhost:8983/solr/helloworld/dataimport?command=delta-import&commit=true");

httpPost.addHeader(HTTP.CONTENT_TYPE, "application/json");	
HttpResponse response = httpClient.execute(httpPost);

HttpEntity entity = response.getEntity();

String body = EntityUtils.toString(entity);

httpClient.getConnectionManager().shutdown();

return body;
```

#### dataimport.properties

There is `dataimport.properties` under the `conf` folder.

```
#Wed Jan 16 07:12:33 UTC 2019
last_index_time=2019-01-16 07\:12\:32
CUSTOMER.last_index_time=2019-01-16 07\:12\:32
```

If `full import` or `delta import` success, Solr will update the `last_index_time` in it.

##### last_index_time not updated

If `deltaImportQuery` is
`deltaImportQuery=select CUSTOMER_ID, CUSTOMER_CODE, NAME from CUSTOMER where CUSTOMER_ID='${dih.delta.customer_id}'`,
but the field `column` and `name` in `data-config.xml` are `CUSTOMER_ID`, then `delta import` will not throw any error/warning,
just the `last_index_time` not be updated.

### data-config.xml

#### dataSource

It describes the data source whose data will be imported to Solr.

##### type

`type` is optional. The default value is `JdbcDataSource`.

##### name

The attribute `name` can be used if there are multiple datasources used by multiple entities.

##### password

The `password` attribute is optional if there is no password set for the DB.

Alternately, the password can be encrypted,
 
https://lucene.apache.org/solr/guide/7_6/uploading-structured-data-store-data-with-the-data-import-handler.html#encrypting-a-database-password
describes how to do this.

#### entity

For a RDBMS data source, an entity is a view or table, which would be processed by one or more SQL statements to
generate a set of rows (documents) with one or more columns (fields).

> Note that entity elements can be nested, and this allows the entity relationships in the sample database to be mirrored here,
> 
> so that we can generate a denormalized Solr record which may include multiple features for one item, for instance.

##### pk 

It is optional and only needed when using delta-imports.

##### deltaQuery

It is used in `delta import` which is used to get id from table when `update_datetime` great than `last_index_time`.

##### deltaImportQuery

It is used in `delta import` and select all the updated records according to the id which from `deltaQuery`

##### field

`<field column="CUSTOMER_ID" name="CUSTOMER_ID"/>`.

`field` elements, which map the data source field names to Solr fields, and optionally specify per-field transformations.

If the column name is different from the solr field name(case does not matter), then another attribute name should be given.

### References

[Solr Ref Guide](https://lucene.apache.org/solr/guide/7_6/index.html)

[DateImportHandler](https://wiki.apache.org/solr/DataImportHandler)
