---
layout : post
title : Spring Scope Proxy
categories : Spring
---

### Scope Proxy

  Following content is from **Craig Walls 沃尔斯. Spring实战（第4版） (Kindle Locations 2372-2375). 人民邮电出版社.**

  > 我们 先来 看一下 proxyMode 所 解决问题 的 场景。 假设 我们 要将 ShoppingCart bean 注入 到 单 例 StoreService bean 的 Setter 方法 中， 
  > 如下 所示：
  > @Component 
  > public class StoreService { 
  > @Autowired 
  > public void setShoppingCart( ShoppingCart shoppingCart) { 
  > this. shoppingCart = shoppingCart; 
  > } 
  > ... }
  > 因为 StoreService 是 一个 单 例 的 bean， 会在 Spring 应用 上下文 加载 的 时候 创建。 当它 创建 的 时候， 
  > Spring 会 试图 将 ShoppingCart bean 注入 到 setShoppingCart() 方法 中。 但是 ShoppingCart bean 是 会话 作用域 的， 
  > 此时 并不 存在。 直到 某个 用户 进入 系统， 创建 了 会话 之后， 才会 出现 ShoppingCart 实例。 
  > 另外， 系统 中将 会有 多个 ShoppingCart 实例： 每个 用户 一个。 我们 并 不想 让 Spring 注入 某个 固定 的 ShoppingCart 实例 
  > 到 StoreService 中。 我们 希望 的 是 当 StoreService 处理 购物 车 功能 时， 它 所 使用 的 ShoppingCart 实例 恰好 是 
  > 当前 会话 所 对应 的 那 一个。 
  > Spring 并 不会 将 实际 的 ShoppingCart bean 注入 到 StoreService 中， Spring 会 注入 一个 到 ShoppingCart bean 的 代理， 
  > 这个 代理 会 暴露 与 ShoppingCart 相同 的 方法，
  > 所以 StoreService 会 认为 它 就是 一个 购物 车。 但是， 当 StoreService 调用 ShoppingCart 的 方法 时， 
  > 代理 会对 其 进行 懒 解析 并将 调用 委托 给 会话 作用域 内 真正 的 ShoppingCart bean。 现在， 我们 带着 对 这个 作用域 的 理解， 
  > 讨论 一下 proxyMode 属性。 如 配置 所示， proxyMode 属性 被 设置 成了 ScopedProxyMode. INTERFACES， 
  > 这 表明 这个 代理 要 实现 ShoppingCart 接口， 并将 调用 委托 给 实现 bean。 如果 ShoppingCart 是 接口 而 不是 类 的 话， 
  > 这是 可以 的（ 也是 最为 理想 的 代理 模式）。 
  > 但 如果 ShoppingCart 是一 个 具体 的 类 的 话， Spring 就 没有 办法 创建 基于 接口 的 代理 了。 此时， 
  > 它 必须 使用 CGLib 来生 成 基于 类 的 代理。 所以， 如果 bean 类型 是 具体 类 的 话， 我们 必须 要将 proxyMode 属性 设置
  > 为 ScopedProxyMode. TARGET_ CLASS， 以此 来 表明 要以 生成 目标 类 扩展 的 方式 创建 代理。 
  > 尽管 我 主要 关注 了 会话 作用域， 但是 请求 作用域 的 bean 会面 临 相同 的 装配 问题。 
  > 因此， 请求 作用域 的 bean 应该 也 以 作用域 代理 的 方式 进行 注入。
  
  ![_config.yml]({{ site.baseurl }}/images/spring-scope-proxy.png)

### Usage

  `@Scope( value= WebApplicationContext. SCOPE_ SESSION, proxyMode= ScopedProxyMode. INTERFACES )` or
  `< bean id=".." class=".." scope="session"> < aop: scoped- proxy /> </ bean>`, this will create proxy base on class.
  
  While `< aop: scoped- proxy proxy- target- class=" false" />` will create proxy base on interface.
