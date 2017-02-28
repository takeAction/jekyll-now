---
layout: post
title: java web service
date: 2016-03-26 23:16
author: 2freesky
comments: true
categories: [Uncategorized]
---
1.What is web service
<p class="western">    It is used to communicate from one system to another system over the internet even they are build in different programming language.</p>
2.Types of web service
<ul>
	<li>
<p class="western">soap(simple object access protocol) is xml based and the java-ws(java api for xml web service) is its API.</p>
</li>
	<li>
<p class="western">restful(representational state transfer) is an architectural style not a protocol and jax-rs(java api for restful web service) is its API.</p>
</li>
</ul>
<a href="https://2freesky.files.wordpress.com/2016/03/web-service-api.png" rel="attachment wp-att-91"><img class="alignnone size-medium wp-image-91" src="https://2freesky.files.wordpress.com/2016/03/web-service-api.png?w=300" alt="web service api" width="300" height="233" /></a>

3.SOA
<p class="western">     SOA is abbreviation of service oriented architectural which is a design pattern. It is designed to provide services to other application through protocol. It is a concept only and  not tied to any programming language or platform.<span style="line-height:1.7;">Web service is a technology of SOA.</span></p>
4.WSDL
<p class="western">     WSDL is an acronym web services description language. It's a xml document containing information about web services such as method name, method parameter and how to access it. We can type “<span style="color:#000000;"><span style="font-family:Verdana;"><span style="font-size:small;">http:</span></span></span><span style="color:#008200;"><span style="font-family:Verdana;"><span style="font-size:small;">//&lt;ip&gt;:&lt;port&gt;/ws/hello?wsdl</span></span></span> ” in browser to see WSDL.</p>
<p class="western">       elements of WSDL are :</p>

<ul>
	<li>
<p class="western">Types element describes the data types used by your web service. Data types are usually specified by XML schema. It can be described in any language as long as your web service API supports it.</p>
</li>
</ul>
<ul>
	<li>
<p class="western">Binding element describes how your web service is bound to a protocol. In other  words, how your web service is accessible. To be accessible, the web service must be reachable using some network protocol. This is called 'binding' the web service to the protocol.</p>
</li>
</ul>
<ul>
	<li>
<p class="western">Interface element describes the operations supported by your web service. It is similar to methods in programming languages. Client can only call one operation per request.</p>
</li>
</ul>
<ul>
	<li>
<p class="western">Service element shows the endpoint of your web service. In other words, the address where the web services can be reached.</p>
</li>
</ul>
<ul>
	<li>
<p class="western">Endpoint illustrates the address of your web service. The endpoint binding attribute indicates what binding element this endpoint uses, i.e. protocol with which you will access web service. The address attribute describes the URI at which you can access the service.</p>
</li>
</ul>
<ul>
	<li>
<p class="western">Message element describes the data being exchanged between the web service provider and consumer.</p>
</li>
</ul>
5.UDDI
<p class="western">    It represents universal description, discovery and integration. It is a xml based framework for describing, discovering and integrating web services. UDDI is a directory of web service interfaces described by WSDL which containing information about web services.</p>
6.How to build web service
<table border="1" width="100%" cellspacing="0" cellpadding="4"><colgroup> <col width="25*" /> <col width="33*" /> <col width="198*" /> </colgroup>
<tbody>
<tr valign="TOP">
<td width="10%"></td>
<td width="13%"></td>
<td width="77%">
<p class="western">code</p>
</td>
</tr>
<tr valign="TOP">
<td rowspan="2" width="10%">
<p class="western" align="CENTER">SOAP</p>
</td>
<td width="13%">
<p class="western">RPC</p>
</td>
<td width="77%">
<ul>
	<li>
<p class="western">Defines an interface with annotation @WebService, @SOAPBinding(style=Style.RPC) at class level and @WebMethod at method level</p>
</li>
	<li>
<p class="western">Defines an implementation class with annotation @WebService(endpointInterface=”&lt;interface name with package name&gt;”) at class level</p>
</li>
	<li>
<p class="western">Publish this service with code : Endpoint.publish(address, implementator), the address looks <span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">like : </span></span></span><span style="color:#2a00ff;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">http://192.168.2.102:7779/ws/hello</span></span></span></p>
</li>
	<li>
