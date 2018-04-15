---
layout: post
title: Maven deploy webapp to tomcat
categories: [maven]
---

1. create a user with role `manager-script` in tomcat-users.xml

```
<role rolename="manager-script"/>
<user username="deployer" password="<your password>" roles="manager-script"/>
```

2. config server in maven settings.xml

```
<server>
    <id>deploy2Tomcat</id>
    <username>deployer</username>
    <password><your password></password>
</server>
```

**Note: user name and password are the same as your tomcat-users.xml**

3. config `pom.xml`

```
<plugins>
    <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.2</version>
    		<configuration>
    				<url>http://127.0.0.1:8080/manager/text</url>
    				<server>deploy2Tomcat</server>
    				<path>/example</path>
    		</configuration>
    </plugin>
</plugins>
```

Note : 
    - value of `<server>` is server id of maven settings.xml
    - `tomcat7-maven-plugin` work for tomcat 8
    - `path` value cannot be only `/`, it should be `/<webapp name>`

4. make sure other user has write permission on folder `webapps`

5. start tomcat

6. run maven gole `tomcat7:deploy`, `tomcat7:redeploy` or `tomcat7:undeploy`
