<div align="center">

# WendellManual

Wendell 的博客使用说明与文档站。

<p>
  <a href="https://yenchiu.com/WendellManual/"><img src="https://img.shields.io/badge/Docs-yenchiu.com%2FWendellManual-6366f1?style=for-the-badge&logo=readthedocs&logoColor=white" alt="Docs" /></a>
  <a href="https://yenchiu.com"><img src="https://img.shields.io/badge/Blog-yenchiu.com-a855f7?style=for-the-badge&logo=jekyll&logoColor=white" alt="Blog" /></a>
  <img src="https://img.shields.io/badge/Theme-Just%20the%20Docs-7253ed?style=flat-square" alt="Just the Docs" />
  <img src="https://img.shields.io/badge/License-MIT-blue?style=flat-square" alt="MIT" />
</p>

</div>

## 关于

WendellManual 是主站 [yenchiu.com](https://yenchiu.com) 的配套文档：把博客的使用说明、建站笔记和一些零散经验整理成可以持续维护的文档站。

- 线上地址：<https://yenchiu.com/WendellManual/>
- 主站：<https://yenchiu.com>

## 内容

- **博客使用说明**：写作台、发布流程、常见问题
- **使用指南**：站点各个功能的入口与操作
- **建站笔记**：Jekyll、Chirpy、GitHub Pages 的踩坑记录
- **其他笔记**：一些长期维护的个人资料

文档按主题放在 `docs/` 下的不同文件夹里，侧边栏会自动生成。

## 技术栈

| 层次 | 选型 |
| --- | --- |
| 文档框架 | [Just the Docs](https://just-the-docs.github.io/just-the-docs/) |
| 静态站点 | Jekyll |
| 托管 | GitHub Pages（`baseurl: /WendellManual`） |
| 搜索 | Just the Docs 内置搜索 |

## 本地运行

```bash
bundle install
bundle exec jekyll serve
# 打开 http://127.0.0.1:4000/WendellManual/
```

如果只想改文档内容，直接编辑 `docs/` 下的 Markdown 即可，`index.md` 是首页。

## 目录结构

```text
.
├── docs/                # 文档正文（按主题分文件夹）
├── _includes/           # 页面片段
├── _layouts/            # 布局
├── _sass/               # 样式
├── assets/              # 静态资源
├── index.md             # 文档首页
├── _config.yml          # 站点配置（title / baseurl / url）
├── CHANGELOG.md         # 模板与站点的更新记录
└── LICENSE.txt
```

## 致谢

基于 [Just the Docs](https://github.com/just-the-docs/just-the-docs) 模板搭建；感谢模板作者与贡献者。

## License

模板与代码遵循 [MIT License](LICENSE.txt)；文档内容版权归 Wendell 所有。
