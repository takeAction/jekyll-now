---
layout: post
title: Hibernate ManyToMany
date: 2016-02-01 21:54
author: 2freesky
comments: true
categories: [Hibernate]
---
<h3 class="western">1.table structures</h3>
<p class="western">author_book : book_id int not null, author_id int not null, author_id foreign key which refer to id of author, book_id foreign key for id of book</p>
<p class="western">author : id int pk auto increment, name varchar</p>
<p class="western">book : id int pk auto increment, name varchar</p>

<h3 class="western">2.Unidirectional</h3>
<h4 class="western">2.1.POJO</h4>
<p class="western"><span style="color:#646464;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">@Entity</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#646464;">@Table</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"author"</span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>class</b></span><span style="color:#000000;"> Author {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@Id</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@GeneratedValue</span><span style="color:#000000;">(strategy=GenerationType.</span><span style="color:#0000c0;"><i><b>AUTO</b></i></span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span> <span style="color:#7f0055;"><b>int</b></span> <span style="color:#0000c0;">id</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#7f0055;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><b>private</b></span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> String </span></span></span><span style="color:#0000c0;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">name</span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#646464;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">@Entity</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#646464;">@Table</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"book"</span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>class</b></span><span style="color:#000000;"> Book {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@Id</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@GeneratedValue</span><span style="color:#000000;">(strategy=GenerationType.</span><span style="color:#0000c0;"><i><b>AUTO</b></i></span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span> <span style="color:#7f0055;"><b>int</b></span> <span style="color:#0000c0;">id</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span><span style="color:#000000;"> String </span><span style="color:#0000c0;">name</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@ManyToMany</span><span style="color:#000000;">(cascade=CascadeType.</span><span style="color:#0000c0;"><i><b>ALL</b></i></span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@JoinTable</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"author_book"</span><span style="color:#000000;">,joinColumns=</span><span style="color:#646464;">@JoinColumn</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"book_id"</span><span style="color:#000000;">),</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;"> inverseJoinColumns=</span><span style="color:#646464;">@JoinColumn</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"author_id"</span><span style="color:#000000;">))</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#7f0055;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><b>private</b></span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> List&lt;Author&gt; </span></span></span><span style="color:#0000c0;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">authors</span></span></span><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">;</span></span></span></p>

<h4 class="western" align="LEFT"><span style="font-family:'Times New Roman', serif;"><span style="font-size:medium;"><span style="color:#000000;">2.2.test class</span></span></span></h4>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Author </span><span style="color:#6a3e3e;">a1</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> Author();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">a1</span><span style="color:#000000;">.setName(</span><span style="color:#2a00ff;">"icil"</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Author </span><span style="color:#6a3e3e;">a2</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> Author();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">a2</span><span style="color:#000000;">.setName(</span><span style="color:#2a00ff;">"yict"</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Book </span><span style="color:#6a3e3e;">b1</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> Book();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">b1</span><span style="color:#000000;">.setName(</span><span style="color:#2a00ff;">"history"</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">List&lt;Author&gt; </span><span style="color:#6a3e3e;">authors</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> ArrayList&lt;Author&gt;();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">authors</span><span style="color:#000000;">.add(</span><span style="color:#6a3e3e;">a1</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">authors</span><span style="color:#000000;">.add(</span><span style="color:#6a3e3e;">a2</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">b1</span><span style="color:#000000;">.setAuthors(</span><span style="color:#6a3e3e;">authors</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Author </span><span style="color:#6a3e3e;">a3</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> Author();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">a3</span><span style="color:#000000;">.setName(</span><span style="color:#2a00ff;">"micle"</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Author </span><span style="color:#6a3e3e;">a4</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> Author();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">a4</span><span style="color:#000000;">.setName(</span><span style="color:#2a00ff;">"henry"</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Book </span><span style="color:#6a3e3e;">b2</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> Book();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">b2</span><span style="color:#000000;">.setName(</span><span style="color:#2a00ff;">"starwar"</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">List&lt;Author&gt; </span><span style="color:#6a3e3e;">authors2</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> ArrayList&lt;Author&gt;();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">authors2</span><span style="color:#000000;">.add(</span><span style="color:#6a3e3e;">a3</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">authors2</span><span style="color:#000000;">.add(</span><span style="color:#6a3e3e;">a4</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">b2</span><span style="color:#000000;">.setAuthors(</span><span style="color:#6a3e3e;">authors2</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">s</span><span style="color:#000000;">.save(</span><span style="color:#6a3e3e;">b1</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">s</span><span style="color:#000000;">.save(</span><span style="color:#6a3e3e;">b2</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">for delete : </span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Book </span><span style="color:#6a3e3e;">book</span><span style="color:#000000;"> = (Book)</span><span style="color:#6a3e3e;">s</span><span style="color:#000000;">.get(Book.</span><span style="color:#7f0055;"><b>class</b></span><span style="color:#000000;">, 3);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">s</span><span style="color:#000000;">.delete(</span><span style="color:#6a3e3e;">book</span><span style="color:#000000;">);</span></span></span></p>

