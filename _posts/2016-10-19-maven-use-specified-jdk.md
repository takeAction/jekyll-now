---
layout: post
title: maven use specified jdk
date: 2016-10-19 23:07
author: 2freesky
comments: true
categories: [maven]
---
Currently, maven uses jdk 1.5 by default, that is even we specify JDK and compiler in eclipse, if we check out/create maven project, or update maven project or clean install, it will use jdk 1.5 rather than eclipse settings.  This is not convenient and has error sometimes if we use higher jdk.

In order to use specified jdk, we can add following to maven settings.xml such that it uses jdk 1.7:
<blockquote>&lt;profile&gt;
&lt;id&gt;jdk-1.7&lt;/id&gt;
&lt;activation&gt;
&lt;activeByDefault&gt;true&lt;/activeByDefault&gt;
&lt;jdk&gt;1.7&lt;/jdk&gt;
&lt;/activation&gt;
&lt;properties&gt;
&lt;maven.compiler.source&gt;1.7&lt;/maven.compiler.source&gt;
&lt;maven.compiler.target&gt;1.7&lt;/maven.compiler.target&gt;
&lt;maven.compiler.compilerVersion&gt;1.7&lt;/maven.compiler.compilerVersion&gt;
&lt;/properties&gt;
&lt;/profile&gt;</blockquote>
