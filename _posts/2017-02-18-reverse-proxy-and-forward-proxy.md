---
layout: post
title: reverse proxy and forward proxy
date: 2017-02-18 12:07
author: 2freesky
comments: true
categories: [Uncategorized]
---
<ul>
	<li>forward proxy(some people call it client proxy)</li>
</ul>
(client -&gt; forward proxy) -&gt; target server

client cannot access target server directly, therefore use forward proxy to get client request and forward it to target.

client knows forward proxy and target server, while target server only knows forward proxy.

The client must be configured to use the forward proxy to access other sites.

It also can use caching to reduce the network usage.
<ul>
	<li>reverse proxy(server proxy)</li>
</ul>
client -&gt; (reverse proxy -&gt; other server)

for client, it only knows reverse proxy and reverse proxy is target server for it, it just let other server to handle the client requests.

No configuration in client is necessary.

A typical usage is load balance
