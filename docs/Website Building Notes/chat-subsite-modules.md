---
title: 聊天子站的三模块改造
parent: 建站笔记
nav_order: 3
---

# 聊天子站的三模块改造
{: .no_toc }

2026-11-04。把 `chat` 子站从「一个聊天页 + 一份剧本」，改成「壳 + 模块 + 数据」的结构，顺带加了一个对话游戏、一个自带模型的 AI 页和致谢页。本文是当时的判断、做法和验证记录。

---

## 一、判断：问题不在功能，在结构

原来的 `chat` 子站只有三样东西：一个 `index.html`、一份 `assets/dialog.json`、一堆写死的样式。想加任何东西都要动这个页面，于是功能加不动。

真正卡住的是结构：

- 页面、样式、剧本三样绑死，没有「模块」的概念；
- 没有文档层，谁帮了忙只能写在 README 的一段话里；
- 旧页面（Vue 2 + Zepto + 手写 CSS）不适合推倒重来，只能在外面加一层。

所以这次没有去「改聊天页」，而是在它旁边长出一层壳：**壳负责入口和主题，模块是独立页面，内容全部是可编辑的数据**。

```text
壳层   js/shell.js + css/shell.css    设计令牌、应用抽屉、主题、跳转
模块   index.html / game.html / ai.html / credits.html
数据   assets/*.json                  剧本、人格、设置
```

关键取舍是**不上 SPA**：站点是纯静态 + Jekyll，没有打包器。独立页面的好处是故障隔离、可单独分享、任何静态服务器都能跑；抽屉只负责跳转，不做前端路由。

---

## 二、壳层：手机式的应用抽屉

右上角四格按钮拉出底部抽屉，四个模块图标，底下是主题切换和站外链接。

![应用抽屉]({{ '/assets/images/chat-subsite/drawer.png' | relative_url }})

实现上的三个细节：

1. **主题在 `<head>` 里同步解析**。`shell.js` 加载即读 `localStorage` 并给 `<html>` 打 `data-theme`，否则深色模式会先白一下。可选「自动 / 浅色 / 深色」，自动跟随 `prefers-color-scheme`。
2. **抽屉按钮按容器注入**。页面里有 `[data-wd-apps-slot]` 就塞进去，有 `#mobile-head` 就插到旧聊天页标题栏，都没有就用悬浮按钮兜底——同一份脚本服务四种页面。
3. **深色主题只覆盖旧页面的配色**。`main.min.css` 没动，靠 `shell.css` 里的 `html[data-theme="dark"]` 覆盖 `#mobile`、`.msg`、`#prompt` 这些写死白色的地方。

---

## 三、对话游戏：《凌晨两点十七分》

一个即时通讯式的短篇：45 个节点、4 个结局、约 12 分钟。对方会「正在输入」、会发照片、会在特定前提下给出隐藏选项。

![对局界面（深色）]({{ '/assets/images/chat-subsite/game.png' | relative_url }})

走完一档之后是结局页：这一档的标题、一句台词、摘要，以及四个结局的收集进度（没走到的地方只显示问号）。

![结局页]({{ '/assets/images/chat-subsite/ending.png' | relative_url }})

### 运行时

`js/core/narrative.js`，约 550 行，做四件事：

- **条件表达式**：`close >= 3 && !photo`，自己写了个递归下降解析器，**不用 `eval`**，剧本写错只会在控制台警告，不会白屏；
- **变量结算**：`{ "close": "+=1", "truth": true }`，进入节点或选完选项时结算；
- **结局与分支**：`end` 指向结局表，`branch` 按顺序取第一个满足条件的跳转；
- **存档 / 回退**：每次选择前存快照，回退时截断消息并重新列出选项（不重播台词）。

