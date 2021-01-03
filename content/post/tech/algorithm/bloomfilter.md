---
title: "bloom filter"
date: 2021-01-02T22:53:10+08:00
tags: ["algorithm", "hash"]
categories: ["algorithm"]
draft: true
---

在查看leveldb的代码中的bloom filter，正好可以把bloom filter的理论知识和leveldb的实践代码结合起来看看。
在[wiki](https://en.wikipedia.org/wiki/Bloom_filter)中对于bloom filter的介绍