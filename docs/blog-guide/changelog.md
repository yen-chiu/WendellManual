---
title: 更新日志
parent: 博客使用说明
nav_order: 4
---

# 更新日志

更新日志不是一个页面模板，是一个 YAML 文件：`_data/changelog.yml`。

`/changelog/` 把它渲染成时间线，首页只露最近三条。

## 一条长什么样

```yaml
- date: 2026-11-03
  kind: feature
  scope: 主站
  title: 页脚加了「刷新缓存」
  note: 不用再去设置里删网站数据，点一下就好。
  links:
    - { label: 相关文章, url: /posts/refresh-button/ }
```

| 字段 | 说明 |
| --- | --- |
| `date` | `YYYY-MM-DD` |
| `kind` | `feature` 新功能 · `fix` 修复 · `content` 内容 · `style` 样式 · `infra` 基建 |
| `scope` | `主站` · `写作台` · `子站` |
| `title` | 一句话，40 字以内 |
| `note` | 补一句具体的，60 字以内 |
| `links` | 可选，`label` 加 `url` |

## 三条规矩

**最新的放最上面。** 页面按文件顺序渲染，不做排序——这样同一天记好几条时，
先后顺序就是你写的顺序，不会被日期排序打乱。

**一次发布记一条，不按 commit。** git log 里一半是错字修正和格式调整，
照搬过来是噪音。

**写不下就写成文章。** 标题超过 40 字，说明这件事值得一篇正文，不是三行。

## 校验

CI 会检查 `date` 格式、`kind` 枚举、`scope` 取值，以及标题和备注的长度。
本地：

```bash
node tools/check-frontmatter.mjs
```
