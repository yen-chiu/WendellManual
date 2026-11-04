---
title: 聊天页背景与落款：把「单调」改成「深夜的房间」
parent: 建站笔记
nav_order: 5
---

# 聊天页背景与落款：把「单调」改成「深夜的房间」
{: .no_toc }

2026-11-05。聊天页原来是一张径向渐变 + 一个悬浮的 360px 卡片，左下角是三条 16px 纯白链接。两个问题被一起提出：背景单调、左下文字突兀。这篇是完整的判断、落地与验收：为什么改、改了什么、代价多少，以及一个能直接点开切换方向的预览。

---

## 一、结论

问题不是「不够花哨」，是**图底关系反了**，和**明度优先级倒挂**。

改版只做两件事，且都是替换而非叠加：

| 换掉 | 换成 | 为什么 |
| --- | --- | --- |
| 一张径向渐变 + 悬浮卡片 | **一个有纵深的内景**：天光 → 台灯 → 空气（颗粒/暗角/24 粒光尘） | 高级感来自层次与材质，不来自更多特效 |
| 16px 纯白三条链接 | **12.5px 单行落款**：署名 │ 博客 源码 致谢 | 页脚的问题是「太响」，不是「不够有趣」 |

工作量：P0–P2 约一天（含验收）；最终只新增 2 个文件、约 7KB 源码。

![改版后的深色聊天页]({{ '/assets/images/chat-ambient/after-dark.webp' | relative_url }})

---

## 二、诊断（对着线上页面，逐条有据）

### 2.1 背景：低空间频率 + 图底反转

| # | 问题 | 证据（1920×1000 实测） |
| --- | --- | --- |
| 1 | 一层渐变承担全部信息 | `#13302c → #07110f`，约 85% 面积落在同一段很窄的明度里 |
| 2 | **卡片比背景更暗** | 卡片面 `#131e21`（L\*≈12）落在顶部背景 `#13302c`（L\*≈21）上，是 −13 的负差 |
| 3 | 只有两层 | 背景 / 卡片；缺「桌面」这一中间层，360×940 的矩形独扛注意力 |
| 4 | 没有时间维度 | 完全静止 |
| 5 | 浅色更糟 | 大面积高饱和薄荷 + 白卡片，边界只靠柔光 |

第 2 条是「廉价感」的真正来源：卡片上半像洞、下半像浮起来，只能靠 `0 0 36px rgba(34,195,170,.22)` 的青色外发光硬撑。不是阴影重，是方向错。

![改版前的深色聊天页]({{ '/assets/images/chat-ambient/before-dark.webp' | relative_url }})

### 2.2 落款：明度优先级倒挂

| 事实 | 数值 | 后果 |
| --- | --- | --- |
| 字号 / 颜色 | 16px / `#fff` | 比 13px 的卡片标题更大更亮 |
| 容器宽度 | `max-width:20%` → 实测 **313.8px** | 视口 < **1569px** 必折行，1440 / 1366 全中 |
| 折行后 | 每项 `position:relative; top:-4px` | 两行基线不齐、左边缘随字宽参差 |
| 浅色对比度 | 白字落在 `#A1E5DA` 上 = **1.4:1** | 标准要求 4.5:1，等于不可读 |
| 语法 | `Source code` / `Blog` / `Original author: Ovilia` | 11 / 4 / 21 字符，还带冒号，不是一组 |
| 重复 | 抽屉底部已有「博客 / 源码 / 联系」 | 同一信息一屏出现两次 |

### 2.3 顺手发现的既有缺陷

1. `body { font-family: Lato }` 单值，**没有中文后备**，中文全部落到浏览器默认字体。
2. `@font-face` 声明 `font-weight:900` 却指向 `lato400.woff`，粗体是合成伪粗。
3. 右下 4 个社交链接**没有可访问名称**；SVG 里写死 `fill="#FFFFFF"`，浅色下直接消失。

---

## 三、从 GitHub 学到什么

按「能拿走的手艺」排序，不按 star：

