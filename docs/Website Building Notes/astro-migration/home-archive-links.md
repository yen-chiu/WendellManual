---
title: 全屏首页、文章日历与链接页
parent: Astro 迁移记录
grand_parent: 建站笔记
nav_order: 13
---

# 2026-11-18 全屏首页、文章日历与链接页

## 需求与决策

首页改为全屏人物入口，下滑收起为横幅，文章及列表/网格功能集中到博客。右下角显示可点击的实际封面来源。头像由用户选择方案 1：鼠标轻微倾斜、点按回弹；首页名字增加故障动画。归档参考 Mizuki 加入日历、数据及手动播放的声音；链接页参考 Iris。

## 参考源码

- [Iris 首页](https://github.com/LemonAdorable/astro-theme-iris/blob/56f5cfee0d39c08649a714da68a53200c7600223/src/pages/index.astro)：全屏人物、向下入口、头像与欢迎区。
- [Iris 链接页](https://github.com/LemonAdorable/astro-theme-iris/blob/56f5cfee0d39c08649a714da68a53200c7600223/src/pages/links/index.astro)：分组卡片、站点统计、本站资料和申请说明。
- [Mizuki 归档](https://github.com/LyraVoid/Mizuki/blob/dbdd3eb039e5e92f9375f5f8f79c3589cdf6ca16/src/pages/archive.astro)、[日历工具](https://github.com/LyraVoid/Mizuki/blob/dbdd3eb039e5e92f9375f5f8f79c3589cdf6ca16/src/components/widgets/calendar/utils/calendarUtils.ts)：归档侧栏、周一开头的日历与发布日期选择。

参考记录与许可继续保留在迁移仓库 migration/theme-references/。没有增加另一套主题框架。

## 页面与交互

- 首页不再渲染文章；全屏封面随正常滚动逐渐压缩，人物淡出，右下出现矢量封面源文件链接。向下探索按钮和展开封面入口均保留。没有沿用参考源码中向上滚动即强制跳回顶部的行为。
- 首页和关于页复用 InteractiveAvatar；鼠标倾斜最多约 6 度，手机点按与键盘激活回弹。首页名字可悬停/点按触发短暂故障效果。减少动态效果偏好关闭倾斜、回弹、名字故障与背景动画。
- 博客原有分页、标签、分类与阅读时间保留；列表/网格按钮迁移至文章工具栏，与原外观面板共享持久设置。
- 归档加入文章日历：默认最近发布月，前后月、原生月份选择、今天按钮、发布日标记、点选日期、再次点选取消及本月全部。日期按上海时区的发布日期统计，时间线统一采用发布日期，避免更新日期改变文章归档位置。
- 数据展示真实文章、标签、活跃月和文字量估算，明确计数口径，没有虚构访问量。
- 声音先提供本地合成雨声：手动播放/暂停/音量，离开页面或切到后台停止，不请求第三方音乐或复制有版权的演示音轨。正式歌曲/歌单可后续由用户指定。
- 链接页分为我的角落、开源与阅读、友好邻居，支持搜索、键盘切换标签、空结果和复制本站资料。使用原博客公开资料/外链与明确标注的主题参考，移除模板作者的示例朋友。友好邻居暂为空，没有伪造互链、健康检测或自动申请状态。本站正式域名和预览头像地址明确说明。

## 备份与验收

修改前备份：C:/03_Github/.work/iris-before-home-20261118.bundle、manual-before-home-20261118.bundle。原 Jekyll 根站、DNS 与 chat 运行时保持原部署。

日期测试覆盖上海跨日、闰年二月、周一表头、跨年月份。完整构建、原有路径/主题/图谱/内容链接回归及 17 篇原文校验继续执行。浏览器验收与发布结果在完成后追加。


## 浏览器验收

Chrome 本地生产构建：390×844 首页 cover 初始高 844px，首页文章元素为 0；滚动后人物隐藏、右下封面源文件链接可见。名字按钮激活后观察到 name-shift 动画；头像点按已执行，鼠标跟随与系统减少动态效果仅做源码检查。

归档选中 2026-11-05 显示 3 篇文章；切换 2024-02 显示 29 天，切月与今天按钮可用。播放/暂停雨声时按钮文字和 aria-pressed 正确切换，未校验实际声音输出。桌面 1280px 日历/数据列宽约 547.7/476.3px。手机归档无横向溢出。

链接页搜索 Mizuki 仅显示对应卡片，空搜索结果有提示；ArrowRight 切到下一分组，复制本站信息显示“已复制”。博客网格在 390px 下两列约 166.3px，320px 下两列约 131.3px；刷新和第二页保留网格，第二页 8 篇文章。测试后恢复列表。

详细证据：[home-archive-links-qa.json](https://github.com/yen-chiu/yen-chiu-astro/blob/main/migration/validation/home-archive-links-qa.json)。截图位于 .work/iris-home-collapsed.jpg、iris-calendar-mobile.jpg、iris-archive-desktop.jpg；手机截图仅是视口模拟。首轮自动定位点击先滚动了移动中的按钮，未能确认真实点击处理器；最终改用键盘激活，并将按钮目标明确为紧凑横幅的滚动位置。


最终收起验收：390×844，使用 Enter 激活向下按钮后 scrollY=504，封面高 340px，来源链接可见；展开入口返回顶部。早先自动定位点击会先把随滚动移动的按钮滚到中间，导致点击落空，不能据此认定真实按钮处理器失效。记录了该工具限制；最终使用实际按钮的键盘激活确认目标。


本批最终构建 136 页，0 类型错误/0 警告、2 个既有提示，静态审计无本地缺失。日期/主题/两类路径/内容链接/图谱回归均通过，17 篇原文 SHA 和 57 个念真资源引用保持校验通过。最终桌面收起高约 340.1px，链接页图片均加载；浏览器末轮有 Zotero/扩展消息通道错误，记录为环境噪声，未据此宣称控制台完全无错误。临时视口已撤销。

网站提交：[e8be150](https://github.com/yen-chiu/yen-chiu-astro/commit/e8be15016bd8918d0493ff67409ddfb4ffc97de9)，main 已推送且远端 SHA 一致。


## Pages 发布与线上核对

[Pages 任务 44139762994](https://github.com/yen-chiu/yen-chiu-astro/actions/runs/44139762994) 的 build/deploy 均 success，部署提交为 e8be15016bd8918d0493ff67409ddfb4ffc97de9。

[公开预览](https://yenchiu.com/yen-chiu-astro/?v=e8be150) 已更新。线上首页、博客、归档、链接、关于共 5 页均 HTTP 200，新组件和新 favicon 元数据存在，本地关键内联脚本在页面中一致。CDN 额外注入的 Cloudflare JS 检测脚本单独识别，没有误当作应用脚本差异。

21 项本地关联资源均 HTTP 200；20 项编译脚本/样式或二进制图标/分享图与本地验收版本字节一致。manifest 因 Windows CRLF 与 Linux LF 不同而字节不等，解析后的 JSON 完全相同。在线验证报告保存于 .work/iris-home-online.json；交互验收来自本地生产构建，线上按页面、脚本和资源一致性验证。

本地预览进程已关闭，临时浏览器视口已撤销。原站根目录、域名配置与聊天工作区保持原部署。
