---
layout: post
title: div popup window
categories: [front-end]
---
There are three ways to make a div as a popup window.
<ol>
	<li>
<p class="western">Jquery</p>
</li>
</ol>
<blockquote>
<ul>
	<li>
<p class="western">make div invisible with css</p>
</li>
	<li>
<p class="western">call Jquery function : $( "#{div id}" ).dialog()</p>
</li>
</ul>
</blockquote>
<ol start="2">
	<li>
<p class="western">Pure CSS</p>
</li>
</ol>
<blockquote>
<ul>
	<li>
<p class="western">create a link whose href is : #{div id}</p>
</li>
	<li>
<p class="western">make div invisible with css</p>
</li>
	<li>
<p class="western">create a css like : .{div class value}:target{}, make div visible in it</p>
</li>
</ul>
</blockquote>
<p class="western">the source looks like :</p>

<blockquote>
<p class="western">.overlay {</p>
<p class="western">visibility: hidden;</p>
<p class="western">opacity: 0;</p>
<p class="western">}</p>
<p class="western">.overlay:target {</p>
<p class="western">visibility: visible;</p>
<p class="western">opacity: 1;</p>
<p class="western">}</p>

<div class="box">

'&lt;a href="#popup"&gt;test&lt;/a&gt;'

<em><strong><div class="overlay" id="popup">div content</div></strong></em>

</div>
<div id="popup1" class="overlay">
<p class="western"></p>

</div></blockquote>
<ol start="3">
	<li>
<p class="western">CSS+Javascript</p>
</li>
</ol>
<blockquote>
<ul>
	<li>
<p class="western">create a button or link whose onclick has value(js function)</p>
</li>
	<li>
<p class="western">in js function, we use : document.getElementById('{div id}').style.display = to make div visible or hidden</p>
</li>
	<li>
<p class="western">create a div and make it invisible with css</p>
</li>
</ul>
</blockquote>
