---
title: Git fundamentals, a complete guide
date: 2023-03-15
type: articles
source: https://leandronsp.com/articles/git-fundamentals-a-complete-guide
author: Leandro Proença
tags: [git, linux]
---

# Git fundamentals, a complete guide

原文链接: [https://leandronsp.com/articles/git-fundamentals-a-complete-guide](https://leandronsp.com/articles/git-fundamentals-a-complete-guide)

作者: Leandro Proença

## 内容摘要

这是一篇关于 Git 基础知识的完整指南，适合每天使用 Git 但想深入理解其内部工作原理的开发者。文章详细介绍了 Git 的架构设计，包括对象数据库（Object Database）、引用系统（refs）、HEAD 指针等核心概念。

通过阅读本文，你将真正理解 **add、checkout、reset、commit、merge、rebase、cherry-pick、pull、push** 和 **tag** 等常用命令的内部工作机制。文章强调"不要让 Git 驾驭你，而要驾驭 Git"，帮助读者从使用者进阶为掌握者。

主要内容涵盖：
- Git 的两种命令类型：plumbing（底层命令）和 porcelain（高层命令）
- Git 对象数据库的内部结构
- SHA-1 哈希函数在 Git 中的应用
- .git 目录的核心组件解析
- 各命令的底层原理和使用场景

## 备注

原文为英文博客，发布于 2023 年 3 月 15 日。
