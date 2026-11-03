---
title: 站点结构
parent: 博客使用说明
nav_order: 5
---

# 站点结构

| 位置 | 是什么 | 怎么更新 |
| --- | --- | --- |
| `yenchiu.com` | 主站，仓库 `yen-chiu.github.io` | 推 `master`，Actions 自动构建发布 |
| `yenchiu.com/admin/` | 写作台，就在主站仓库里 | 同上 |
| `yenchiu.com/chat/` | 「来聊聊吧」，静态文件在主站仓库的 `chat/` | 改 Chat 仓库后同步过来 |
| `yenchiu.com/WendellManual/` | 这份文档，独立仓库 | 推 `master`，它自己的 Actions 构建 |
| `media.yenchiu.com` | Cloudflare R2，放视频和大文件 | wrangler 上传 |
| `yenchiu.com/refresh/` | 清缓存并重新加载 | 打开就用，不用维护 |

## 两个容易搞混的地方

**Manual 站在主域下面，但不是主站的一部分。**
它有自己的仓库、自己的构建、自己的 404。主站推代码不会影响它，反过来也一样。
只是因为这个账号给 GitHub Pages 绑了 `yenchiu.com`，项目站才会挂在主域的路径下。

**「来聊聊吧」有两份。** `Chat` 仓库是源码主本，主站里的 `chat/` 是发布副本。
改完记得同步，否则两边会不一致。

## 一句提醒

改完看不到新东西，先点页脚「刷新缓存」，或者打开 `/refresh/`。
手机浏览器留缓存，留得比你想的久。
