---
title: 文档站红叉与站内链接修复
parent: Astro 迁移记录
grand_parent: 建站笔记
nav_order: 19
---

# 2026-11-18 文档站红叉与站内链接修复

## 现象与根因

用户在GitHub手机端看到 master 分支与多次提交后的红叉。核对 d584945 的运行：Pages [44250941589](https://github.com/yen-chiu/WendellManual/actions/runs/44250941589) 已部署成功；CI [44250941612](https://github.com/yen-chiu/WendellManual/actions/runs/44250941612) 中Jekyll构建与CSS/JS检查成功，只有 Validate HTML 的 html-proofer 失败。因此此前文件已推送、文档已部署，但提交的综合状态为失败。

实际日志检查95个HTML文件、852个内部链接和93个文件的锚点，报告5个路径错误：chrome-qa中的mobile-controls，以及issues中的mobile-controls、appearance-navigation、home-archive-links、supplied-site-icon。

文章采用pretty permalink，chrome-qa.md输出为chrome-qa/index.html。此时 ./mobile-controls 会指向 chrome-qa/mobile-controls，而不是同级记录。全部改为 ../对应记录/，明确返回父目录再进入目标页面。索引页自身位于astro-migration/index.html，其现有 ./链接解析正确，保持原样。

## 修复范围与验证

只修改上述5处路径，保留工作记录正文和全部CI校验规则，不忽略站内链接或关闭HTML检查。新增本记录并纳入分类索引。

本地使用锁定依赖完成Jekyll构建。Windows html-proofer 的本地启动受libcurl动态依赖影响，不能把本地启动错误视作GitHub原始故障；GitHub原始日志明确为5个无效站内链接。最终以GitHub上的完整CI和Pages结果验收，并补充生成HTML链接目标检查与线上访问。

变更前备份：C:/03_Github/.work/manual-before-html-validation-20261118.bundle。详细失败日志只保存在本地 .work/manual-ci-job.log，不提交整个运行日志。

## 发布结果

修复提交 [2afdee3](https://github.com/yen-chiu/WendellManual/commit/2afdee3d7f05e1c009b4e27288bf2651a4465ecd) 已推送并核对远端SHA。

- [CI 44276712399](https://github.com/yen-chiu/WendellManual/actions/runs/44276712399)：completed/success，Jekyll gem构建、CSS/JS检查及HTML校验全部通过。
- [Pages 44276712424](https://github.com/yen-chiu/WendellManual/actions/runs/44276712424)：completed/success。
- 本地生成96个HTML页面，独立检查10760条站内资源/链接及锚点引用，无缺失。报告 .work/manual-html-local-check.json；Windows html-proofer 本身未成功启动，真正的html-proofer验收由上述Linux CI完成。
- 线上两篇记录的5个修正链接均已输出为正确相对地址，4个目标记录页面全部HTTP200。报告 .work/manual-ci-links-online.json。

历史失败提交仍会保留红叉，这是当时的检查结果；新的修复提交已全绿。此前只核对Pages成功，遗漏了独立HTML校验，后续发布应同时检查部署和CI整体状态。
