---
title: 所选十项执行与验收
parent: Astro 迁移记录
grand_parent: 建站笔记
nav_order: 5
---

# 2026-11-17 所选十项执行与验收

用户选择：1、2、3、4、5、6、10、14、18、20。迁移前再次备份到本机 .work/iris-before-selected-20261117.bundle。

| 项目 | 本轮处理 | 验收边界 |
| --- | --- | --- |
| 1 搜索浮层 | 修正异步索引文档 ID 一致性、失败索引请求重试、预加载拒绝处理 | 当前聊天浏览器不可用，展开/关闭/重开仍待实测 |
| 2 独立搜索 | 已有标题/摘要/标签过滤、数量与空结果提示；生成页检查 | 中文输入与键盘操作待浏览器实测 |
| 3 图谱与双链 | 新增真实插件与反向引用函数回归，验证链接转换、别名文字、代码块排除和双向引用 | 图谱拖动缩放、嵌套浮层需浏览器实测；未添加虚构文章关联 |
| 4 永久 URL | 从原 Jekyll 生成页读取 17 个真实 canonical，采用原 slug；保留 17 个临时 slug 跳转，并生成 posts 兼容路径 | 静态 Pages 使用生成的 HTML 跳转，不能宣称为服务器 HTTP 301；原根站地址未改动 |
| 5 文章资源 | 全站根路径资源扫描，修正上一篇/下一篇在子路径部署时丢失 blog 层级的问题 | 外部视频实际播放、失效外链受服务限制，单列待验 |
| 6 跨设备 | 保留现有响应式样式 | 工具明确返回 Local browser access is unavailable for this chat，手机/平板/桌面验收未完成 |
| 10 关于与日志 | 完整迁移 about.yml 所有结构化内容、milestones、tools、now、interests 和 changelog.yml 全部条目 | 使用 Astro 原生展示，未复制原 Jekyll 动画；历史日志链接仍回原站 |
| 14 评论 | 移除组件内原作者硬编码，读取统一站点配置，复用原博客 Giscus 仓库与分类；按原文标题 specific 匹配 | 没有发表测试评论；实际讨论匹配和第三方 iframe 仍需浏览器验收 |
| 18 记录 | 本页与机器报告、测试脚本随仓库保存 | 未完成项不标通过 |
| 20 SEO | 文章 canonical 指回原文；关于、念真指回原站；临时站 noindex,follow，robots 允许爬取读取 noindex，输出 sitemap | 正式切换根站前不开放临时副本收录，避免重复内容；原站 SEO 未改动 |

## 验证命令

- npm run build
- node scripts/pages-paths.mjs
- node scripts/pages-paths.test.mjs
- node scripts/content-links.test.mjs
- python -X utf8 scripts/verify-migration.py
- python -X utf8 scripts/audit-static.py

静态报告：迁移库 migration/validation/selected-audit.json。构建通过不等于手机和全部交互验收通过。

## 补充检查

只读查询原仓库 Discussions，确认既有讨论使用完整页面标题（如 MyBlog | Wendell、关于 | Wendell），评论 specific term 因此保留原标题后缀，避免错误创建分散讨论。本轮没有创建讨论或发布评论。搜索、图谱、嵌套预览及分类浏览器的动态地址已接入子目录路径函数，并通过中文路径、重复前缀、锚点和外链回归。外部视频 HEAD 200；Markdown 教程的四张第三方图片 HEAD 403，记录为待浏览器确认的外链风险。

