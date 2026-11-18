---
title: 文章卡片底栏与背景设置分离
parent: Astro 迁移记录
grand_parent: 建站笔记
nav_order: 15
---

# 2026-11-18 文章卡片底栏与背景设置分离

## 用户反馈与原因

手机单列卡片“阅读全文”不整齐，长短标签导致链接浮在不同位置。原底栏用 flex 同时排列可换行标签和阅读链接；480px 以下又把阅读链接设为 width:100%，却没有把两部分分成独立行，导致标签占位挤压和对齐不稳定。

全屏首页建立后，原背景“横幅/全屏/叠加/隐藏”语义混在一起：三个模式的首页都是相同全屏人物入口，差别主要出现在内页的固定背景，而“隐藏”还顺带隐藏首页插画。用户因此难以判断选项是否有用。

## 修改

- 标签独占一行，阅读时间与阅读全文进入独立操作栏；左时间、右按钮，按钮高至少 44px。使用主题色的轻背景/细边框，箭头单独对齐。
- 窄屏网格将操作栏改成上下排列，按钮等宽铺满卡片内部；单列继续稳定右对齐。
- 外观面板明确分成“内页背景：纯色/壁纸/柔化”和“首页封面：显示插画/显示标题/轻微波浪”，附作用范围说明。
- 内页壁纸只对 body[data-page=content] 生效；首页保留全屏入口，插画由独立 cover 偏好控制。
- 旧存储键不变；旧 banner/fullscreen/overlay 保留并分别对应新标签，旧 hidden 迁移为内页纯色+首页插画关闭。已选色相、深浅模式和文章布局保留，不删除聊天等其他存储。
- 波浪选项增加轻微边缘移动，仅在开启且系统不要求减少动态效果时运行。

## 备份与验证

修改前：.work/iris-before-card-settings-20261118.bundle、manual-before-card-settings-20261118.bundle。新增偏好迁移与独立开关持久化测试，结合原 361 色相对比度、损坏存储、刷新/路由主题测试。

结果见下方验收记录。


## 本地生产构建验收

- 390px 单列 8 个按钮右边缘均为约 339.60px，按钮高 44px，与左侧阅读时间的垂直中心差为 0，所有标签均位于操作栏上方。
- 320px 单列 8 个按钮右边缘均为约 269.60px；320px 网格两列各约 131.14px，按钮宽约 102.61px、高 44px，同一行按钮底边一致；均无横向溢出。
- 1280px 单列 8 个按钮右边缘均为约 1127.67px，无溢出。第七个阅读入口实际进入 QQ/NapCat 文章后可返回。
- 内页纯色：背景图隐藏；壁纸：清晰图、opacity .24；柔化：opacity .13、blur 3px。首页在这些模式下均隐藏内页壁纸元素，封面图由独立开关控制。
- 首页插画关闭+内页柔化，刷新后保留；再打开首页插画不改变内页柔化。开启波浪后观察到 cover-edge-drift 动画名。恢复默认、临时视口撤销完成。
- 完整构建 136 页，Astro 类型诊断 0 errors/0 warnings/2 个既有 hints；静态资源审计无本地缺失。原有大 chunk 提示保留，不作为本次新增问题。

[验收 JSON](https://github.com/yen-chiu/yen-chiu-astro/blob/main/migration/validation/cards-background-qa.json)。截图：.work/iris-card-actions-mobile.jpg、iris-background-settings-mobile.jpg。Chrome 视口模拟不等于真机验收；系统减少动效只作条件代码检查。


## 发布

提交 [1702920](https://github.com/yen-chiu/yen-chiu-astro/commit/170292080849f417dbecce07eb20765859b755f9) 已推送 main，远端 SHA 已核对。[Pages 44236843129](https://github.com/yen-chiu/yen-chiu-astro/actions/runs/44236843129) build/deploy success。

线上 5 页和 21 项关联资源均 HTTP 200；新的内页背景/首页封面选项、页面组件及应用内联脚本已核对。编译资源与二进制字节一致；manifest 的 CRLF/LF 差异经 JSON 一致性验证。报告 .work/iris-cards-online.json。后续用户继续要求整站信息密度优化，另记下一篇。