<p class="western"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">Client side has to define the interface with same package name as server side and the code are :</span></span></span></p>
</li>
</ul>
<p class="western"><span style="color:#000000;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;">URL url = new URL(“&lt;address&gt;?wsdl”);</span></span></span></p>
<p class="western"><span style="color:#000000;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;">QName qName = new Qname(</span></span></span></p>
<p class="western"><span style="color:#000000;"> “<span style="font-family:'Courier New', monospace;"><span style="font-size:small;">http://&lt;package name&gt;”,”implementator name”);</span></span></span></p>
<p class="western"><span style="color:#000000;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;">Service service = Service.create(url,qName);</span></span></span></p>
<p class="western"><span style="color:#000000;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;">&lt;Interface variable&gt; = </span></span></span></p>
<p class="western"><span style="color:#000000;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;">service.getPort(&lt;interface class&gt;);</span></span></span></p>
<p class="western"><span style="color:#000000;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;">Last step is call the service method.</span></span></span></p>
</td>
</tr>
<tr valign="TOP">
<td width="13%">
<p class="western">Document</p>
</td>
<td width="77%">
<p class="western">It's similar to RPC, just replace the Style.RPC with Style.DOCUMENT</p>
</td>
</tr>
<tr valign="TOP">
<td rowspan="2" width="10%">
<p class="western" align="CENTER">REST</p>
</td>
<td width="13%">
<p class="western">Jersey</p>
</td>
<td width="77%">
<p class="western">Jersey is an implementation of jax-rs.</p>

<ul>
	<li>
<p class="western">In server side(dynamic web project), the code is :</p>
</li>
</ul>
<p class="western"><span style="color:#646464;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">@Path</span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">(</span></span></span><span style="color:#2a00ff;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">"/hello"</span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#7f0055;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><b>public</b></span> <span style="color:#7f0055;"><b>class</b></span><span style="color:#000000;"> Hello {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@GET</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@Produces</span><span style="color:#000000;">(MediaType.</span><span style="color:#0000c0;"><i><b>TEXT_PLAIN</b></i></span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span><span style="color:#000000;"> String sayPlainTextHello() {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>return</b></span> <span style="color:#2a00ff;">"Hello Jersey Plain"</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> } </span></span></span></p>

<ul>
	<li>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">the web.xml looks like :</span></span></span></p>
</li>
</ul>
<p class="western" align="LEFT"><span style="color:#008080;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">&lt;</span></span></span><span style="color:#3f7f7f;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">servlet</span></span></span><span style="color:#008080;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#008080;">&lt;</span><span style="color:#3f7f7f;">servlet-name</span><span style="color:#008080;">&gt;</span><span style="color:#000000;">Jersey REST Service</span><span style="color:#008080;">&lt;/</span><span style="color:#3f7f7f;">servlet-name</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#008080;">&lt;</span><span style="color:#3f7f7f;">servlet-class</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">org.glassfish.jersey.servlet.ServletContainer</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#008080;">&lt;/</span><span style="color:#3f7f7f;">servlet-class</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#008080;">&lt;</span><span style="color:#3f7f7f;">init-param</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#008080;">&lt;</span><span style="color:#3f7f7f;">param-name</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">jersey.config.server.provider.packages</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#008080;">&lt;/</span><span style="color:#3f7f7f;">param-name</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#008080;">&lt;</span><span style="color:#3f7f7f;">param-value</span><span style="color:#008080;">&gt;</span><span style="color:#000000;">cn.my.webservice.restful</span><span style="color:#008080;">&lt;/</span><span style="color:#3f7f7f;">param-value</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#008080;">&lt;/</span><span style="color:#3f7f7f;">init-param</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#008080;">&lt;</span><span style="color:#3f7f7f;">load-on-startup</span><span style="color:#008080;">&gt;</span><span style="color:#000000;">1</span><span style="color:#008080;">&lt;/</span><span style="color:#3f7f7f;">load-on-startup</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#008080;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;">&lt;/</span><span style="color:#3f7f7f;">servlet</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#008080;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;">&lt;</span><span style="color:#3f7f7f;">servlet-mapping</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#008080;">&lt;</span><span style="color:#3f7f7f;">servlet-name</span><span style="color:#008080;">&gt;</span><span style="color:#000000;">Jersey REST Service</span><span style="color:#008080;">&lt;/</span><span style="color:#3f7f7f;">servlet-name</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#008080;">&lt;</span><span style="color:#3f7f7f;">url-pattern</span><span style="color:#008080;">&gt;</span><span style="color:#000000;">/rest/*</span><span style="color:#008080;">&lt;/</span><span style="color:#3f7f7f;">url-pattern</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#008080;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;">&lt;/</span></span></span><span style="color:#3f7f7f;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">servlet-mapping</span></span></span><span style="color:#008080;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">&gt;</span></span></span></p>

<ul>
	<li>
<p class="western">In client side, the code is :</p>
</li>
</ul>
<p class="western">ClientConfig config = new ClientConfig();</p>
<p class="western">Client client = ClientBuilder.newClient(config);</p>
<p class="western">URI uri =</p>
<p class="western">UriBuilder.fromUri(“http://&lt;ip&gt;:&lt;port&gt;/&lt;app name&gt;”).build();</p>
<p class="western">WebTarget target = client.target(uri);</p>
<p class="western">target.path(&lt;value of url pattern except “/*”&gt;)</p>
<p class="western">.path(&lt;value of @Path&gt;).request()</p>
<p class="western">.accept(MediaType.&lt;?&gt;).get(&lt;return class&gt;);</p>
</td>
</tr>
<tr valign="TOP">
<td width="13%">
<p class="western">RESTeasy</p>
</td>
<td width="77%">
<p class="western">RESTEasy is the jax-rs implementation provoded by JBoss.</p>
</td>
</tr>
</tbody>
</table>
<a href="https://2freesky.files.wordpress.com/2016/03/jersey.png" rel="attachment wp-att-92"><img class="alignnone size-medium wp-image-92" src="https://2freesky.files.wordpress.com/2016/03/jersey.png?w=300" alt="jersey" width="300" height="203" /></a>
<table style="height:1729px;" border="1" width="830" cellspacing="0" cellpadding="4"><colgroup> <col width="48*" /> <col width="208*" /> </colgroup>
<tbody>
<tr valign="TOP">
<td width="19%">
<p class="western">GET</p>
</td>
<td width="81%">
<p class="western" align="LEFT"><span style="color:#646464;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">@GET</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#646464;">@Path</span><span style="color:#000000;">(</span><span style="color:#2a00ff;">"/{param}"</span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>public</b></span><span style="color:#000000;"> Response getMsg(</span><span style="color:#646464;">@PathParam</span><span style="color:#000000;">(</span><span style="color:#2a00ff;">"param"</span><span style="color:#000000;">) String </span><span style="color:#6a3e3e;">msg</span><span style="color:#000000;">) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">String </span><span style="color:#6a3e3e;">output</span><span style="color:#000000;"> = </span><span style="color:#2a00ff;">"Jersey say "</span><span style="color:#000000;">+</span><span style="color:#6a3e3e;">msg</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>return </b></span></span></span><span style="color:#000000;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;">Response.</span><span style="color:#000000;"><i>status</i></span><span style="color:#000000;">(200).entity(</span><span style="color:#6a3e3e;">output</span><span style="color:#000000;">).build();</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">} </span></span></span></p>
</td>
</tr>
<tr valign="TOP">
<td width="19%">
<p class="western">POST</p>
</td>
<td width="81%">
<p class="western" align="LEFT"><span style="color:#646464;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">@POST</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#646464;">@Path</span><span style="color:#000000;">(</span><span style="color:#2a00ff;">"/add"</span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>public</b></span><span style="color:#000000;"> Response getUser(</span><span style="color:#646464;">@FormParam</span><span style="color:#000000;">(</span><span style="color:#2a00ff;">"id"</span><span style="color:#000000;">) </span><span style="color:#7f0055;"><b>int</b></span> <span style="color:#6a3e3e;">id</span><span style="color:#000000;">,</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#646464;">@FormParam</span><span style="color:#000000;">(</span><span style="color:#2a00ff;">"name"</span><span style="color:#000000;">) String </span><span style="color:#6a3e3e;">name</span><span style="color:#000000;">,</span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@FormParam</span><span style="color:#000000;">(</span><span style="color:#2a00ff;">"price"</span><span style="color:#000000;">) </span><span style="color:#7f0055;"><b>int</b></span> <span style="color:#6a3e3e;">price</span><span style="color:#000000;">) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>return</b></span><span style="color:#000000;"> Response.</span><span style="color:#000000;"><i>status</i></span><span style="color:#000000;">(200).entity(</span><span style="color:#2a00ff;">"..</span><span style="color:#6a3e3e;">”</span><span style="color:#000000;">).build();</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">}</span></span></span></p>
</td>
</tr>
<tr valign="TOP">
<td width="19%">
<p class="western">File upload</p>
</td>
<td width="81%">
<p class="western" align="LEFT"><span style="color:#646464;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">@POST</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#646464;">@Path</span><span style="color:#000000;">(</span><span style="color:#2a00ff;">"/upload"</span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#646464;">@Consumes</span><span style="color:#000000;">(MediaType.</span><span style="color:#0000c0;"><i><b>MULTIPART_FORM_DATA</b></i></span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>public</b></span><span style="color:#000000;"> Response uploadFile(</span><span style="color:#646464;">@FormDataParam</span><span style="color:#000000;">(</span><span style="color:#2a00ff;">"file"</span><span style="color:#000000;">) InputStream </span><span style="color:#6a3e3e;">is</span><span style="color:#000000;">,</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#646464;">@FormDataParam</span><span style="color:#000000;">(</span><span style="color:#2a00ff;">"file"</span><span style="color:#000000;">) FormDataContentDisposition </span><span style="color:#6a3e3e;">fileDetail</span><span style="color:#000000;">) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">String </span><span style="color:#6a3e3e;">fileLocation</span><span style="color:#000000;"> = </span><span style="color:#2a00ff;">"E:/"</span><span style="color:#000000;">+</span><span style="color:#6a3e3e;">fileDetail</span><span style="color:#000000;">.getFileName();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">FileOutputStream </span><span style="color:#6a3e3e;">os</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> FileOutputStream(</span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> File(</span><span style="color:#6a3e3e;">fileLocation</span><span style="color:#000000;">));</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>int</b></span> <span style="color:#6a3e3e;">read</span><span style="color:#000000;"> = 0;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>byte</b></span><span style="color:#000000;">[] </span><span style="color:#6a3e3e;">bytes</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span> <span style="color:#7f0055;"><b>byte</b></span><span style="color:#000000;">[1024];</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>while</b></span><span style="color:#000000;">((</span><span style="color:#6a3e3e;">read</span><span style="color:#000000;"> = </span><span style="color:#6a3e3e;">is</span><span style="color:#000000;">.read(</span><span style="color:#6a3e3e;">bytes</span><span style="color:#000000;">)) != -1) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#6a3e3e;">os</span><span style="color:#000000;">.write(</span><span style="color:#6a3e3e;">bytes</span><span style="color:#000000;">, 0, </span><span style="color:#6a3e3e;">read</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">}</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">os</span><span style="color:#000000;">.flush();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">os</span><span style="color:#000000;">.close();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">String </span><span style="color:#6a3e3e;">output</span><span style="color:#000000;"> = </span><span style="color:#2a00ff;">"file successly uploaded to : "</span><span style="color:#000000;">+</span><span style="color:#6a3e3e;">fileLocation</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#7f0055;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><b>return</b></span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> Response.</span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><i>status</i></span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">(200).entity(</span></span></span><span style="color:#6a3e3e;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">output</span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">).build();</span></span></span></p>
</td>
</tr>
<tr valign="TOP">
<td width="19%">
<p class="western">File download</p>
</td>
<td width="81%">
<p class="western" align="LEFT"><span style="color:#646464;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">@GET</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#646464;">@Path</span><span style="color:#000000;">(</span><span style="color:#2a00ff;">"/txt"</span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#646464;">@Produces</span><span style="color:#000000;">(</span><span style="color:#2a00ff;">"text/plain"</span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>public</b></span><span style="color:#000000;"> Response getFile() {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;"> String </span><span style="color:#6a3e3e;">filePath</span><span style="color:#000000;">=</span><span style="color:#2a00ff;">"E:/test.txt"</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;"> File </span><span style="color:#6a3e3e;">file</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> File(</span><span style="color:#6a3e3e;">filePath</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;"> ResponseBuilder </span><span style="color:#6a3e3e;">builder</span><span style="color:#000000;"> = Response.</span><span style="color:#000000;"><i>ok</i></span><span style="color:#000000;">((Object)</span><span style="color:#6a3e3e;">file</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#6a3e3e;">builder</span><span style="color:#000000;">.header(</span><span style="color:#2a00ff;">"Content-Disposition"</span><span style="color:#000000;">, </span><span style="color:#2a00ff;">"attachment;filename=\"javapoint.txt\""</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>return</b></span> <span style="color:#6a3e3e;">builder</span><span style="color:#000000;">.build();</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">}</span></span></span></p>
</td>
</tr>
</tbody>
</table>
