---
title: Front Matter 字段
parent: 文章规范
nav_order: 1
---

# Front Matter 字段

```yaml
---
title: 刷新一下
date: 2026-11-03 13:30:00 +0800
description: 一两句话的摘要，首页卡片和搜索结果里会显示
categories: [杂记, 折腾]
tags: [随笔, 前端]
image: /assets/img/posts/refresh-button/cover.png
pin: false
---
```

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| `title` | 是 | 标题 |
| `date` | 是 | `YYYY-MM-DD HH:MM:SS +0800`，写成未来日期不会发布 |
| `description` | 建议 | 摘要，SEO 与卡片描述用；不写会截正文开头 |
| `categories` | 否 | 分类，一般两个 |
| `tags` | 否 | 标签，可以多个 |
| `image` | 否 | 封面图，可以直接写路径 |
| `pin` | 否 | `true` 会置顶到首页最前面 |

## 封面图想带说明

```yaml
image:
  path: /assets/img/posts/xxx/cover.png
  alt: 封面说明
```

`lqip` 是低清占位图，一般不用手写。

## 摘要别写太长

一句话到两句。首页卡片只显示两三行，写长了会被截。
