---
layout: post
title: Struts2 Package
categories: struts2
---

### What is Package
Package is a way to group actions, results, result types, interceptors and interceptor-stacks into a logical configuration unit.
Conceptually, package is similar to object in that they can be extended and have individual parts that can be overridden by sub
packages.

> Note that the config file is processed sequentially down the document, so the package referenced by an 'extends' should be defined
above the package which extends it.

### Attributes
- name, it is required, its value is used when other package extends it
- extends, inherits other package behavior
- abstract, declare package to be abstract(no action config required)
- namespace

### Namespace
The namespace attribute subdivides action into logical modules, such that avoid conflicts between action names.
Each namespace can have its own "menu" or "help" action, each with its own implementation.

1. default namespace, an empty string - ""
2. root namespace, "/"
3. specified namespace, specified value, e.g. "/message"

**Note, specified namespace value must be started with "/".**

#### Order
```
specified namespace ---> default namespace

root namespace ---> default namespace
```

namespace is not hierarchical like a file system path.  There is one namespace level, e.g. url /barspace/myspace/bar.action,
struts will first look for namespace /barspace/myspace, if there is no bar action, then the default namespace "".

#### Usage
Action will be executed in current namespace if without specifying namespace, e.g.
```
<package name="test" namespace="/test" >
  <action name="query1" ...>
    <result name="result1">/a.jsp</result>
    <result name="result2">b.jsp</result>
  </action>
</package>
```
If web app name is FIN, current namespace is default namespace, and we want to run query1 action, then the url should be **test/query1.action**, the final url is **localhost:8080/FIN/test/query1.action**.  However, if url is **/test/query1.action**,
the final url is **localhost:8080/test/query1.action**.

For result, **/a.jsp** means struts will look for a.jsp under **FIN/**, **b.jsp** means struts look for it under **FIN/test/**.

#### one namespace action call another namesapce action
For example, a.jsp is in namespace N1, we want to call query2.action of namespace N2 from a.jsp. There are some approaches:
1. write the absolutely path in a.jsp, that is **/FIN/N2/query2.action**, wherein FIN is web app name.
2. `<s:form>` can specify the namespace
3. `<s:url>` can specify namespace, e.g. `<s:url action='query2' id='varName' namespace='N2' /><s:a href='%{varName}'>Click</s:a>`

#### Conclusion
For action url, the recommendation is writing the complete url, e.g. **/FIN/N2/query2.action**.
