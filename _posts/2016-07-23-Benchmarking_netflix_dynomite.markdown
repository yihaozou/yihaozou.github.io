---
author: 邹政华
comments: true
date: 2016-07-13
published: false 
tags: Dynomite
layout: post
title:  Netlix Dynomite性能基准测试，基于AWS和Redis
categories: 行业
---

去年，Netflix云数据库(CDE)团队推出了[Dynomite](http://techblog.netflix.com/2014/11/introducing-dynomite.html)。Dynomite是一个代理层，旨在将任意非分布式的数据库转化为分片、多区域的分布式数据库系统。Dynomite提供高可用性，通过[Active-Active](http://techblog.netflix.com/2013/12/active-active-for-multi-regional.html)设置，即便AWS中整个region失效了，仍然能够正常工作。

Netflix已经在生产系统里部署了Dynomite。在系列博客文章中，Netflix详细描述了Dynomite的使用场景和特性。例如在[第一篇](http://techblog.netflix.com/2016/01/dynomite-with-redis-on-aws-benchmarks_14.html)里，我们可以了解Dynomite帮助Redis在AWS上线性扩展的细节。

Dynomite能够将最终一致性(eventual consistency)扩展为[协调一致性](https://github.com/Netflix/dynomite/wiki/Consistency)(tunable consistency)。使用DC_ONE模式，读写行为在local Availability Zone(AZ)下是同步的；使用DC_QUORUM模式，本地区域特定数量结点下的操作是同步的。

由测试得到的结果，Dynomite能从3，6，12，24一路扩展到48个节点，在DC_ONE和DC_QUORUM模式下，吞吐率都能线性地增长。与此同时，Dynomite在延迟方面只增加了很少的开支，即便在DC_QUORUM模式下，(延迟)也只有几毫秒。DC_QUORUM模式在延迟和吞吐量方面处于劣势，但是能为客户提供更好的读写保证。


最后，Dynomite也支持Redis[管道](http://redis.io/topics/pipelining)，能够批量地处理请求，从而提升高达50%的性能。Dynomite通过提升吞吐量来减少延迟，从用户的角度讲，这是具有现实意义的。

该[博客系列](http://techblog.netflix.com/2016/01/dynomite-with-redis-on-aws-benchmarks_14.html)的下一篇文章，是关于Dynomite中的逆熵处理问题，以及如何获取更好的一致性。