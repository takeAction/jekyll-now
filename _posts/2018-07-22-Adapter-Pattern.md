---
layout : post
title : Adapter Pattern
comments: true
categories : Java
---

Adapter pattern works as a bridge between two incompatible/unrelated interfaces/classes.

In real life, the card reader is the example. The corresponding diagram looks like next:

![_config.yml]({{ site.baseurl }}/images/adapter.png)

The `read` of `CardReader` will call function of `MemoryCard` to read its data.

From the above diagram, the `CardReader` is adapter, `MemoryCard` named adaptee and the `Slot` called the target.

Adapter pattern contains **object adapter** and **class adapter**. Above diagram shows the object adapter, it use composition.

If `CardReader` implements `Slot` and extends `MemoryCard`, this is class adapter.

`Arrays.asList` is an example of adapter pattern.
