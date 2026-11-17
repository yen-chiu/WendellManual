---
title: 玻璃导航、外观面板与手机交互修复
parent: Astro 迁移记录
grand_parent: 建站笔记
nav_order: 12
---

# 2026-11-17 玻璃导航、外观面板与手机交互修复

## 用户需求

参考 Fuwari 的首页封面与 Mizuki 的半透明局部下拉导航、图标分组和丰富外观设置；修复点选标签后弹窗仍打开、手机标签字号过大，以及文件管理器选择后需要手动滚到内容/预览的问题。用户明确建议先参考博客 GitHub 源码，再做适配。

## 源码参考与实现边界

通过 GitHub 连接读取实际默认分支和文件，确认 Mizuki 默认分支是 master。参考版本及完整链接、许可副本保存在迁移库 migration/theme-references/。

- [Fuwari NavMenuPanel](https://github.com/saicaca/fuwari/blob/6d39b0dec41282e7852e23e032998a5789abee28/src/components/widget/NavMenuPanel.astro)、[DisplaySettings](https://github.com/saicaca/fuwari/blob/6d39b0dec41282e7852e23e032998a5789abee28/src/components/widget/DisplaySettings.svelte)、[setting-utils](https://github.com/saicaca/fuwari/blob/6d39b0dec41282e7852e23e032998a5789abee28/src/utils/setting-utils.ts)：浮动面板、色相滑杆、偏好持久化、恢复默认和首屏应用主题。
- [Mizuki NavMenuPanel](https://github.com/LyraVoid/Mizuki/blob/dbdd3eb039e5e92f9375f5f8f79c3589cdf6ca16/src/components/organisms/navigation/NavMenuPanel.astro)、[SettingsPanel](https://github.com/LyraVoid/Mizuki/blob/dbdd3eb039e5e92f9375f5f8f79c3589cdf6ca16/src/components/features/settings/SettingsPanel.svelte)、[backgroundWallpaper](https://github.com/LyraVoid/Mizuki/blob/dbdd3eb039e5e92f9375f5f8f79c3589cdf6ca16/src/config/backgroundWallpaper.ts)：图标分组、单组展开、背景模式、封面及文章布局设置。

按现有 Astro ClientRouter、持久页头和原生 dialog 重写集成，没有引入 Svelte 或另一套主题运行时，也未复制演示站人物壁纸。增加页面切换清理、点外部/Escape 关闭、原生 details、存储校验及动态颜色对比度。公开站点说明补充来源署名。

## 实际变更

1. 标签云点击具体标签后立即关闭并解除滚动锁；页面切换同样关闭。每次初始化中止旧监听器，适配持久化弹窗。手机标签约 13.6–16.8px，保留 44px 点击区域，计数不随标签缩得过小。
2. 手机文件管理器单击目录进入并定位内容区，单击文章选择并定位预览；仅滚动弹窗内部，背后页面不动。尊重减少动效偏好。预览跳转文章时关闭弹窗。补充始终可见的上一级按钮，桌面保留原来的选择/双击进入和键盘浏览。
3. 导航改为阅读、探索、念真、关于四组，左侧图标、右侧箭头。手机面板靠右、宽 280px，使用半透明背景和 18px 模糊，超高内容内部滚动。一个分组展开时收起其他组；点链接、弹窗入口、空白处、Escape 或切换页面均收起。
4. 外观面板提供五个颜色预设、0–360 色相、系统/浅色/深色、横幅/全屏/叠加/隐藏、列表/网格、封面标题和波浪开关，以及恢复默认。保存到 wendell-appearance-v1，兼容现有 theme 键，不删除聊天等其他存储。
5. 首屏即应用偏好，避免刷新后先显示默认颜色。调色会为浅色主色计算亮度，覆盖黄色/绿色等容易对比不足的色相。修正系统主题变化不应覆盖手动浅/深色选择的问题。
6. 首页增加本地原创矢量星夜封面，与月牙图标协调；后续可以替换为用户选定的插画。手机默认 264px 高，首屏能看到文章。提供两种文章布局，沿用实际文章内容；人物头像、关于页个人资料、评论绑定、原文地址与页脚功能保留。

## 验收过程

Chrome 390×844 已确认：选 blogging 后进入 /tags/blogging/，标签弹窗 open=false，滚动锁=false；前四个标签字号为 16.8、16.52、16.21、15.86px。

文件管理器进入“技术”后内部 scrollTop=127，内容区距滚动容器顶部约 8px；选择 R2 文章后内部 scrollTop=606，预览标题与“阅读全文”按钮可见。两次背后页面 scrollY 都为 0，跳转文章后弹窗关闭。

菜单实际宽 280px、背景透明度 .88、blur(18px)，分组互斥展开。验收发现焦点离开瞬间会过早关闭菜单，已改为监听实际焦点进入外部并复测通过。

外观实测：青色 165°、键盘滑杆到 166°、深色、叠加、网格、隐藏标题和波浪选项均生效，刷新后仍保留；恢复默认后可继续使用。中文搜索“念真”返回匹配正文，搜索覆盖视口。菜单跳转关于页正常。

新增 appearance.test.mjs 覆盖损坏存储、无效值、0 度色相、持久化、恢复默认、系统/固定主题、页面切换以及全部 361 个色相的对比度；路径、图谱文字、wikilink/backlink 与原文迁移回归通过。发布结果见下文。

最终构建 136 页，0 类型错误/0 警告，静态审计无本地缺失资源。最终构建还复测了上一级按钮（技术→根目录，根目录禁用）、重复标签选择、320px 手机导航与 Escape、768/1280px 布局；四种视口无横向溢出。手机网格实测为两列各约 166.3px。桌面分组下拉宽 208px，手机按钮按断点隐藏/显示正常。测试结束恢复默认外观并成功撤销临时视口。

详细证据：[appearance-qa.json](https://github.com/yen-chiu/yen-chiu-astro/blob/main/migration/validation/appearance-qa.json)。所有壁纸模式组合、系统减少动效及真机仍未穷尽测试。

## 备份与限制

修改前 Git bundle：iris-before-appearance-20261117.bundle、manual-before-appearance-20261117.bundle，均在 C:/03_Github/.work/。保留 Git 历史。

截图：iris-glass-menu.jpg、iris-appearance-panel.jpg、iris-directory-scroll.jpg，均在 .work/。手机测试使用 Chrome 视口模拟，不能替代真机验收；Giscus 历史评论正文和全部第三方媒体仍保留原待验项。原站 DNS、根站部署和聊天运行时未迁移。

## 发布结果

网站提交 [2345b3b](https://github.com/yen-chiu/yen-chiu-astro/commit/2345b3b50f4166e3a147589c191f4bde0e6b43a6) 已推送 main，[Pages 任务 44113059687](https://github.com/yen-chiu/yen-chiu-astro/actions/runs/44113059687) completed/success。

[新版预览站](https://yenchiu.com/yen-chiu-astro/?v=2345b3b) HTTP 200，包含新导航、紧凑标签字号、星夜封面及偏好初始化代码。页面引用的 12 个本地 JS/CSS/SVG 均 HTTP 200、正文与本地验收构建一致；另核对了 5 段关键内联脚本（外观初始化、面板、页头、标签云、首页布局），均一致。

线上以部署、内容及脚本一致性验证；交互实测来自本地生产构建。使用版本参数或页脚刷新获取新 HTML，避免旧持久页头继续运行。没有清除用户本地数据。
