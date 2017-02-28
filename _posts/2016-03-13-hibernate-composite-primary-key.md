---
layout: post
title: Hibernate composite primary key
date: 2016-03-13 21:49
author: 2freesky
comments: true
categories: [Hibernate]
---
<p class="western">There are three ways to define composite primary key in hibernate, but it is recommended that @EmbeddedId should be used.</p>
<p class="western">1.Model class</p>
<p class="western"><span style="color:#646464;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">@Entity</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#646464;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;">@Table</span><span style="color:#000000;">(name=</span><span style="color:#2a00ff;">"compositepk"</span><span style="color:#000000;">)</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#7f0055;"> <span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><b>public</b></span> <span style="color:#7f0055;"><b>class</b></span><span style="color:#000000;"> Car {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@EmbeddedId</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span><span style="color:#000000;"> CarPK </span><span style="color:#0000c0;">carPK</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span><span style="color:#000000;"> String </span><span style="color:#0000c0;">name</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span><span style="color:#000000;"> CarPK getCarPK() {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>return</b></span> <span style="color:#0000c0;">carPK</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>void</b></span><span style="color:#000000;"> setCarPK(CarPK </span><span style="color:#6a3e3e;">carPK</span><span style="color:#000000;">) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>this</b></span><span style="color:#000000;">.</span><span style="color:#0000c0;">carPK</span><span style="color:#000000;"> = </span><span style="color:#6a3e3e;">carPK</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span><span style="color:#000000;"> String getName() {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>return</b></span> <span style="color:#0000c0;">name</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>void</b></span><span style="color:#000000;"> setName(String </span><span style="color:#6a3e3e;">name</span><span style="color:#000000;">) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>this</b></span><span style="color:#000000;">.</span><span style="color:#0000c0;">name</span><span style="color:#000000;"> = </span><span style="color:#6a3e3e;">name</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@Override</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>boolean</b></span><span style="color:#000000;"> equals(Object </span><span style="color:#6a3e3e;">obj</span><span style="color:#000000;">) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>if</b></span><span style="color:#000000;">(</span><span style="color:#6a3e3e;">obj</span> <span style="color:#7f0055;"><b>instanceof</b></span><span style="color:#000000;"> Car) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;"> Car </span><span style="color:#6a3e3e;">c</span><span style="color:#000000;"> = (Car)</span><span style="color:#6a3e3e;">obj</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>return</b></span> <span style="color:#7f0055;"><b>this</b></span><span style="color:#000000;">.</span><span style="color:#0000c0;">carPK</span><span style="color:#000000;">.equals(</span><span style="color:#6a3e3e;">c</span><span style="color:#000000;">.</span><span style="color:#0000c0;">carPK</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>return</b></span> <span style="color:#7f0055;"><b>false</b></span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@Override</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>int</b></span><span style="color:#000000;"> hashCode() {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>return</b></span> <span style="color:#7f0055;"><b>this</b></span><span style="color:#000000;">.</span><span style="color:#0000c0;">carPK</span><span style="color:#000000;">.hashCode();</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@Embeddable</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>static</b></span> <span style="color:#7f0055;"><b>class</b></span><span style="color:#000000;"> CarPK </span><span style="color:#7f0055;"><b>implements</b></span><span style="color:#000000;"> Serializable{</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span> <span style="color:#7f0055;"><b>static</b></span> <span style="color:#7f0055;"><b>final</b></span> <span style="color:#7f0055;"><b>long</b></span> <span style="color:#0000c0;"><i><b>serialVersionUID</b></i></span><span style="color:#000000;"> = -5202331188724915048L;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span><span style="color:#000000;"> Integer </span><span style="color:#0000c0;">chassisNumber</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>private</b></span><span style="color:#000000;"> Integer </span><span style="color:#0000c0;">engineNumber</span><span style="color:#000000;">; </span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span><span style="color:#000000;"> Integer getChassisNumber() {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>return</b></span> <span style="color:#0000c0;">chassisNumber</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>void</b></span><span style="color:#000000;"> setChassisNumber(Integer </span><span style="color:#6a3e3e;">chassisNumber</span><span style="color:#000000;">) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>this</b></span><span style="color:#000000;">.</span><span style="color:#0000c0;">chassisNumber</span><span style="color:#000000;"> = </span><span style="color:#6a3e3e;">chassisNumber</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span><span style="color:#000000;"> Integer getEngineNumber() {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>return</b></span> <span style="color:#0000c0;">engineNumber</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>void</b></span><span style="color:#000000;"> setEngineNumber(Integer </span><span style="color:#6a3e3e;">engineNumber</span><span style="color:#000000;">) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>this</b></span><span style="color:#000000;">.</span><span style="color:#0000c0;">engineNumber</span><span style="color:#000000;"> = </span><span style="color:#6a3e3e;">engineNumber</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@Override</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>boolean</b></span><span style="color:#000000;"> equals(Object </span><span style="color:#6a3e3e;">obj</span><span style="color:#000000;">) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>    if</b></span><span style="color:#000000;">(</span><span style="color:#6a3e3e;">obj</span> <span style="color:#7f0055;"><b>instanceof</b></span><span style="color:#000000;"> CarPK) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">        CarPK </span><span style="color:#6a3e3e;">car</span><span style="color:#000000;"> = (CarPK)</span><span style="color:#6a3e3e;">obj</span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>    if</b></span><span style="color:#000000;">(</span><span style="color:#7f0055;"><b>this</b></span><span style="color:#000000;">.getChassisNumber().intValue() == </span><span style="color:#6a3e3e;">car</span><span style="color:#000000;">.getChassisNumber().intValue() &amp;&amp;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>        this</b></span><span style="color:#000000;">.getEngineNumber().intValue() == </span><span style="color:#6a3e3e;">car</span><span style="color:#000000;">.getEngineNumber().intValue()) {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>        return</b></span> <span style="color:#7f0055;"><b>true</b></span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#000000;">    } </span><span style="color:#7f0055;"><b>else</b></span><span style="color:#000000;"> {</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"><span style="color:#7f0055;"><b>    return</b></span> <span style="color:#7f0055;"><b>false</b></span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">    }</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>return</b></span> <span style="color:#7f0055;"><b>false</b></span><span style="color:#000000;">;</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#646464;">@Override</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>public</b></span> <span style="color:#7f0055;"><b>int</b></span><span style="color:#000000;"> hashCode() {</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#3f7f5f;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">// return this.chassisNumber.hashCode()+this.engineNumber.hashCode();</span></span></span></p>
<p class="western" align="LEFT"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> <span style="color:#7f0055;"><b>return</b></span><span style="color:#000000;"> Objects.</span><span style="color:#000000;"><i>hash</i></span><span style="color:#000000;">(</span><span style="color:#7f0055;"><b>this</b></span><span style="color:#000000;">.</span><span style="color:#0000c0;">chassisNumber</span><span style="color:#000000;">,</span><span style="color:#7f0055;"><b>this</b></span><span style="color:#000000;">.</span><span style="color:#0000c0;">engineNumber</span><span style="color:#000000;">);</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> }</span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;"> } </span></span></span></p>
<p class="western" align="LEFT"><span style="color:#000000;"><span style="font-family:'Courier New', monospace;"><span style="font-size:small;">}</span></span></span></p>
<p class="western" align="LEFT">2.test class</p>
<p class="western" align="LEFT">    The hql looks like : from Car c where c.carPK.chassisNumber=2 and c.carPK.engineNumber=2016</p>
<p class="western" align="LEFT">    or</p>
<p class="western" align="LEFT">    Car.CarPK pk = new Car.CarPK();
pk.setChassisNumber(3);
pk.setEngineNumber(2017);</p>
<p class="western" align="LEFT">    Car c1 = (Car) s.get(Car.class, pk);</p>
<p class="western" align="LEFT"><span style="color:#ff0000;"><strong>Note : </strong></span></p>

<ol>
	<li class="western"><span style="color:#ff0000;"><strong>Both Car and CarPK have to implement equals() and hashCode()</strong></span></li>
	<li class="western"><span style="color:#ff0000;"><strong>CarPK must implement Serialiazble</strong></span></li>
</ol>
3. The reason we have to override equals() and hashCode()

In java we can compare two objects using == which returns true if both objects have the same memory address.           And this is the behavior of equals() of Object.class.

However, we should use equals() to do comparison in our business case, for example, if two Car have same chassiseNumber and engineNumber, then they are the same, therefore we need to override the equals() to reach this objective.

hashCode() returns hash value which is used in hash collection such as HashTable, HashMap etc.

In HashMap, it adds the element to map if this hash value doesn't exist. If this hash value exist, then it will call the equals() to compare these two objects are equal or not, if equal, replace the old value associated with this hash code with new value.

The default implementation of hashCode() of Object.class is an integer value of memory address of object.

<strong><em>In general, if you override one of these methods, you must override both.  If two objects are equal according to the equals(), they must have the same hashCode() value, although the reverse is not generally true.</em></strong>

More detail, please refer to : https://developer.jboss.org/wiki/EqualsAndHashCode?_sscc=t