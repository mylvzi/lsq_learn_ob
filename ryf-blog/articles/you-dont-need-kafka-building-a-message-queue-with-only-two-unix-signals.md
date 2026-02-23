---
title: You don't need Kafka - Building a message queue with only two UNIX signals
date: 2025-10-21
type: articles
source: https://leandronsp.com/articles/you-dont-need-kafka-building-a-message-queue-with-only-two-unix-signals
author: Leandro Proença
tags: [kafka, unix, 消息队列]
---

# You don't need Kafka: Building a message queue with only two UNIX signals

原文链接: [https://leandronsp.com/articles/you-dont-need-kafka-building-a-message-queue-with-only-two-unix-signals](https://leandronsp.com/articles/you-dont-need-kafka-building-a-message-queue-with-only-two-unix-signals)

作者: Leandro Proença

## 内容摘要

这是一篇非常有趣的技术文章，探讨了一个创新性的想法：是否可以用仅两个 UNIX 信号来替代复杂的消息代理（如 Kafka）？

文章深入介绍了：
- 如何利用 UNIX 系统的底层机制构建轻量级消息队列
- 信号（Signals）在进程间通信中的应用
- 为什么传统的消息代理（如 Kafka）对于简单场景可能是过度设计
- 实际代码示例展示如何实现基于信号的消息队列

作者通过这个实验性的项目，展示了在某些场景下，简单的方法往往比复杂的工具更加有效。这篇文章适合对系统编程、消息队列和 UNIX 内部机制感兴趣的开发者阅读。

## 备注

原文为英文博客，发布于 2025 年 10 月 21 日。
