---
title: Improve Runtime Type Safety with Branded Types in TypeScript
date: 2023-05-30
type: articles
source: https://egghead.io/blog/using-branded-types-in-typescript
---

# Improve Runtime Type Safety with Branded Types in TypeScript

原文链接: [https://egghead.io/blog/using-branded-types-in-typescript](https://egghead.io/blog/using-branded-types-in-typescript)

## 内容摘要

TypeScript虽能提供类型检查，但基础类型（如string）过于通用。例如，用户ID、帖子ID、评论ID都是string类型，TypeScript无法区分它们，容易导致参数顺序错误等运行时问题。

文章介绍了 Branded Types（品牌类型）解决方案，通过交叉类型和 unique symbol 创建带"品牌"的类型：

```typescript
declare const __brand: unique symbol
type Brand<B> = { [__brand]: B }
export type Branded<T, B> = T & Brand<B>
```

使用方式：
- `type UserID = Branded<string, "UserId">`
- `type PostID = Branded<string, "PostId">`

**三大优势**：
1. **清晰性**: 更明确变量的预期用途
2. **安全性**: 便于捕获类型不兼容错误
3. **可维护性**: 减少代码歧义，便于他人理解

**实际应用场景**：
- 自定义验证（如EmailAddress品牌类型验证邮箱格式）
- 领域建模（如汽车制造中的CarBrand、EngineType等）
- API响应处理（区分成功/失败响应的品牌类型）

## 备注

作者：Matías Hernández
发布日期：2023年5月30日

文章末尾提供了实践挑战：创建一个Age品牌类型，需限制在0-125岁区间，并实现createAge和getBirthYear函数。
