---
title: 项目页作品展示与补充链接
parent: Astro 迁移记录
grand_parent: 建站笔记
nav_order: 17
---

# 2026-11-18 项目页作品展示与补充链接

## 需求与资料

用户反馈项目页只有两个链接、过于简陋；随后明确补充“生命线网页版”和 https://996.ninja/。整合真实项目资料，没有引入星标/访问量等远程指标。

项目资料来自现有念真介绍、miko-web 文章、chat/play.html 的应用映射，以及 chat/apps/lifeline-web/README.md。核对 [996忍者](https://996.ninja/) 为摸鱼主题工具与小游戏网站，与生命线入口分开收录。

## 实现

- 数据独立存放 src/data/projects.ts，展示页只负责排版。
- 交互作品：念真、异次元通讯网页版、生命线网页版；网站与文档：Astro 博客、WendellManual。统计自动从数据计算为5/3/2。
- 卡片包含类型、入口说明、简介、特点标签、体验/介绍/源码/文档入口。生命线依据现有 README 展示消息叙事、分支选择、多语言与存档。
- 念真和生命线使用原生 CSS/SVG 的主题封面；异次元通讯使用用户本轮提供的夜景聊天封面。没有增加图像生成依赖或第三方头像请求。
- 桌面三项交互作品同排，手机单列，主要按钮至少44px；移除项目索引页无意义的 Back 和大段留白。公共头部使用本批紧凑设计。
- 原站入口：聊天 /chat/；异次元通讯 /chat/play.html?a=miko；生命线 /chat/play.html?a=lifeline。后两个保持原站运行时，不搬动 chat 工作区。
- 链接页增加996忍者；第二分组改名“参考与发现”，包含开源主题、常读内容和趣味网站，当前6条，全部收录10条。

## 验证与记录

本地生产构建136页通过，类型诊断0错误/0警告、2个既有提示，静态资源无缺失。项目页320/390/1280px无横向溢出，卡片主按钮高44px；桌面交互作品三列，手机单列，Miko封面实际加载。生命线卡片正确指向 a=lifeline。

聊天、miko壳、miko实际页面、lifeline壳和实际页面均以HTTP读取确认200；本批没有重新游玩游戏或测试第三方AI服务。链接页名称搜索“996忍者”命中对应卡片，URL正确。

截图 .work/iris-projects-complete.jpg、iris-projects-mobile.jpg。验收详见[ui-density-projects-qa.json](https://github.com/yen-chiu/yen-chiu-astro/blob/main/migration/validation/ui-density-projects-qa.json)。用户桌面原头像、原站根部署、聊天数据均未改动；本批站点备份 .work/iris-before-ui-density-20261118.bundle。


## 整体 UI 发布与线上验证

整体 UI、项目页、生命线与996忍者提交 [03750a4](https://github.com/yen-chiu/yen-chiu-astro/commit/03750a4c5152cb4bc327611cebe1c58c6aee4628)，[Pages 44241160707](https://github.com/yen-chiu/yen-chiu-astro/actions/runs/44241160707) build/deploy success。

线上9页及28项资源均HTTP200，覆盖项目/文档/标签/分类与图谱动态模块。应用内联脚本和编译/二进制资源一致；manifest按JSON校验，标签图谱数据的节点/边集合一致（Windows/Linux默认locale造成同频标签顺序差异），Cloudflare注入检测脚本单独识别。报告 .work/iris-ui-final-online.json。


## 用户指定异次元通讯封面

用户随后提供 Miko 与夜景聊天画面的封面附件，用于替换项目卡片的章节图。原始附件保留为 src/assets/projects/miko-cover.png；网页导出为 public/images/projects/miko-cover.webp，1280×720。原文章章节图片继续保留。转换仅做WebP格式导出，画面内容未重绘。


封面提交 [8c6a679](https://github.com/yen-chiu/yen-chiu-astro/commit/8c6a679e57808097909ddc7d705a66186f2cc17d)，[Pages 44242380511](https://github.com/yen-chiu/yen-chiu-astro/actions/runs/44242380511) build/deploy success。手机实际加载新图，未发生横向溢出；预览 .work/iris-miko-cover-mobile.jpg。

最终线上9页及29项资源通过，图谱动态模块/项目图片和其他编译资源字节一致，JSON数据按既定方式核对。项目HTML明确引用新封面和生命线入口。报告 .work/iris-miko-cover-online.json。已关闭本地预览进程并恢复临时视口。
