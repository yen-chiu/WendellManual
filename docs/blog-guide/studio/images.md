---
title: 图片与附件
parent: 写作台
nav_order: 3
---

# 图片与附件

## 插图片

三种都行：把图片拖进编辑区、直接粘贴、或者点工具栏的「图片」按钮选文件。

传完编辑器会自动插一行：

```markdown
![](/assets/img/uploads/2026/11/1000465702-1793679255623.jpg)
```

图片按 `assets/img/uploads/年/月/` 归档，文件名带时间戳，不会重名覆盖。

## 会自动压缩

上传前在浏览器里先压一遍：最长边缩到 1600px，PNG 保留透明，其余转 JPEG。

手机直出的照片常常三五兆，压完一般两三百 K。所以不用提前自己压。

## 大文件别塞仓库

视频、PDF 这类东西不适合放进 git：仓库会越来越胖，而且删了文件也还在历史里。

- **视频**：放 Cloudflare R2，地址 `media.yenchiu.com`，文章里直接写完整 URL
- **要给人下载的文件**：同样走 R2，别放 `assets/`

视频写法：

```html
<figure class="media-embed">
  <video controls preload="metadata" playsinline>
    <source src="https://media.yenchiu.com/videos/xxx.mp4" type="video/mp4">
  </video>
  <figcaption>一句话说明</figcaption>
</figure>
```
