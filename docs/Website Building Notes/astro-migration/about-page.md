---
title: 关于页完整移植与排版优化
parent: Astro 迁移记录
grand_parent: 建站笔记
nav_order: 10
---

# 2026-11-17 关于页完整移植与排版优化

## 内容来源与范围

对照原博客 _tabs/about.md、_includes/about/ 下五个分区、_data/about.yml、_data/changelog.yml 和公开联系配置，补齐 Astro 关于页。个人资料 JSON 与原 YAML 内容一致，30 条精选更新日志的内容及顺序也一致，不修改原博客源文件。

补回头像资料卡、兴趣、最近在做、常用工具、站点事实、MyBlog 引语、小游戏入口、近期三篇文章、站点历程、更新日志与留言入口。统计从 Astro 实际公开文章集合计算，近期文章随集合更新。当前显示 17 篇文章、16 个分类路径、52 个标签；分类路径口径与临时站分类索引一致，包含父级目录。

站点事实准确区分当前 Astro + Iris 与原站 Jekyll + Chirpy。写作台仍留在原站，本轮没有迁移其运行时。邮箱来自原站公开 social.email，游戏入口保留原站 /chat/play.html?a=miko。

## 排版与行为

- 沿用现有淡紫色主题，桌面分栏介绍，手机改为紧凑头像资料卡；正文、引语、工具与时间线建立清晰层次。
- 增加五个章节锚点与返回顶部入口。默认展示最近 3 条更新，其余 27 条用原生 details 展开；没有 JavaScript 时仍可阅读和展开。
- 更新类型显示中文标签；日志内已迁移文章链接改到临时站，外部发布页等仍保留原地址。
- 主按钮使用白字深紫背景，优化手机单行按钮空间；所有交互保留键盘焦点样式。没有循环打字机、滚动隐藏内容或外部字体依赖，减少动效干扰。
- 关于页留言板展开后才加载 Giscus，复用原讨论 term=关于 | Wendell。文章页保持原有预加载策略。反复展开不会重复插入脚本。
- Astro 客户端路由的同页锚点不一定触发 hashchange；补上章节点击和 astro:page-load 处理，点击“留言”时同步展开留言板。自定义元素离开页面后中止事件监听器。
- 保留原站 canonical、临时站 noindex 以及全站菜单、知识图谱、页脚运行时间和刷新行为。

## 验收记录

最终构建 136 页，类型检查 0 错误 / 0 警告，静态审计无本地缺失链接；迁移源校验 17 篇文章一致，57 个人页资源引用无缺失。路径回归通过。

Chrome 实测：320、390、768、1280 CSS 像素宽度无横向溢出；头像完整加载（naturalWidth=200）；历史更新可展开、收起；留言展开前脚本数为 0，展开后为 1，反复收放不重复加载。已确认原仓库及 term=关于 | Wendell 的 iframe 参数。

点击文章入口进入预览博客，再从页头返回关于页，留言仍保持延迟加载；点击章节“留言”同步展开成功。深色主题实测正常，测试后恢复原先 system 主题；返回顶部按钮使 scrollY=0；临时视口已成功恢复。

手机与桌面截图分别保存至 C:/03_Github/.work/iris-about-mobile-final.jpg、iris-about-desktop-final.jpg。详细证据：[about-page-qa.json](https://github.com/yen-chiu/yen-chiu-astro/blob/main/migration/validation/about-page-qa.json)。手机视口模拟不代替真机测试；评论正文和历史讨论显示仍未验证，本轮没有发送评论或邮件。

## 备份

- 迁移库修改前：C:/03_Github/.work/iris-before-about-20261117.bundle。
- 文档库修改前：C:/03_Github/.work/manual-before-about-20261117.bundle。

保留 Git 历史。原站、聊天工作区、DNS 和根站 Pages 配置均未改变。

## 发布结果

网站提交 [2cb9be1](https://github.com/yen-chiu/yen-chiu-astro/commit/2cb9be13ec43b5ba3e19a0dd8214c5e73167cdb0) 已推送 main；[Actions 44050787968](https://github.com/yen-chiu/yen-chiu-astro/actions/runs/44050787968) 的 build 与 deploy 均 completed/success。推送过程中曾返回网络 EOF，随后 ls-remote 确认远端提交一致，没有重复强制推送。

[新版关于页](https://yenchiu.com/yen-chiu-astro/about/?v=2cb9be1) 返回 HTTP 200，包含新版介绍结构与延迟留言属性，30 条日志标题均存在，canonical 仍指向原站 https://yenchiu.com/about/。检查到的 15 个资源地址均返回 200，其中 11 份本地 JS/CSS 正文与验收构建一致，另有两份头像及 Cloudflare/Giscus 脚本。浏览器交互结论来自本地生产构建，线上以部署结果、内容和资源一致性验证。
