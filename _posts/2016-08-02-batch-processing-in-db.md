---
layout: post
title: Batch processing in DB
date: 2016-08-02 16:11
author: 2freesky
comments: true
categories: [Mybatis]
---

### What

  Group sql statements into a batch and submit them with one call to DB, rather than send each statement one by one, 
  this is batch processing.

### Why

  Batch processing can reduce the amount of communication overhead, thereby improving  performance. 
  
  It is faster than sending them one by one without waiting for each one to finish, and the DB may be 
  execute some of them in parallel.
  
### How

  In mybatis, we have two approaches to implement batch processing:
  
  1. if integrate mybatis and spring</span></p>
</li>
</ul>
<blockquote>
<pre class="western">     <span style="font-family:Arial, sans-serif;"><span style="font-size:large;"><span style="color:#008080;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">&lt;</span></span></span><span style="color:#3f7f7f;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">bean</span></span></span> <span style="color:#7f007f;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">id</span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">=</span></span></span><span style="color:#2a00ff;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><i>"sqlSession"</i></span></span></span> 
<span style="color:#7f007f;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">           class</span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">=</span></span></span><span style="color:#2a00ff;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><i>"org.mybatis.spring.SqlSessionTemplate"</i></span></span></span><span style="color:#008080;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">&gt;</span></span></span></span></span></pre>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#008080;">        &lt;</span><span style="color:#3f7f7f;">constructor-arg</span> <span style="color:#7f007f;">index</span><span style="color:#000000;">=</span><span style="color:#2a00ff;"><i>"0"</i></span> <span style="color:#7f007f;">ref</span><span style="color:#000000;">=</span><span style="color:#2a00ff;"><i>"sqlSessionFactory"</i></span> <span style="color:#008080;">/&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#008080;">        &lt;</span><span style="color:#3f7f7f;">constructor-arg</span> <span style="color:#7f007f;">index</span><span style="color:#000000;">=</span><span style="color:#2a00ff;"><i>"1"</i></span> <span style="color:#7f007f;">value</span><span style="color:#000000;">=</span><span style="color:#2a00ff;"><i>"BATCH"</i></span> <span style="color:#008080;">/&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#008080;">      &lt;/</span><span style="color:#3f7f7f;">bean</span><span style="color:#008080;">&gt;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#008080;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">     then in code :</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#008080;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">     for() {</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#008080;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">        sqlSession.update();</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#008080;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">     }</span></span></span></p>
</blockquote>
<ul>
	<li>
<p class="western"><span style="font-family:Arial, sans-serif;">without spring</span></p>
</li>
</ul>
<blockquote>
<pre class="western">    <span style="font-family:Arial, sans-serif;"><span style="font-size:large;"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">SqlSession </span></span></span><span style="color:#6a3e3e;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">session</span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> = </span></span></span><span style="color:#6a3e3e;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">factory</span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">.openSession(ExecutorType.</span></span></span><span style="color:#0000c0;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><i><b>BATCH</b></i></span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">);			</span></span></span></span></span></pre>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>    for</b></span><span style="color:#000000;">(Child </span><span style="color:#6a3e3e;">c</span><span style="color:#000000;"> : </span><span style="color:#6a3e3e;">childs</span><span style="color:#000000;">) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">       session</span><span style="color:#000000;">.update(</span><span style="color:#2a00ff;">"updateChild"</span><span style="color:#000000;">, </span><span style="color:#6a3e3e;">c</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">    }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">    session</span><span style="color:#000000;">.commit();</span></span></span></p>
</blockquote>
