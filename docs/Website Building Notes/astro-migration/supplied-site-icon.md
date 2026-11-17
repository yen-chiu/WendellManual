---
title: 用户指定的透明人物网站图标
parent: Astro 迁移记录
grand_parent: 建站笔记
nav_order: 14
---

# 2026-11-18 用户指定的透明人物网站图标

## 用户决定

使用桌面“博客网站头像”目录中去背景的透明 PNG，替换之前的月亮星星网站图标。延续此前区分：这是浏览器标签/收藏/网站身份图标，不是首页和关于页的人物照片。插画原始作者/来源未提供，记录待补；本次不做创作归属或重新许可声明。

## 文件与备份

选用文件：૮_˶ᵔ_ᵕ_ᵔ˶_ა_Follow_me!-up2x-no-denoise(去背景).png。桌面原图及用户提供的 ICO 均保留。公开源文件为 public/favicon/wendell-avatar-source.png，1322×1322、RGBA。

原图 SHA256：4C3A2A70B3DF075B98112ACF2CDACAA2AB8D8A40272E7D474850146B031CA193。原图 1,747,684 像素中 1,165,805 像素完全透明，另有半透明边缘；不是纯色背景模拟透明。

修改前额外备份 .work/iris-before-custom-icon-20261118.bundle；本批之前另有首页修改前的站点与文档库 bundle。旧月牙素材和来源声明作为历史资产保留，新的页面引用改为新文件名。

## 实现

- 透明 PNG：16/32/48/64/128/180/192/256/512。
- ICO：16/32/48/64/128/256 六个 PNG 帧，保留 alpha。
- 浏览器 icon、apple-touch-icon、manifest、外链头像说明/默认回退同步更新；manifest 使用 wendell-avatar.webmanifest，避免沿用旧图标 URL。
- 默认分享卡片使用同一透明插画与 Wendell 标题，1200×630；图标本身保留透明，分享卡片为固定浅色背景。
- scripts/generate-branding.mjs 改为从提供的 PNG 做确定性的尺寸/格式导出，没有重绘插画。
- 公开站点说明更新当前图标，保留历史月牙图标许可署名。首页与关于页继续使用原 avatar.png。

## 验收

192px 输出有 4 通道，四角 alpha 均为 0。浅/深背景预览人工查看通过，分享卡片没有遮挡标题。图像验证图 .work/iris-custom-icon-check.png；本地构建及线上图标路径、内容一致性验证完成后追加。


ICO 实际解码验证 16/32/48/64/128/256 六帧尺寸正确且均有 alpha；网页 head 中 ICO、32/192 PNG、Apple 180、新 manifest 和 og:image 都已指向新资源，链接卡片的 192px 图片实际加载成功。原始浏览器标签栏中绘制出的微型图标未直接检查。验收报告：[supplied-icon-qa.json](https://github.com/yen-chiu/yen-chiu-astro/blob/main/migration/validation/supplied-icon-qa.json)。

同批网站提交：[e8be150](https://github.com/yen-chiu/yen-chiu-astro/commit/e8be15016bd8918d0493ff67409ddfb4ffc97de9)。构建 136 页、0 类型错误/0 警告，本地资源审计通过。


## Pages 发布与线上核对

[Pages 任务 44139762994](https://github.com/yen-chiu/yen-chiu-astro/actions/runs/44139762994) 的 build/deploy 均 success，部署提交为 e8be15016bd8918d0493ff67409ddfb4ffc97de9。

[公开预览](https://yenchiu.com/yen-chiu-astro/?v=e8be150) 已更新。线上首页、博客、归档、链接、关于共 5 页均 HTTP 200，新组件和新 favicon 元数据存在，本地关键内联脚本在页面中一致。CDN 额外注入的 Cloudflare JS 检测脚本单独识别，没有误当作应用脚本差异。

21 项本地关联资源均 HTTP 200；20 项编译脚本/样式或二进制图标/分享图与本地验收版本字节一致。manifest 因 Windows CRLF 与 Linux LF 不同而字节不等，解析后的 JSON 完全相同。在线验证报告保存于 .work/iris-home-online.json；交互验收来自本地生产构建，线上按页面、脚本和资源一致性验证。

本地预览进程已关闭，临时浏览器视口已撤销。原站根目录、域名配置与聊天工作区保持原部署。
