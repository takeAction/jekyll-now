---
layout: post
title: maven connect to private repository
date: 2017-02-19 22:46
author: 2freesky
category: Dev
tags: [web, jekyll]
---
Take nexus oss 3 for example, maven settings.xml looks like:

```

<?xml version="1.0" encoding="UTF-8"?>

<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">

<servers>

<!-- used for deploy artifact to nexus release or snapshots repository -->
<server>
<id>nexus</id>
<username>admin</username>
<password>admin123</password>
</server>
</servers>

<mirrors>
<mirror>
<id>nexus</id>
<mirrorOf>*</mirrorOf>
<url>http://192.168.2.102:8081/repository/maven-public</url>
</mirror>
</mirrors>

<profiles>

<!-- set maven default java compiler version -->
<profile>
<id>jdk-1.8</id>
<activation>
<activeByDefault>true</activeByDefault>
<jdk>1.8</jdk>
</activation>
<properties>
<maven.compiler.source>1.8</maven.compiler.source>
<maven.compiler.target>1.8</maven.compiler.target>
<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
</properties>
</profile>

<profile>
<id>nexus</id>
<repositories>
<repository>
<id>central</id>
<url>http://central</url>
<releases><enabled>true</enabled></releases>
<snapshots><enabled>true</enabled></snapshots>
</repository>
</repositories>
<pluginRepositories>
<pluginRepository>
<id>central</id>
<url>http://central</url>
<releases><enabled>true</enabled></releases>
<snapshots><enabled>true</enabled></snapshots>
</pluginRepository>
</pluginRepositories>
</profile>
</profiles>

<activeProfiles>
<activeProfile>nexus</activeProfile>
</activeProfiles>

</settings>

```

`<servers`> tag is a list of authentication profiles, keyed by the server-id used within the system.  Authentication profiles can be used whenever maven must make a connection to a remote server.

`<server`> specifies the authentication information to use when connecting to a particular server, identified by a unique name within the system (referred to by the 'id' attribute below).
NOTE: You should either specify username/password OR privateKey/passphrase, since these pairings are used together.

`<mirrors`>  is a list of mirrors to be used in downloading artifacts from remote repositories.
It works like this: a POM may declare a repository to use in resolving certain artifacts.
However, this repository may have problems with heavy traffic at times, so people have mirrored it to several places.
That repository definition will have a unique id, so we can create a mirror reference for that repository, to be used as an alternate download site. The mirror site will be the preferred server for that repository.

`<mirror`> specifies a repository mirror site to use instead of a given repository. The repository that this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used for inheritance and direct lookup purposes, and must be unique across the set of mirrors.

profiles is a list of profiles which can be activated in a variety of ways, and which can modify the build process. Profiles provided in the settings.xml are intended to provide local machine-specific paths and repository locations which allow the build to work in the local environment.
For example, if you have an integration testing plugin - like cactus - that needs to know where your Tomcat instance is installed, you can provide a variable here such that the variable is dereferenced during the build process to configure the cactus plugin.
As noted above, profiles can be activated in a variety of ways. One way - the activeProfiles
section of this document (settings.xml) - will be discussed later. Another way essentially
relies on the detection of a system property, either matching a particular value for the property, or merely testing its existence. Profiles can also be activated by JDK version prefix, where a value of '1.4' might activate a profile when the build is executed on a JDK version of '1.4.2_07'.
Finally, the list of active profiles can be specified directly from the command line.
NOTE: For profiles defined in the settings.xml, you are restricted to specifying only artifact
repositories, plugin repositories, and free-form properties to be used as configuration
variables for plugins in the POM.

profile Specifies a set of introductions to the build process, to be activated using one or more of the mechanisms described above. For inheritance purposes, and to activate profiles via &lt;activatedProfiles/&gt; or the command line, profiles have to have an ID that is unique.
An encouraged best practice for profile identification is to use a consistent naming convention
for profiles, such as 'env-dev', 'env-test', 'env-production', 'user-jdcasey', 'user-brett', etc.
This will make it more intuitive to understand what the set of introduced profiles is attempting to accomplish, particularly when you only have a list of profile id's for debug.

maven-public is a repository group.  From next screenshot, we can see that:

<img class="alignnone size-full wp-image-654" src="https://2freesky.files.wordpress.com/2017/02/untitled-2.png" alt="untitled-2" width="691" height="789" />

type of maven-public is group and it has three members: maven-releases, maven-snapshots and maven-central.

group means we can just use group(e.g.maven-public) in maven settings.xml to represent all its members, such that we don't need to define all repository urls in settings.xml and  it will download jars from its members according to the definition order, e.g. maven-snapshots has a.jar, we use maven-public in settings.xml, then it can download a.jar from nexus.

However we cannot upload artifact to group.

maven-releases and maven-snapshots are hosted repository.  They are used for storing our own artifacts, for instance our company jars, or 3rd party which cannot downloaded from public maven repository.

maven-central is proxy repository which means nexus look for jars from it, if it has, then download jar from maven-central, if it has not, then download jar from public maven repository(https://repo1.maven.org/maven2/) and keep it in maven-central.

Upload own artifact to nexus by following configuration in project pom.xml:

```

<distributionManagement>
<repository>
<id>nexus</id>
<name>Releases</name>
<url>http://192.168.2.102:8081/repository/maven-releases/</url>
</repository>
<snapshotRepository>
<id>nexus</id>
<name>Snapshot</name>
<url>http://192.168.2.102:8081/repository/maven-snapshots/</url>
</snapshotRepository>
</distributionManagement>

```

then run maven deploy command.
