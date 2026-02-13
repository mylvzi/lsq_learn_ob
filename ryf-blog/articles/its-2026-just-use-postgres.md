---
title: It's 2026, Just Use Postgres
date: 2026-02-02
type: articles
source: https://www.tigerdata.com/blog/its-2026-just-use-postgres
---

# It's 2026, Just Use Postgres

原文链接: [https://www.tigerdata.com/blog/its-2026-just-use-postgres](https://www.tigerdata.com/blog/its-2026-just-use-postgres)

## 内容摘要

文章主张使用 PostgreSQL 替代多个专用数据库。作者认为"使用正确的工具做正确的事"这一建议实际上是个陷阱，导致团队需要管理 7 个不同的数据库系统。AI 时代使数据库分散问题更加严重，测试环境搭建变得极其复杂。Postgres 扩展已具备与专用数据库相当的算法：pg_textsearch 使用 BM25 排名、pgvectorscale 采用 DiskANN 算法性能优于 Pinecone、TimescaleDB 匹配 InfluxDB。多数据库带来隐性成本：7 倍备份、监控、安全补丁、运维负担。作者建议："Start with Postgres. Stay with Postgres. Add complexity only when you've earned the need for it."

## 备注

- 作者核心观点：PostgreSQL 的扩展生态已经足够强大，可以替代大多数专用数据库
- 提到的 Postgres 扩展：
  - pg_textsearch：全文搜索（BM25 算法）
  - pgvectorscale：向量搜索（DiskANN 算法）
  - TimescaleDB：时序数据库
- 文章日期：2026-02-02