| 仓库 | 它做对了什么 | 能拿走的 | 用不用 |
| --- | --- | --- | --- |
| [DavidHDev/react-bits](https://github.com/DavidHDev/react-bits) ★51k | 63 个背景组件，全部参数化、可调、有降级 | **组织方式**：背景是可配置的氛围层，不是魔法效果 | 不引库 |
| [paper-design/shaders](https://github.com/paper-design/shaders) ★3.9k | 零依赖 canvas shader，`speed / distortion` 参数克制 | **慢是高级感的关键**；留给 V2 | 备选 |
| [magicuidesign/magicui](https://github.com/magicuidesign/magicui) ★23k | 点阵 / 网格，几乎全是 CSS + SVG mask | **网格必须被暗角吃掉**，不能铺满全屏 | 借鉴 |
| [tengbao/vanta](https://github.com/tengbao/vanta) ★7.3k | three.js 3D 背景 | 体积 / 电量 / 气质都不匹配 | 不用 |
| [tsparticles/tsparticles](https://github.com/tsparticles/tsparticles) ★9k | 功能齐全的粒子库 | 100KB+，默认效果容易「素材站」 | 不用 |
| [PavelDoGreat/WebGL-Fluid-Simulation](https://github.com/PavelDoGreat/WebGL-Fluid-Simulation) ★16.8k | 流体模拟，冲击力强 | 与「安静私密」冲突，移动端 GPU 成本高 | 不用 |

**结论：不上库，上做法。** 分层、克制、可降级、CSS 优先、JS 必须能一键关掉。

---

## 四、设计师视角

### 4.1 概念：深夜的房间

不是「星空 / 极光」的贴图，是一个**内景**：一盏台灯、一张桌子、空气里的尘。

- **天光**：只负责上下的明度差，不再在顶部中央打一个比卡片还亮的洞。
- **台灯**：卡片背后一束偏暖的柔光，让卡片成为画面里唯一的光源，图底关系翻正。
- **空气**：5.5% 颗粒（去暗部色带）+ 暗角 + 24 粒光尘。

为什么不用「极光球」：极光是**外景 / 开阔 / 远**，与「深夜私聊」的私密语义相反；「几团模糊渐变球」也是 2022–2024 的通用模板语言，容易被认出。

### 4.2 三个方向

| 方向 | 做法 | 成本 | 判断 |
| --- | --- | --- | --- |
| 01 极光 | 4 团 54–88s 缓慢漂移的光雾 + 颗粒 + 暖灯 | 纯 CSS | 好看，但最「模板」 |
| **02 萤火（选它）** | 极光底 + 24 粒光尘（30fps、无指针视差、无发光叠加） | +3KB JS | 有生命感，仍然安静 |
| 03 静室 | 桌面光斑 + 只在光斑里出现的网格 + 暗角，完全静止 | 纯 CSS | 最「设计」，适合 AI / 致谢页 |

关键取舍：

- 光尘从 90 粒降到 **24 粒**，合成从 `lighter` 改 `source-over`，去掉鼠标视差 —— 否则就是「interactive background 素材站」。
- 浅色主题**关掉光尘**：浅底上的粒子 = 屏幕脏了。
- 动效 54–88s 一轮，位移 ≤ 4%，透明度变化 ≤ 0.08。看一眼是静的，看久了才发现它在呼吸。

### 4.3 落款：从「法务信息」到「身份信息」

四个方案都做了，结论是**降权 + 重新分组**，不是加装饰：

| 方案 | 做法 | 问题 |
| --- | --- | --- |
| A 现状 | 16px 白字三条 | 权重倒挂、折行、浅色失效 |
| B 署名条 | 圆形 W 徽标 + 两行身份 + 竖线 + `01/02/03` | 5 个元素过多；9.5px 会糊；编号暗示顺序，这里没有顺序 |
| C 馆藏牌 | 292px 毛玻璃卡片 + 标题 + 两行介绍 | 同屏第二个卡片 = 双焦点；把「关于」塞进角落 |
| **D 落款行（选它）** | `Wendell │ 博客↗ 源码↗ 致谢` | 一行、三块、永不折行 |

最终规格：

| 属性 | 深色 | 浅色 |
| --- | --- | --- |
| 容器 | `fixed; left:32px; bottom:24px; height:24px; gap:12px` | 同 |
| 署名 | 12.5px / 500 / `#B3C6C1` | `#1D2B27` |
| 细线 | `1px × 11px / rgba(232,242,239,.16)` | `rgba(12,60,52,.16)` |
| 链接 | 12px / 400 / `#7F948F` | `#36473F` |
| hover | `#5FE3CB` + 下划线 0→100%（0.26s） | `#0A6555` |

- **不发光、不位移、不加第二层模糊。**
- 与右侧图标共用 24px 中线；`≥900px` 显示，以下收起，三项并进抽屉底部链接。
- `Original author: Ovilia` 不再出现在页脚，致谢页的 Ovilia 条目改为「原作者」标签 —— 署名义务仍在，只是不占视觉焦点。

### 4.4 廉价感的阈值

| # | 陷阱 | 阈值 |
| --- | --- | --- |
| 1 | 发光叠层 | 外发光只留 1 层、alpha ≤ .12、blur ≤ 72px；文字/图标永不加 glow |
| 2 | 颗粒过重 | 暗色 0.03–0.05、浅色 0.02–0.03；tile ≥ 200px |
| 3 | 色相过多 | 全页 ≤ 2 个色相；背景层峰值 alpha ≤ .30；暗色背景 L\* ≤ 22 |
| 4 | 图底反转 | 卡片必须亮于周边背景，并配真实接触阴影 |
| 5 | 图标不统一 | 统一 20×20、stroke 1.6、`currentColor` |

---

## 五、架构师视角

### 5.1 层叠契约（最容易翻车）

直接把背景层追加到 `body` 尾部会出事：`#mobile` 是 absolute + transform 且 `z-index:auto`，与 `z-index:0` 的 ambient 同处一个绘制步骤，靠树序决胜负；`grain(1)` / `vignette(2)` 若在根上下文里拿 1/2，会盖住卡片；`credits` 的 `.wd-doc` 连定位都没有。

正确写法：把 0/1/2 关进 ambient 自己的堆叠上下文，再把内容统一抬到 1。

```css
.wd-ambient {
  position: fixed; inset: 0; z-index: 0;
  overflow: hidden; pointer-events: none;
  isolation: isolate;            /* 把 mix-blend-mode 限制在背景内部 */
}
.wd-ambient__grain    { z-index: 1; }
.wd-ambient__vignette { z-index: 2; }

#mobile, #meta-link, #social-link, .wd-signature, .wd-social,
.wd-app, .wd-phone { z-index: 1; }   /* 卡片是 absolute，只补 z-index，不改 position */

.wd-doc { position: relative; z-index: 1; }  /* 原本无定位，补 position 不给偏移 */
```

### 5.2 运行时契约

```js
window.WDAmbient = {
  mount(), destroy(),
  setMode('auto' | 'still' | 'firefly'), getMode(),
  suspend('drawer'), resume('drawer'),
  refresh(), debug()
};
```

- 暂停原因用集合 `{hidden, drawer, reduced, saveData, small}`，所有事件只改状态再 `tick()`。
- 帧循环的 id 只在续帧时赋值，避免 `stop()` 被上一帧续上。
- 主题切换由 `shell.js` 的 `applyTheme()` 派发 `wd:themechange`，背景只重建 2 张 64×64 sprite。
- 粒子坐标归一化到 0..1，resize 不重新播种（iOS 地址栏不会让粒子瞬移）。
- 抽屉打开 `suspend('drawer')`：抽屉有全屏 `backdrop-filter`，底下再跑动画就是每帧重算模糊。
- 「念真在打字」用 `MutationObserver` 观察 `#input-hint`，不改旧 Vue 逻辑。

### 5.3 性能红线

- 只允许动画 `transform` / 容器 `opacity` / canvas 内容。
- 禁止动画 `filter` / `backdrop-filter` / `mask-image` / 全屏渐变 `background-position`。
- 30fps（33ms 节流）、DPR ≤ 1.5、24 粒、sprite 预渲染、`document.hidden` 暂停。
- 颗粒的 `mix-blend-mode: overlay` 只在 `≥1024px` 且允许动效时开。

### 5.4 降级

| 场景 | 行为 |
| --- | --- |
| 无 JS | 不挂载背景层，`body` 原有渐变照常兜底 |
| 无 canvas | 只留 CSS 层，静默降级 |
| `prefers-reduced-motion` | 动画全停，光尘不画 |
| `saveData` | 只留 CSS 层 |
| ≤520px | 背景层 `display:none`（卡片已满屏），连带停掉内部动画 |
| 浅色 | 光尘不画，只留光雾与暗角 |

### 5.5 缓存与双仓

- `tools/stamp.mjs` 用 `css/shell.css + css/ambient.css + js/shell.js + js/ambient.js` 的内容哈希前 10 位做 token，统一重写 4 个页面的 8 处引用。内容不变 token 不变，内容一变 token 必变，不再靠手写 `?v=`。
- 当前部署仍是「Chat 仓 + 博客仓 chat/」两份副本，靠手动同步；本次两边提交一致。

---

## 六、这次改了什么

| 文件 | 改动 |
| --- | --- |
| `css/ambient.css`（新） | 天光 / 极光 / 台灯 / 颗粒 / 暗角 / 光尘层 + 层叠契约 + 模式与降级 |
| `js/ambient.js`（新） | 挂载、24 粒光尘（30fps、DPR≤1.5）、启停策略、主题联动、打字呼吸 |
| `js/shell.js` | 抽屉顺序 聊天→AI→游戏→致谢；新增「氛围」开关；派发 `wd:themechange`；抽屉开关时暂停背景 |
| `css/shell.css` | 落款与社交图标样式、中文后备字体栈、浅色页脚重算、浅色卡片阴影 |
| `index.html` | 移除三条白字链接，换成单行落款；社交图标补 `aria-label` / `rel`；引入 ambient |
| `ai/game/credits.html` | 引入 ambient |
| `credits.html` | Ovilia 条目标签改为「原作者」 |
| `tools/stamp.mjs`（新） | 内容哈希打版本号 |

上线：`Chat@43ddccb`、`yen-chiu.github.io@075c512`，线上地址 <https://yenchiu.com/chat/>。

---

## 七、验收记录

| 检查 | 结果 |
| --- | --- |
| 1920×1000 深色 | 背景三层可见、光尘 24 粒、落款单行不折行、社交图标随主题变色 |
| 1920×1000 浅色 | 光尘停止（`debug().theme=light, running=false`），落款对比度约 5.4:1（原来 1.4:1） |
| 内容层级 | 卡片中心 `elementFromPoint` 命中 `mobile-body-content`，未被颗粒/暗角覆盖 |
| 抽屉 | 打开后 `WDAmbient.debug().running === false`，关闭恢复 |
| 390×844 | 背景层 `display:none`，落款隐藏，`scrollWidth=390` 无横向滚动 |
| 抽屉顺序 | `chat, ai, game, credits` |
| 控制台 | 无报错 |
| 未做 | 真机低端安卓 / iPhone、系统「减少动态效果」的真机验证 |

![抽屉：配色 / 氛围两组开关]({{ '/assets/images/chat-ambient/drawer.webp' | relative_url }})

![移动端 390×844：卡片满屏，背景层关闭]({{ '/assets/images/chat-ambient/mobile.webp' | relative_url }})

![浅色模式]({{ '/assets/images/chat-ambient/after-light.webp' | relative_url }})

---

## 八、后续

1. **真机一轮**：低端安卓滚动时的帧率、iPhone 地址栏收放、系统深色切换。
2. **同步脚本**：`tools/sync-chat.mjs --check`，把「两份副本」的漂移变成可检测的。
3. **可选 V2**：自托管 Paper Shaders 的 MeshGradient（`speed≈0.15`），只在桌面、30fps、静态海报降级。
4. **预览**：方向对比原型在 [`assets/demo/chat-ambient-preview.html`]({{ '/assets/demo/chat-ambient-preview.html' | relative_url }})，`1–4` 换背景，`Q W E R` 换落款，`T` 切深浅，`M` 关动效。
