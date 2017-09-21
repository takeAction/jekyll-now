### What is load balance

![_config.yml]({{ site.baseurl }}/images/load-balance.png)

As above diagram shows, distribute multiple client requests to different servers, as known as server farm or server pool,
is the load balance.

### Why

Web application is visited by hundreds of thousands concurrent clients, even millions. In order to meet this high
volume efficiently, more servers are added to serve this case.

Therefore it optimize resource use, maxmize throughput, minimize response time and avoid overload of single server.
This increase reliability and availability through redundancy.

### How

In this scenario, if one server goes down, the load balancer redirects request to the remaining online servers.  When a new server is added,
the load balancer automatically send requests to it.

Load balancer typically include:

- Hardware-based, vendors load special software onto the machine they provide, which ofter uses specialized processors.
  To deal with increasing traffic at your website, you have to buy more or bigger machines from vendor.
- Software-based, it generally run on commodity hardware, achieve similar function with less expensive and more flexible.

