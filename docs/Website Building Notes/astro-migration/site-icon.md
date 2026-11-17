---
title: 透明月牙网站图标与分享缩略图
parent: Astro 迁移记录
grand_parent: 建站笔记
nav_order: 11
---

# 2026-11-17 透明月牙网站图标与分享缩略图

## 用户选择与素材来源

用户要求替换模板作者的网站头像缩略图，并强调不更换首页人物头像；看过网上图片后选择 4 号“月牙与星星”，希望使用镂空透明样式。

素材为 [Moon Stars](https://www.svgrepo.com/svg/426321/moon-stars)，原作者 Nishanth kunder。来源页标注 CC Attribution（CC BY），没有明确版本号；保留其提供的[授权说明](https://www.svgrepo.com/page/licensing/)。已在公开站点说明、public/favicon/NOTICE.md 和 SVG 元数据中注明作者、链接及修改内容。

## 实现

- 保留月牙与三颗星的镂空几何，使用紫色描边、圆角、适当加粗及留白；SVG/PNG 没有色块底板。
- SVG 用 prefers-color-scheme 提供深色浏览器/系统偏好的浅紫线条。这跟随浏览器媒体偏好，不依赖页面里手动切换的主题。
- 提供 16/32/48 PNG 与 ICO 兼容版本，以及 Apple 180、Android 192/512 PNG 和相对路径 manifest。没有添加服务工作线程或自动安装行为。
- 默认分享图改为同款月牙、Wendell 名称和站点标语，尺寸 1200×630，避免继续显示模板作者的图案。文章自定义分享封面仍由文章元数据决定。
- BaseHead 从配置读取图标，补上项目 base 路径。默认 Open Graph/Twitter 图片此前解析到根域 /images，现修正为 /yen-chiu-astro/images/。
- 友情链接的本站信息与缺图后备图改为新标识。未修改首页、页头和关于页的人物头像。
- 生成源为 public/favicon/moon-stars.svg，导出脚本 scripts/generate-branding.mjs；只使用现有 sharp 依赖，PNG、ICO、分享图均可重生成。
- 修正站点说明中“尚未公开发布”的过期描述，增加图标署名。

## 验收与限制

构建 136 页，类型检查 0 错误/0 警告；静态链接审计无缺失，路径回归通过。17 篇原文源校验和 57 项个人页资源引用检查通过。

101 个正常页面均引用新图标，无旧 GIF 或错误的根路径默认分享图；34 个即时跳转页保持原行为。所有尺寸 PNG 为 RGBA、四角透明；ICO 包含 16/32/48；分享图为 1200×630。

Chrome 验收预览中六个图片样本全部加载，无横向溢出。实际首页的 SVG/PNG/ICO、Apple 图标、manifest 与 og:image 地址均正确，页头人物头像仍为原头像。截图 C:/03_Github/.work/iris-favicon-preview.jpg；结构化记录见 [favicon-qa.json](https://github.com/yen-chiu/yen-chiu-astro/blob/main/migration/validation/favicon-qa.json)。

深色预览使用深色 PNG，未修改操作系统主题；iOS/Android 真机桌面快捷方式未测试，系统可能自行添加底色。现有书签图标缓存也可能稍后才更新。新图标采用新文件名，页脚刷新可重新获取新 HTML；不清除主题、聊天数据或整站本地存储。

## 备份与发布

改动前 Git bundle：C:/03_Github/.work/iris-before-favicon-20261117.bundle、manual-before-favicon-20261117.bundle。人物头像 SHA256 仍为 F41FDA39BDA3150B283E6DDE5B899DB5855E3A168D1127E54061DC578B723D54。

网站提交 [44f0352](https://github.com/yen-chiu/yen-chiu-astro/commit/44f03525a933984f87e2b5a263a9ffe75a46efd4) 已推送 main；[Pages 任务 44063103859](https://github.com/yen-chiu/yen-chiu-astro/actions/runs/44063103859) 已 completed/success。推送曾返回网络 EOF，后续远端提交核对确认已成功，没有强制推送。

[新版预览站](https://yenchiu.com/yen-chiu-astro/?v=44f0352) 返回 HTTP 200，引用新 SVG 与分享图，不再引用原 GIF。在线读取的 SVG、ICO、32/180/192/512 PNG、manifest 和分享图共 8 份文件，SHA256 均与本地验收构建一致；公开站点说明已显示 Nishanth kunder 的署名。

原站 DNS 与根站部署未切换。浏览器界面/小图效果来自本地生产构建验收，线上以资源一致性和引用确认；不将系统桌面安装或旧书签缓存更新标为实测通过。