为什么不直接上 [inkle/ink](https://github.com/inkle/ink)：ink 需要一个编译器，和「无构建」的站冲突。现在这套是它的极简版，等剧本长到几千行再换 [inkjs](https://github.com/y-lohse/inkjs)。

### 剧本格式

纯 JSON，写在 `assets/stories/night-radio.json`，格式说明在 `assets/stories/README.md`。一个节点长这样：

```json
{
  "time": "02:17",
  "messages": [
    { "who": "system", "text": "柚子 上线了" },
    { "who": "them", "text": "还醒着吗？" }
  ],
  "choices": [
    { "text": "在的，怎么了？", "goto": "n1", "set": { "close": "+=1" } },
    { "text": "你是谁？", "goto": "n2" }
  ]
}
```

选项默认会把它的文字作为「我」的气泡发出去；`silent: true` 用于「……」这种不说话的选择。

### 自检

`tools/check-story.mjs`：静态检查所有 `goto` / `next` / `branch` 目标是否存在、`when` 能否解析、有没有从开头走不到的节点；再用真实运行时随机试玩 400 次，统计结局覆盖。

```bash
node tools/check-story.mjs
# 节点 45 个 · 结局 4 个 · 随机试玩 400 次
# 走到的结局：e_quiet, e_unsent, e_dawn, e_letter
```

---

## 四、AI 模块：自带模型的对话页

![AI 模块的流式输出]({{ '/assets/images/chat-subsite/ai.png' | relative_url }})

### 没有后端

Key 只写进本机 `localStorage`，请求由浏览器直接发往用户填的地址。站点不经手任何数据，也不会有额度成本。代价是要面对 CORS。

设置面板里的预设覆盖 DeepSeek、OpenAI、硅基流动、OpenRouter、本机 Ollama 和自定义代理，每个都写了「能不能浏览器直连」的提示。OpenAI 官方接口不允许直连，所以「接口地址」这一栏本身就是给代理留的口子。

### 客户端

`js/core/ai-client.js`，只实现 OpenAI 协议里用得上的三个字段：`messages`、`stream`、`tools`。

- **流式**：`fetch` + `ReadableStream`，按行切 `data:`，拼 `delta.content`，兼容个别代理「说 stream 却返回整个 JSON」；
- **错误归类**：401 / 404 / 429 / 400 / `Failed to fetch` 各自给一句人话，`Failed to fetch` 直接提示「多半是 CORS 或地址写错」；
- **工具循环**：模型返回 `tool_calls` → 本地执行 → 以 `role: "tool"` 送回 → 最多四轮。

### 人格与工具

- 「念真」人格会去读 `assets/dialog.json`，抽十四句真实台词当语气样本塞进 system prompt，让模型学短句和停顿，而不是变成客服。
- 三个本地工具：`now`、`calc`、`roll`。`calc` 是手写的递归下降求值，不碰 `eval`。Agent 模式下界面会把调用过程画成小卡片：`⚙ 本地工具 calc((12+3)*4) → 60`。

### Markdown

`js/core/markdown.js`，约 130 行。**先转义、再排版**：结构在原始文本上识别，进入正文的每一段都先 `escape`，所以模型输出里的 HTML 一律是字面量，没有 XSS 面。代码块带复制按钮。

---

## 五、开源项目与各自的贡献

这次没有引入新依赖，全部是读别人的项目再自己写。清单也放在了站内的 `credits.html`，这里记一下「学到了什么」。

| 学到的东西 | 来源 | 许可证 |
| --- | --- | --- |
| 节点 + 变量 + 条件跳转的剧本语法 | [inkle/ink](https://github.com/inkle/ink) | MIT |
| 「选项本身就是一句台词」 | [Yarn Spinner](https://github.com/YarnSpinnerTool/YarnSpinner) | MIT |
| 剧情运行时的备选方案 | [y-lohse/inkjs](https://github.com/y-lohse/inkjs) | MIT |
| 即时通讯即叙事 | *Emily is Away*、*Digital: A Love Story*（后者用 [Ren'Py](https://github.com/renpy/renpy) 写成） | — |
| 分支地图式写作 | [Twine](https://github.com/klembot/twinejs) | GPL-3.0 |
| 多服务商配置面板 | [LibreChat](https://github.com/danny-avila/LibreChat) | MIT |
| 人格 / 助手作为一等公民 | [LobeChat](https://github.com/lobehub/lobe-chat)、[Open WebUI](https://github.com/open-webui/open-webui) | 以仓库为准 |
| 流式消息、停止生成的状态划分 | [assistant-ui](https://github.com/Yonom/assistant-ui) | MIT |
| 工具循环的写法 | [Vercel AI SDK](https://github.com/vercel/ai) | Apache-2.0 |
| 用样本教模型说话 | [SillyTavern](https://github.com/SillyTavern/SillyTavern) | AGPL-3.0 |
| 手机桌面式启动器 | [Puter](https://github.com/HeyPuter/puter)、[daedalOS](https://github.com/DustinBrett/daedalOS) | 以仓库为准 |

原则是「只借鉴、不打包」：除了原本就有的 Vue 2、Zepto、Lato 和主站主题，没有把上面任何项目塞进页面。

---

## 六、验证清单

这一轮不是「写完就算」，下面这些都真跑过：

- **剧本自检**：45 个节点、4 个结局全可达，无断链、无孤儿节点；
- **真浏览器跑全流程**：用 Chrome DevTools Protocol（`tools/ui-check.mjs`）在无头浏览器里点击选项一路走到结局，检查控制台错误、`scrollWidth` 和横向溢出；
- **两种宽度**：430px（手机）与 1200px（桌面）各截一遍，溢出为 0；
- **存档续玩**：伪造一份存档，验证「继续上次」能还原消息并停在选项上；
- **AI 链路**：本地起了一个假接口（`tools/mock-openai.mjs`）验证 SSE 流式解析、工具循环 `calc((12+3)*4) → 60`、接口不可达时的错误提示；
- **子站镜像**：在 `yen-chiu.github.io/chat/` 的路径下逐页验证，顺带修了一个旧 bug——`css/font/lato400.woff` 一直 404，Lato 其实从没生效过。

---

## 七、已知限制

- **CORS**：浏览器直连是最大的不确定性。要彻底解决只能上代理（Worker），那会带来额度和滥用问题，所以默认没做。
- **Key 在 localStorage**：BYOK 的代价。公共电脑要记得点「清空 Key」。
- **无后端**：没有跨设备同步、没有限流、没有对话日志。
- **无构建的边界**：Markdown、剧情运行时、SSE 客户端都是手写的，功能一复杂就该考虑引入构建。
- **旧页面**：`index-min.js` 是压缩过的旧代码，本轮只在外层加壳，没有重写。

---

## 八、下一步（按性价比排序）

1. 部署自己的 LLM 代理 Worker（已有 `auth.yenchiu.com` 的基建），把设置里的默认地址指向它；
2. AI 模块接站内 RAG：Jekyll 已生成搜索数据，可以直接当检索源；
3. 模块协议升级成 `modules/manifest.json` + 动态加载，让「加模块」只需丢一个文件夹；
4. 再写 1–2 个短篇剧本，或换 inkjs 承载长篇；
5. 对话改存 IndexedDB，支持导入 / 导出。

---

## 九、文件位置

```text
Chat/
├── game.html                     # 对话游戏
├── ai.html                       # AI 模块
├── credits.html                  # 致谢
├── css/shell.css                 # 设计令牌 + 抽屉 + 深色主题
├── css/game.css / ai.css / credits.css
├── js/shell.js                   # 抽屉与主题
├── js/core/narrative.js          # 剧情运行时
├── js/core/markdown.js           # 安全 Markdown
├── js/core/ai-client.js          # OpenAI 兼容客户端
├── js/game.js / ai.js            # 两个模块的页面逻辑
├── assets/stories/night-radio.json
├── assets/stories/README.md      # 剧本格式
└── tools/
    ├── check-story.mjs           # 剧本自检
    ├── ui-check.mjs              # 无头浏览器体检
    └── mock-openai.mjs           # 本地假接口
```

同步：`yen-chiu.github.io/chat/` 下是部署镜像；站点更新日志在 `_data/changelog.yml`。
