---
sidebar_position: 1
---

# 介绍

CnosDB 是一款高性能、高压缩率、高易用性的开源分布式时序数据库。主要应用场景为物联网，工业互联网，车联网，IT运维。所有代码均已在 [GitHub](https://github.com/cnosdb/cnosdb) 开源。

我们在设计上充分利用了时序数据特点，包括结构化数据、无事务、较少的删除更新、写多读少等等，因此相比其它时序数据库，CnosDB 有以下特点：
* **高性能**：CnosDB 解决了时间序列膨胀，理论上支持时间序列无上限，支持沿时间线的聚合查询，包括按等间隔划分窗口的查询、按某列枚举值划分窗口的查询、按相邻时序记录的时间间隔长度划分窗口。具备对最新数据的缓存能力，并且可以配置缓存空间，能够高速获取最新数据。
* **简单易用**：CnosDB 提供清晰明了的接口，简单的配置项目，支持标准SQL，轻松上手，与第三方工具生态无缝集成，拥有便捷的数据访问功能。支持 schemaless （"无模式"）的写入方式，支持历史数据补录（含乱序写入）
* **云原生**：CnosDB 有原生的分布式设计、数据分片和分区、存算分离、Quorum 机制、Kubernetes 部署和完整的可观测性，具有最终一致性，能够部署在公有云、私有云和混合云上。提供多租户的功能，有基于角色管理的权限分配。支持计算层无状态增减节点，储存层水平扩展提高系统存储容量。
