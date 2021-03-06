---
title: mysql优化
date: 2020/11/06 09:57:25
toc: true
tags:
- mysql
---



#### 从代码角度优化

* 连接池
  * 使用持久的连接数据库以避免连接开销。代码中我们一般使用连接池。
* 减少对MySQL访问
  * 避免对同一数据做重复检索
    * 理清取数逻辑，一次连接取出所有结果
  * 查询缓存 MySQL Query Cache
    * 存储select查询文本以及相应结果，随后收到相同的查询，server直接从查询缓存中取查询结果
    * 适用更新不频繁的表，当表更改(结构或者数据)时，查询缓存值相关的record也会清除
    * 需要指定开启
      * query_cache_size 表明缓存区大小，单位为M
      * query_cache_type 的变量值从0 到2
      * Qcache_queries_in_cache  在缓存中已注册的查询数目
    ```SQL
    show variables like '%query_cache%';
    show status like 'query';
    ```
<!--more-->
  * 应用端增加cache层
    * 例如把部分数据从数据库中抽取出来放在应用端，查询时直接去这个"cache"中检索， 涉及到的问题有数据更新怎么办，多久更新这个"cache"
    * 或者建立一个二级数据库，把访问频度大的数据放到二级库上，然后设定一个机制与主数据库同步，降低主数据库的压力
* 负载均衡
  * 核心思想把固定的负载量分布到不同的server上
  * MySQL 复制分流-- 主从复制
    * 分流update和select操作，即主server承担update，多台从服务器承担select，主从之间通过复制实现同步，多台从服务器一方面确保可用性，另一方面可以创建不同的索引满足不同查询需要
    * 问题：主服务器更新频繁或网络问题，主从之间存在较大延迟更新
* 分布式数据库
  * 适合大数据量，负载高，具有良好的扩展性和高可用性
  * 具体实现，使用MySQL的CLUSTER功能，分布式事务只支持InnoDB engine
* 其他优化思想
  * 字段尽量不用自增属性，因为在高并发情况下，字段的自增可能对效率有影响，推荐在应用中实现字段的自增长


