---
layout: post
title: Struts2 Package
categories: struts2
---

### What is Package
Package is a way to group actions, results, result types, interceptors and interceptor-stacks into a logical configuration unit.
Conceptually, package is similar to object in that they can be extended and have individual parts that can be overridden by sub
packages.

Note that the config file is processed sequentially down the document, so the package referenced by an 'extends' should be defined
above the package which extends it.

### Attributes
- name, it is required, its value is used when other package extends it
- extends, inherits other package behavior
- abstract, declare package to be abstract(no action config required)
- namespace

### Namespace
1. default namespace, an empty string - ""
2. root namespace, "/"
3. specified namespace, specified value, e.g. "/message"

Note, specified namespace value must be started with "/".

#### Order
```
specified namespace ---> default namespace

root namespace ---> default namespace
```

namespace is not hierarchical like a file system path.  There is one namespace level, e.g. url /barspace/myspace/bar.action,
struts will first look for namespace /barspace/myspace, if there is no bar action, then the default namespace "".

#### Usage

