---
layout: post
title: eclipse maven plugin installation
date: 2016-03-27 09:45
author: 2freesky
comments: true
categories: [maven]
---
maven : 3.3.9

eclipse : Luna

OS        : win 10
<ol>
	<li>install maven in windows as maven website describes</li>
	<li>install m2eclipse in eclipse using url</li>
	<li>in eclipse, go to Window -&gt; Preference -&gt;Maven-&gt; Installation, add your installed maven</li>
	<li>go to Window-&gt;Preference-&gt;Maven-&gt;User Settings, set your settings.xml and click 'úpdate settings' button</li>
</ol>
That's all, you can use maven and create maven project in eclipse, it can download jars to default repository folder  : C:\Users\&lt;user name&gt;\.m2 automatically from maven repository web site.

We can change the default repository folder in &lt;maven installation folder&gt;/conf/setting.xml, just add : &lt;localRepository&gt;E:/MavenRepository&lt;/localRepository&gt; in it.

<strong>Note : </strong>
<ul>
	<li>If there are errors about '
<pre class="lang-xml prettyprint prettyprinted"><code><span class="pln">Plugin org.apache.maven.plugins:maven-resources-plugin:2.9 or one of its dependencies could not be resolved</span></code></pre>
', just delete all files under your local repository folder, then rebuild the maven project.</li>
	<li>Sometimes it cannot download the jars to local repository folder, the solution is deleting this jar folder in local repository, then rebuild this project.</li>
</ul>
