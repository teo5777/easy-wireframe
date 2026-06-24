# Easy Wireframe · 移动端原型 / 页面流程线框图

![License](https://img.shields.io/github/license/teo5777/easy-wireframe?style=flat-square)
![Skill](https://img.shields.io/badge/Skill-Agent-111111?style=flat-square)
![Single-file HTML](https://img.shields.io/badge/HTML-Single--file-0A7CFF?style=flat-square)

把「想做什么 App、有哪几页、怎么点击流转」变成一张**移动端线框流程图**：多个 iPhone 尺寸(375×812)页面按网格平铺、连线标注页面间跳转。产出是**零依赖的单文件 HTML**，浏览器直接打开。适配 Claude Code / Codex 等 Agent 环境。

![Easy Wireframe 效果预览](example-preview.png)

> 上图为内置黄金样例 `example.html`：一个电商 App 的全流程线框图，购物主线横排一行、搜索/发现/我的/门店各自分行，连线标注跳转。

## 30 秒开始

```bash
npx skills add https://github.com/teo5777/easy-wireframe --skill easy-wireframe
```

> 更新：用上面命令装的执行 `npx skills update easy-wireframe`；手动 `git clone` 装的进入该目录 `git pull`。

安装后直接对 Agent 描述需求即可：

```text
帮我做一个外卖 App 的原型，大概 5 个页面，从首页点进商家、加购物车、下单、支付。
```

## 特点

- 📱 **网格平铺 + 连线**：页面按「行,列」坐标摆放，连线说明「点击什么 → 去哪一页」（同行相邻走直线，跨行/汇聚自动绕开页面）。
- 🎨 **极简黑白灰 + Apple HIG**：尺寸、字号、图标按设计系统锁定，真机宽度下短文案不折行。
- 🧩 **丰富组件库**：导航栏 / 列表 / 对话 / 底部 Tab / 卡片 / 宫格 / 商品卡 / Feed / 表单 / 时间线 / 地图 / 柱状·折线·圆环图 / 弹窗浮层 等 30+ 形态，复制即用。
- 🧲 **布局模式（拖拽调位）**：浏览器里直接拖动页面换位置，连线实时重画；「导出布局」把坐标贴回给 AI 固化——不用再用文字描述往哪挪。
- 📌 **标注模式（改图闭环）**：在图上点一下写想法，「导出标注」生成带页面名 + 坐标的指令文本，回贴给 AI 就能精准改图。
- 🔍 **缩放 / 平移 / 截图**：工具栏缩放、`Ctrl ⌘ + 滚轮`、空格拖动画布（类 Figma）、一键导出 2× 高清 PNG。
- 🛟 **图标永不空白**：图标 CDN 失败时浮现「一键修复」入口，复制指令发给 AI 即转为内联 SVG。
- 📄 **单文件 HTML**：不需要构建、不需要服务器，发给同事、离线打开都能用。

> 打开生成的 HTML 后，点工具栏最前面的 **?** 可随时查看「布局模式 / 标注模式」怎么用。

## 改图闭环：标注 / 布局，一键导出贴回

线框图最大的痛点是**画完之后怎么改**。Easy Wireframe 把这件事做成了闭环：

- **想改内容** → 开「标注模式」，在要改的页面上点一下写想法（跨页面都行）→「导出标注」复制一段结构化文本：

  ```text
  我在原型页面上做了如下标注，请据此更新 prototype.html：
  [#1] 页面：首页 | 锚点：「景区伴游」入口 | 位置：62%,40%
       想法：这里加一个搜索框
  ```

- **想调位置** → 开「布局模式」，拖动页面换格子（拖到占用格＝两页交换）→「导出布局」复制各页坐标。

把导出的文本贴回对话框发给 AI，就能**精准定位到哪一页、哪个位置、要改什么**，不用再用嘴描述。标注存在浏览器 localStorage、按文件名隔离，关掉再打开还在。

## 适合 / 不适合

**✅ 合适**：产品早期构思 / 需求评审 / 交互流程梳理 / 给设计或开发对齐页面跳转 / 快速演示 App 页面骨架。

**❌ 不合适**：高保真视觉稿（本 skill 是黑白灰线框，不上色）/ 桌面端或网页端布局（尺寸锁定 iPhone 375×812）/ 可点击的真实交互 demo（产出是静态流程图）。

## 文件说明

| 文件 | 内容 |
|---|---|
| `SKILL.md` | Skill 清单与工作流（入口文件） |
| `design-system.md` | 颜色 / 字号(HIG) / 尺寸 / 图标 / 连线 的权威数值表 |
| `template.html` | 骨架：已含全部 CSS + 网格/连线 JS + 标注 JS + 截图 JS，复制后填充 `.flow` |
| `components.md` | 可复制的 HTML 积木：页面摆放 / 连线 / 导航栏 / 列表 / 对话 / 统计 / 30+ 扩展组件 |
| `example.html` | 黄金样例（电商 App 全流程），演示全部组件 + 网格摆放 + 统一连线 |

## 安装

**Claude Code**：复制或克隆到 skills 目录，之后输入 `/easy-wireframe` 使用。

```bash
git clone https://github.com/teo5777/easy-wireframe.git ~/.claude/skills/easy-wireframe
```

**Codex**：相同 `SKILL.md` 约定，放入 `.codex/skills/easy-wireframe/` 即可。

**其他编码 Agent**（Cursor / Kimi Code / OpenCode / Gemini CLI 等）：把仓库链接发给 Agent，让它从 `SKILL.md` 入手、按需加载其引用的支持文件。

> 普通无文件系统 / 浏览器的 Chatbot 不推荐——难以稳定产出完整流程图。

## 内置依赖（CDN，无需本地安装）

- [Lucide](https://lucide.dev) — 图标
- [html2canvas](https://html2canvas.hertzen.com) — 截图导出
