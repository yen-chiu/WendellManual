---
title: 手机菜单、图谱与页脚修复
parent: Astro 迁移记录
grand_parent: 建站笔记
nav_order: 9
---

# 2026-11-17 手机菜单、图谱与页脚修复

用户通过手机截图提出：导航点选后收起；图谱去掉粗黑框，修复缩放按钮；恢复建站时间与刷新按钮。工作台暂不迁移。

## 本轮变更

- 持久化页头在点击导航、打开搜索/图谱等入口、页面切换及 Escape 时关闭菜单，同步 aria-expanded，避免 Astro 页面切换后仍覆盖正文。
- 粗黑框来自 SVG 可聚焦节点的浏览器默认 outline。改为节点本身的细紫色圆环；保留键盘焦点提示与选中状态 aria-pressed。节点标题文字也接收点击，详情面板可正常更新。
- 放大、缩小、重置直接更新 D3 缩放，停止旧动画；按钮最小 44×44 CSS 像素，强化触控和层级，手机工具栏固定在图谱底部。
- 页脚恢复每秒更新的建站运行时间，起点沿用原站 2023-05-20 13:14:00（UTC+8）。组件离开页面时清理计时器与监听器。
- 页脚“刷新页面”先以 cache=reload 请求当前页面，再携带 v 时间戳重载。失败时恢复按钮并提示重试；不会清除 localStorage、聊天内容、主题设置或同源服务工作线程。原 Jekyll 清除整站缓存/服务工作线程的逻辑没有照搬，避免影响同域原站。

## 验收

Chrome 扩展连接下测试最终生产构建，实测 CSS 390×844：

1. 菜单展开时 aria-expanded=true；点击“标签”后为 false，并进入标签页。
2. 缩放按钮均为 44×44，中心命中测试没有遮挡；放大得到 scale(1.3)，缩小回到 scale(1)，重置得到单位变换。
3. 点击 MyBlog 标题后详情为 MyBlog，节点 aria-pressed=true；outline=none，圆环为紫色 rgb(127,81,184)。
4. 页脚运行时间正常更新；点击刷新后 URL 出现 v 时间戳，重载完成按钮重新可用。
5. 标签页无横向溢出；测试后成功撤销浏览器临时视口设置。

构建 136 页，类型检查 0 错误/0 警告；路径、wikilink/backlink、手机标签策略回归均通过。静态审计本地缺失链接为 0；迁移源文件校验 17 篇一致、57 个人页资源引用无缺失。

浏览器证据见迁移库 [mobile-controls-qa.json](https://github.com/yen-chiu/yen-chiu-astro/blob/main/migration/validation/mobile-controls-qa.json)。本地截图 C:/03_Github/.work/iris-menu-graph-final.jpg。手机视口模拟不能替代用户真机观察。

## 备份与发布

迁移库改动前备份 C:/03_Github/.work/iris-before-menu-footer-20261117.bundle；文档库改动前备份 manual-before-mobile-controls-20261117.bundle。保留全部 Git 历史，不改原站 DNS。

网站提交 [d797dbc](https://github.com/yen-chiu/yen-chiu-astro/commit/d797dbc9ce32b107d28bb0b60e0b39a9a9528c45)，[Pages 部署任务](https://github.com/yen-chiu/yen-chiu-astro/actions/runs/44035776731) 已 completed/success。

线上 https://yenchiu.com/yen-chiu-astro/?v=d797dbc 返回 HTTP 200，包含新版页脚与菜单属性；页面引用的 11 份 JS/CSS 全部返回 200，正文与本地验收构建一致。带版本参数可避开旧 HTML 缓存。

发布后的 Chrome 复查发生调用超时，随后提示该标签 debugger 未附着；未将线上交互标为通过。本地最终构建交互结果与线上静态文件一致性分别记录。线上复查尝试设置临时视口后连接中断，清理接口也报告未附着，无法确认第二阶段视口是否恢复；第一阶段本地验收的视口恢复已成功。

## 留待后续

工作台按用户指示延期。Giscus 正文、嵌套预览和全部第三方媒体的剩余验收仍保留，不纳入本次完成范围。
