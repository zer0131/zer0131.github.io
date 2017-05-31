---
layout: post
title: Elasticsearch初探
category: technology
keywords: Elasticsearch,java,入门
description: 公司数据迁移至Elasticsearch，初步认识Elasticsearch。
tags: Elasticsearch java 入门
---

* content
{:toc}

## 背景

由于公司业务不断发展，前段时间对公司的订单、行程等数据“迁移”至Elasticsearch。订单、行程等数据在MySQL中都是以分表的形式存储的，这样很不利于mis端的查询。

之前的做法是我们建立一个索引表，将查询条件作为字段存储下来，这样有两个弊端：

1. 当业务数据量越来越大时，对于查询，效率会降低，不利于查询条件字段的扩展。
2. 会增加开发主流程业务的同学工作量，他们不得不将主库数据同步一份到索引表中。

经过调研，我们选择将主库中的数据同步到Elasticsearch，作为开源的分布式搜索引擎，解决了我们后期数据维护、搜索、扩展的难题。而且我们还有专门的团队对Elasticsearch维护。

## Elasticsearch概念

### 简介

Elasticsearch是基于Lucene的开源搜索引擎，<a href="http://lucene.apache.org/" target="_blank">了解Lucene</a>，Elasticsearch以Lucene为核心实现了索引和搜索。值得提的一点是Elasticsearch屏蔽了Lucene的复杂性并以RESTful API的形式提供读写等操作。Elasticsearch是**面向文档**的，它以JSON的形式存储数据，并索引每个文档。
