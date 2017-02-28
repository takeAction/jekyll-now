---
layout: post
title: struts internationalization
date: 2016-05-29 14:36
author: 2freesky
comments: true
categories: [struts]
---
internationalization(aka i18n) in struts 2 has following steps :
<ol>
	<li>create locale properties files, the fild name format is : &lt;bunlde name&gt;_&lt;language&gt;_&lt;country&gt;.properties, bundle name can be any, the language looks like : es, fr etc, country is optional which can be US and so on. This properties file contains key/value pairs.</li>
	<li>config <strong><em>&lt;constant name="struts.custom.i18n.resources" value="global" /&gt;</em></strong> in struts.xml, the value 'global' is the bundle name. If there are multiple types resource files, the value can be : value="bundle1,bundle2,...."</li>
	<li>On UI page, there are some ways to show locale message : <em><strong>&lt;s:text name="global.heading" /&gt;</strong></em>, the global.heading is the key in locale properties file, or use <em><strong>key="&lt;key&gt;"</strong></em> in other tags, e.g. &lt;s:textfield name="name" key="global.name" size="20" /&gt; or &lt;s:submit name="submit" key="global.submit" /&gt; or <em><strong>&lt;s:property value="getText('global.success')" /&gt;</strong></em></li>
	<li>In back-end java code, we can use getText("&lt;key&gt;") directly</li>
</ol>
