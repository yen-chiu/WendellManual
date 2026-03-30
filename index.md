---
title: 首页
layout: home
nav_order: 1
description: Wendell's Manual
permalink: /
---

# 专注于高质量文档编写
{: .fs-9 }

-... ..- - -....- .... . -....- .- .-.. .-. . .- -.. -.-- -....- .... .- ... -....- .... .. ... -....- .-. --- ... .
{: .fs-6 .fw-300 }

[立即开始](#开始){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
[在 GitHub 上查看](https://github.com/yen-chiu/WendellManual){: .btn .fs-5 .mb-4 .mb-md-0 }

---

{: .warning }
> **提示:** 请参阅 [更新日志]({% link CHANGELOG.md %}) 了解最新发布、功能更新和错误修复。

通过使用 [Markdown]、[Liquid] 模板语言和 HTML 编写源文件，构建的专业级文档平台。[^1]  

---

## 开始
### 关于项目

WendellManual 是一份指南，由 &copy; 2023-{{ "now" | date: "%Y" }} 创建，旨在帮助用户快速上手。

#### 特别感谢 Just the Docs 的贡献者
<ul class="list-style-none">
{% for contributor in site.github.contributors %}
  <li class="d-inline-block mr-1">
    <a href="{{ contributor.html_url }}">
      <img src="{{ contributor.avatar_url }}" width="32" height="32" alt="{{ contributor.login }}">
    </a>
  </li>
{% endfor %}
</ul>

---

[^1]: 本页面的 [源文件](https://github.com/yen-chiu/WendellManual/blob/master/index.md) 使用了 Markdown、Liquid 和 HTML。

---

### 相关链接
- [Jekyll 官方文档][Jekyll]
- [Markdown 指南][Markdown]
- [Liquid 模板语言][Liquid]
- [Front Matter 说明][Front matter]
- [Jekyll 配置指南][Jekyll configuration]
- [GitHub Pages 教程][GitHub Pages]
- [GitHub Actions 工作流][GitHub Pages / Actions workflow]

[Jekyll]: https://jekyllrb.com
[Markdown]: https://daringfireball.net/projects/markdown/
[Liquid]: https://github.com/Shopify/liquid/wiki
[Front matter]: https://jekyllrb.com/docs/front-matter/
[Jekyll configuration]: https://jekyllrb.com/docs/configuration/
[GitHub Pages]: https://pages.github.com/
[GitHub Pages / Actions workflow]: https://github.blog/changelog/2022-07-27-github-pages-custom-github-actions-workflows-beta/