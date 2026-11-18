---
title: 执行、备份与验证
parent: Folio 文档迁移
grand_parent: 建站笔记
nav_order: 1
---

# Folio 文档迁移：执行与验证

## 授权与备份

用户先授权开始文档库迁移，再确认新仓库名称 folio。迁移以独立预览进行，原站继续运行；没有更改原站 DNS、根站部署或聊天工作区。

迁移基线为 WendellManual 提交 e925feaa124b4e625d45da157f0755c28e8bcda1。迁移前 Git bundle：C:/03_Github/.work/manual-before-folio-20261118.bundle。

## 内容范围

源站 92 个 docs Markdown 页面，加上旧首页、CHANGELOG、MIGRATION，共 95 个页面；同时保留 33 个静态资源。

- 48 个常规页面继续以 Markdown 编辑，Liquid 资源路径已展开，Kramdown 的纯样式标记移除。
- 47 个旧主题复杂页面采用原渲染 HTML 存档，保留代码与演示。页面明确标注旧 Jekyll / Just the Docs 的适用范围。
- 原始 Markdown、源提交及 SHA-256 记录在 Folio 的 migration/source/ 和 migration/manifest.json。
- 新首页、从这里开始、迁移说明以及本组执行记录属于新站新增文档，不计入上述 95 页基线。

常规迁移文档在 src/content/docs/ 中维护。原始快照仅用于溯源，日常构建不依赖 Ruby。历史 HTML 在构建时解析成语法树，统一修正链接、保留原章节锚点，参与原生目录及搜索。

## 架构与目录

采用 Astro + Starlight，静态输出到 GitHub Pages 的 /folio/ 子路径。框架与依赖使用锁定版本，由 package-lock.json 固定安装结果。

导航分为博客指南、建站笔记、资料导航、其他笔记、旧主题参考。侧边栏读取实际 Markdown 的标题和顺序，目录概览与子页均有入口；新文档放入对应目录即可纳入导航。

迁移兼容表接收旧的相对文档路径。原 WendellManual 的在线绝对地址继续访问旧站；本轮没有把旧站全量重定向到新站。新站内部的旧路径引用会转换到 Folio 页面。

保留 Starlight 原生搜索、页内目录、主题切换和导航。旧演示页面的代码复制与 Mermaid 流程图用小范围增强补齐；Mermaid 仅在含图表的页面加载。

## 验证与发布

本地构建成功，95 个迁移页面及 33 个原静态资源的快照校验通过。生成 198 个 HTML 文件，站内链接与锚点检查无缺失；搜索索引文件存在。原页面规范化正文及代码块另做对照，报告位于 migration/validation/content-fidelity.json。

已修复两项迁移管线问题：旧 Rouge 代码高亮把代码拆成多个 span，新高亮器读取时可能只取首段；现统一还原完整代码文本后再交给新管线。另关闭根据代码首行注释自动提取文件名，保留原代码示例完整内容。

中文搜索的清空、结果数量、无结果与加载更多提示，以及章节锚点标签均提供中文翻译。手机导航分组和搜索结果跳转已验证。迁移时合并与页面标题完全相同的正文标题，旧章节锚点仍保留。

发布由 Folio 仓库的 Validate and deploy Folio 工作流执行。工作流先验证代码与生成内容，再上传 Pages；PR 不发布。首次上线结果在本记录后续补录。


### 浏览器验收

Chrome 桌面模拟手机 390 像素宽度，完成目录展开、中文搜索、选中结果跳转并关闭搜索弹层、页内目录跳到 Mermaid 图表并收起，以及深色主题下 SVG 图表渲染。写作台说明的原生复制按钮显示“复制成功”。这些是桌面浏览器模拟结果，不代替实体手机验收。

初次迁移的 95 页规范化正文对照达到 100%，未发现缺失代码块。此指标排除自动目录、辅助朗读标签和纯样式标记；原始 Markdown 与资源另以 SHA-256 检查。保留完整快照，后续仍可按页追查。


## 首次发布结果

源码提交 [adb66e2](https://github.com/yen-chiu/folio/commit/adb66e297a7475bac16b3d0a1b3627c1823b87bd) 已推送并核对远端 SHA。[Validate and deploy Folio 44332334673](https://github.com/yen-chiu/folio/actions/runs/44332334673) 已 completed/success，构建和 Pages 部署通过。Astro 检查为 0 errors、0 warnings、0 hints。

- 线上预览：[Folio](https://yenchiu.com/folio/)。已在 Chrome 中实际打开，首页名称、入口与分类正常。
- 线上 101 个文档页面、94 个旧相对路径兼容页及 45 个资源目标均通过 HTTPS 返回 200；兼容页的目标保留 /folio/。
- 原始静态资源的字节校验通过。两个 HTML 原型由既有 Cloudflare 设置加入安全脚本和统计脚本；仅移除这些已知注入后比较原 HTML 的结构、属性、正文、样式及原有脚本，确认内容一致，不据此修改原文件或域名配置。
- 本地最终生成 198 个 HTML，13907 条站内引用与锚点检查无缺失。95 页规范化正文完整性为 100%，代码块无缺失。
- 源快照 SHA-256 以迁移时 Windows 工作区的原件字节为准；通过 .gitattributes 保留快照及原资源的换行，Linux CI 同样完成原件校验。

线上验证报告：C:/03_Github/.work/folio-online-verification.json。截图：.work/folio-live.jpg、.work/folio-home-mobile.jpg、.work/folio-mobile-diagram.jpg。

旧 WendellManual 继续服务；本次完成并行迁移预览，没有执行旧入口切换。