<h3 class="western" align="LEFT"><span style="font-family:'Times New Roman', serif;"><span style="font-size:medium;"><span style="color:#000000;">3.Bidirectional</span></span></span></h3>
<h4 class="western" align="LEFT"><span style="font-family:'Times New Roman', serif;"><span style="font-size:medium;"><span style="color:#000000;">3.1.POJO</span></span></span></h4>
<p class="western" align="LEFT"><span style="color:#646464;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">@Entity</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#646464;">@Table</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"author_mu"</span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>class</b></span><span style="color:#000000;"> Author {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@Id</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@GeneratedValue</span><span style="color:#000000;">(strategy=GenerationType.</span><span style="color:#0000c0;"><i><b>AUTO</b></i></span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span> <span style="color:#7f0055;"><b>int</b></span> <span style="color:#0000c0;">id</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span><span style="color:#000000;"> String </span><span style="color:#0000c0;">name</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@ManyToMany</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@JoinTable</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"author_book"</span><span style="color:#000000;">,joinColumns=</span><span style="color:#646464;">@JoinColumn</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"author_id"</span><span style="color:#000000;">),</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;"> inverseJoinColumns=</span><span style="color:#646464;">@JoinColumn</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"book_id"</span><span style="color:#000000;">))</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span><span style="color:#000000;"> List&lt;Book&gt; </span><span style="color:#0000c0;">books</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#646464;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">@Entity</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#646464;">@Table</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"book_mu"</span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>class</b></span><span style="color:#000000;"> Book {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@Id</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@GeneratedValue</span><span style="color:#000000;">(strategy=GenerationType.</span><span style="color:#0000c0;"><i><b>AUTO</b></i></span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span> <span style="color:#7f0055;"><b>int</b></span> <span style="color:#0000c0;">id</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span><span style="color:#000000;"> String </span><span style="color:#0000c0;">name</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@ManyToMany</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@JoinTable</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"author_book"</span><span style="color:#000000;">,joinColumns=</span><span style="color:#646464;">@JoinColumn</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"book_id"</span><span style="color:#000000;">),</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;"> inverseJoinColumns=</span><span style="color:#646464;">@JoinColumn</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"author_id"</span><span style="color:#000000;">))</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span><span style="color:#000000;"> List&lt;Author&gt; </span><span style="color:#0000c0;">authors</span><span style="color:#000000;">;</span></span></span></p>

<h4 class="western" align="LEFT"><span style="font-family:'Times New Roman', serif;"><span style="font-size:medium;"><span style="color:#000000;">3.2.test class</span></span></span></h4>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">List&lt;Author&gt; </span><span style="color:#6a3e3e;">authors</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> ArrayList&lt;Author&gt;();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">List&lt;Book&gt; </span><span style="color:#6a3e3e;">books</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> ArrayList&lt;Book&gt;();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">List&lt;Author&gt; </span><span style="color:#6a3e3e;">authors2</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> ArrayList&lt;Author&gt;();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">List&lt;Book&gt; </span><span style="color:#6a3e3e;">books2</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> ArrayList&lt;Book&gt;();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Author </span><span style="color:#6a3e3e;">a1</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> Author();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Author </span><span style="color:#6a3e3e;">a2</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> Author();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">a1</span><span style="color:#000000;">.setName(</span><span style="color:#2a00ff;">"harry"</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">a2</span><span style="color:#000000;">.setName(</span><span style="color:#2a00ff;">"gelin"</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">authors</span><span style="color:#000000;">.add(</span><span style="color:#6a3e3e;">a1</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">authors</span><span style="color:#000000;">.add(</span><span style="color:#6a3e3e;">a2</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">authors2</span><span style="color:#000000;">.add(</span><span style="color:#6a3e3e;">a2</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Book </span><span style="color:#6a3e3e;">b1</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> Book();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">b1</span><span style="color:#000000;">.setName(</span><span style="color:#2a00ff;">"learn java in 30 days"</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Book </span><span style="color:#6a3e3e;">b2</span><span style="color:#000000;"> = </span><span style="color:#7f0055;"><b>new</b></span><span style="color:#000000;"> Book();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">b2</span><span style="color:#000000;">.setName(</span><span style="color:#2a00ff;">"stock market"</span><span style="color:#000000;">); </span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">books</span><span style="color:#000000;">.add(</span><span style="color:#6a3e3e;">b1</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">books2</span><span style="color:#000000;">.add(</span><span style="color:#6a3e3e;">b1</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">books2</span><span style="color:#000000;">.add(</span><span style="color:#6a3e3e;">b2</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">a1</span><span style="color:#000000;">.setBooks(</span><span style="color:#6a3e3e;">books</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">a2</span><span style="color:#000000;">.setBooks(</span><span style="color:#6a3e3e;">books2</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#3f7f5f;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">//b1.setAuthors(authors);</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#3f7f5f;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">//b2.setAuthors(authors2);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">s</span><span style="color:#000000;">.save(</span><span style="color:#6a3e3e;">a1</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">s</span><span style="color:#000000;">.save(</span><span style="color:#6a3e3e;">a2</span><span style="color:#000000;">); </span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">s</span><span style="color:#000000;">.save(</span><span style="color:#6a3e3e;">b1</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">s</span><span style="color:#000000;">.save(</span><span style="color:#6a3e3e;">b2</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">for delete :</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Book </span><span style="color:#6a3e3e;">a</span><span style="color:#000000;"> = (Book)</span><span style="color:#6a3e3e;">s</span><span style="color:#000000;">.load(Book.</span><span style="color:#7f0055;"><b>class</b></span><span style="color:#000000;">, 12); </span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">s</span><span style="color:#000000;">.delete(</span><span style="color:#6a3e3e;">a</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">or </span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">Author </span><span style="color:#6a3e3e;">a</span><span style="color:#000000;"> = (Author)</span><span style="color:#6a3e3e;">s</span><span style="color:#000000;">.load(Author.</span><span style="color:#7f0055;"><b>class</b></span><span style="color:#000000;">, 12); </span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#6a3e3e;">s</span><span style="color:#000000;">.delete(</span><span style="color:#6a3e3e;">a</span><span style="color:#000000;">);</span></span></span></p>