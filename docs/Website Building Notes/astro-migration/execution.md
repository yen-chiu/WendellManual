---
title: 执行与备份
parent: Astro 迁移记录
grand_parent: 建站笔记
nav_order: 1
---

# 2026-11-17 执行与备份

## 范围

用户授权夜间自主调试与渐进迁移，结果推送私有临时库，记录进入 WendellManual。域名、线上部署、云端接口与聊天数据不改动。

## 备份

本机 C:/03_Github/.work/ 保存三个 Git bundle：iris-before-migration-20261117.bundle、jekyll-before-migration-20261117.bundle、manual-before-migration-20261117.bundle。bundle 保存已提交历史；源仓库检查为干净。原始 npm 锁文件另存 iris-original-package-lock-20261117.json。

恢复时可在新目录 git clone 对应 bundle，避免覆盖现有仓库。新库 upstream 保留 Iris 原始远端；原始基线提交为 56f5cfee0d39c08649a714da68a53200c7600223。

## 内容分类

- 念真：独立 /persona 页面，保留原始正文、角色图片与专用 CSS；聊天按钮暂回线上原站。
- 文章：17 篇公开文章转换为 Astro 内容集合；原始元数据、来源 SHA-256 与新地址收录于 migration/content-manifest.json。
- 示例：Iris 原作者文章、文档及部分个人页面移入 migration/upstream-examples，退出生产内容集合。
- 多模态日志：保留原 HTML 页面。
- 聊天、工具、后台与游戏：本轮不复制运行时或密钥，入口保留原站链接。

## 风险控制

停用继承的自动部署和友链写入 workflow；禁用原作者 Giscus，删除原作者备案展示，临时 canonical 使用 example.invalid，robots 禁止抓取。未绑定域名，未开启 Pages。

## 阶段收尾

最终清理后生成 90 个静态页面，补齐日志索引和模板文档残留链接。没有切换线上站点。临时库内容与记录分别提交推送；提交编号见 Git 历史。

