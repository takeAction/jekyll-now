---
layout : post
title : Apache Solr MySql Hello World
comments: true
categories : Java
---

### Environment

  Windows 10, Apache Solr 7.6.0
  
### Steps

#### Download

  Download and unzip solr, e.g. **D:/solr-7.6.0**
  
#### Start Solr

  Execute `solr start` in `D:/solr-7.6.0/bin`
  
#### Solr Admin UI

  Open admin UI by `http://localhost:8983/solr`. Now it looks like:
  
  ![_config.yml]({{ site.baseurl }}/images/solr-1.png)
  
#### Create Core

  Click `Core Admin` to add a core, core name is **helloworld**.
  
  ![_config.yml]({{ site.baseurl }}/images/solr-2.png)
  
  After clicking `Add Core` button, it may throw a error:
  `Error CREATEing SolrCore 'helloworld': Unable to create core [helloworld] Caused by: Can't find resource 'solrconfig.xml' in classpath or 'D:\solr-7.6.0\server\solr\helloworld'`
  
  In order to solve this problem, just copy `conf` folder of `D:/solr-7.6.0/server/solr/configsets/_default/` to
  `D:/solr-7.6.0/server/solr/helloworld/`.
  
  If core is created success, then the UI looks:
  
  ![_config.yml]({{ site.baseurl }}/images/solr-3.png)
  
#### Add MySql Jar

  Add mysql connector jar to `D:/solr-7.6.0/contrib/dataimporthandler/lib/`.
  
  Create `lib` folder if needed.
  
#### Modify solrconfig.xml

  Change `solrconfig.xml` of `D:\solr-7.6.0\server\solr\helloworld\conf`, add following two sections:
  
  ```xml
  <lib dir="${solr.install.dir:../../../..}/contrib/dataimporthandler/lib" regex=".*\.jar" />
  <lib dir="${solr.install.dir:../../../..}/dist/" regex="solr-dataimporthandler-.*\.jar" />
  ..........<!-- other content -->
  <requestHandler name="/dataimport" class="org.apache.solr.handler.dataimport.DataImportHandler">
    <lst name="defaults">
    <str name="config">data-config.xml</str>
    </lst>
  </requestHandler>
  ```
  
#### Add data config

  Create a data config file under the `D:\solr-7.6.0\server\solr\helloworld\conf`.
  
  ```xml
  <dataConfig>
      <dataSource type="JdbcDataSource"
            driver="com.mysql.cj.jdbc.Driver"
            url="jdbc:mysql://192.168.88.25:3306/Test"
            user="root"
            password="123456"/>
      <document>
          <entity name="T_CRM_CUSTOMER"
              pk="CUSTOMER_ID"
              query="select CUSTOMER_ID, CUSTOMER_CODE, NAME from T_CRM_CUSTOMER"
          >
              <field column="CUSTOMER_ID" name="CUSTOMER_ID"/>
              <field column="CUSTOMER_CODE" name="CUSTOMER_CODE"/>
              <field column="NAME" name="NAME"/>
         </entity>
      </document>
  </dataConfig>
  ```
  
#### Restart Solr
  
    `solr restart -p 8983`
    
#### Create Schema

  Add the fields your want to be indexed and/or stored.
  
  ![_config.yml]({{ site.baseurl }}/images/solr-4.png)
  
#### Import Data From DB

  ![_config.yml]({{ site.baseurl }}/images/solr-5.png)
  
  Click `Execute` button with `Auto Refresh Status` checked(otherwise you have to click refresh status button to see import result.)
  
### Query

  ![_config.yml]({{ site.baseurl }}/images/solr-6.png)
  
#### Standard Query Parser

  For the detail about the query syntax, please refer the [doc](https://lucene.apache.org/solr/guide/7_6/the-standard-query-parser.html)
  
### Call Solr From Code
  
  We can call solr from our code, e.g. Java, by restful call, the url looks like: `http://localhost:8983/solr/helloworld/select?q=NAME:TW`
