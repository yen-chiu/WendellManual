---
title: 建站笔记
nav_order: 4
---

# 建站笔记
{: .no_toc }

## 目录
{: .no_toc .text-delta }

1. TOC
{:toc}

---
# lunr.js 添加中文支持
`lunr.js` 是一个轻量级的 JavaScript 库，用于在浏览器端进行全文搜索。下面是使用 `lunr.js` 的详细步骤，特别是添加中文支持的方式：

## 基本使用 Lunr.js
### 1. 引入 Lunr.js
通过 `<script>` 标签加载库文件，或者使用 npm 安装。

#### 方法 1：直接加载文件
从 [lunr.js 官网](https://lunrjs.com/) 或 CDN 加载：
```html
<script src="https://cdn.jsdelivr.net/npm/lunr/lunr.min.js"></script>
```

#### 方法 2：使用 npm
```bash
npm install lunr
```
然后在 JavaScript 文件中引入：
```javascript
const lunr = require('lunr');
```

---

### 2. 构建索引
#### 创建索引对象
创建包含待搜索内容的索引：
```javascript
const idx = lunr(function () {
  this.field('title');   // 定义需要索引的字段
  this.field('content');
  this.ref('id');        // 每条记录的唯一标识符
  
  // 添加文档
  documents.forEach(doc => this.add(doc));
});
```

#### 示例文档数据
```javascript
const documents = [
  { id: 1, title: 'Hello World', content: 'This is a test document.' },
  { id: 2, title: '恁好，世界', content: '这是一个中文测试文档。' }
];
```

---

### 3. 执行搜索
通过关键词查询索引：
```javascript
const results = idx.search('测试');
console.log(results);
```
结果为匹配的文档列表，包含 `ref` 和匹配分数。

---

## 添加中文支持

### 1. 引入 Lunr.js 中文分词插件
`lunr.js` 默认不支持中文，需要引入 [lunr-languages](https://github.com/MihaiValentin/lunr-languages) 插件。

#### 安装插件
直接使用 CDN 或 npm 安装：
```bash
npm install lunr-languages
```

加载插件：
```html
<script src="https://cdn.jsdelivr.net/npm/lunr/lunr.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lunr-languages/lunr.stemmer.support.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lunr-languages/tinyseg.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lunr-languages/lunr.zh.js"></script>
```

或者在 Node.js 中：
```javascript
const lunr = require('lunr');
require('lunr-languages/lunr.stemmer.support')(lunr);
require('lunr-languages/tinyseg')(lunr);
require('lunr-languages/lunr.zh')(lunr);
```

---

### 2. 初始化支持中文的索引
修改索引构建逻辑，使用中文语言支持：
```javascript
const idx = lunr(function () {
  this.use(lunr.zh); // 使用中文分词插件

  this.field('title');
  this.field('content');
  this.ref('id');

  documents.forEach(doc => this.add(doc));
});
```

---

### 3. 配置中文分词
如果文档内容较多，可以使用服务端预分词工具（如 `nodejieba` 或 Python 的 `jieba`），将分词结果直接存入索引文件中。

#### 示例：使用 `nodejieba` 分词
安装 `nodejieba`：
```bash
npm install nodejieba
```

在生成索引时，将内容进行分词处理：
```javascript
const jieba = require('nodejieba');
const documents = [
  { id: 1, title: '恁好，世界', content: jieba.cut('这是一个中文测试文档。').join(' ') }
];

const idx = lunr(function () {
  this.use(lunr.zh);
  this.field('title');
  this.field('content');
  this.ref('id');

  documents.forEach(doc => this.add(doc));
});
```

---

## 部署到前端

1. 将生成的索引（`JSON` 文件）部署到前端。
2. 在浏览器端加载索引并执行搜索：
```javascript
fetch('/path-to-index.json')
  .then(response => response.json())
  .then(data => {
    const idx = lunr.Index.load(data);
    const results = idx.search('测试');
    console.log(results);
  });
```

---

## 总结
通过以下步骤可完成中文搜索功能：
1. 引入 `lunr.js` 和中文支持插件。
2. 使用预分词工具处理文档内容。
3. 在前端加载索引并调用 `search()` 方法进行查询。

---

## 文档站迁移

- [Folio 文档迁移记录](./folio-migration/)
