# 设计系统 · 权威参照

> 这是本 Skill 的「度量衡」。`template.html` 的 `<style>` 已经按这张表锁定好全部数值。
> 生成页面时**对照本表理解每个数字的含义**，但**不要去改 `<style>`**——改一个值就会破坏整体协调。
> 风格定位：移动端线框 / 极简黑白灰 / iOS 尺寸标准 / Apple HIG 字号体系。

---

## 一、颜色变量（`:root`）

全局只用这 9 个变量，不要引入新色。整体是「干净冷白灰 + 近黑文字」，没有彩色。

| 变量 | 值 | 用途 |
|---|---|---|
| `--ink` | `#33373d` | 主文字、深色按钮/气泡底、开关开启态——近黑但偏柔 |
| `--line` | `#b7bcc2` | 图标描边、chevron 等中性线条 |
| `--line-soft` | `#ededf0` | 分割线（极浅）、开关关闭态底 |
| `--hairline` | `#e6e7ea` | 卡片发丝边框 |
| `--fill` | `#f6f7f8` | 浅填充：图标底、输入框、对方气泡 |
| `--fill-2` | `#eef0f2` | 次级填充：己方气泡底、柱状图普通柱 |
| `--bg` | `#f3f4f6` | 页面背景（配合顶部径向高光渐变） |
| `--muted` | `#9aa0a6` | 次要文字、副标题、说明文案 |
| `--white` | `#ffffff` | 卡片底、导航底 |

背景：`radial-gradient(circle at 50% 0%, #fff 0%, var(--bg) 60%)` + `background-attachment: fixed`。

---

## 二、字号表（Apple HIG 映射）

字号是这次反复打磨的核心——按 iPhone 真机宽度(375)排版，短文案不折行。**严格沿用，不要缩小。**

| 元素 | 字号 | 字重 | HIG 层级 |
|---|---|---|---|
| 页面大标题 `.page-header h1` | 22px | 600 | Title |
| 导航栏标题 `.navbar` | 17px | 600 | Headline |
| 列表主文本 `.t1` | 17px | 600 | Body |
| 列表副文本 `.t2` | 15px | — | Subhead |
| 对话气泡 `.bubble` | 17px | — | Body（iMessage 同款） |
| 权限提示标题 `.pt1` | 15px | 600 | Subhead |
| 权限提示副文 `.pt2` | 13px | — | Footnote |
| 输入框占位 `.field` | 15px | — | Subhead |
| 分组标题 `.section-cap` | 13px | — | Footnote（大写+字距） |
| 统计大数字 `.stat .num` | 28px | 700 | 展示性 |
| 统计标签 `.lbl` / 进度文案 | 12–13px | — | Caption/Footnote |
| 柱状图数值/标签 | 12px | — | Caption |
| 页面副标题 / 头像副文 `.sub` | 13px | — | Footnote |
| 气泡发送人 `.who` / stage 编号 | 11–12px | — | Caption |

字体栈：`"PingFang SC", "Helvetica Neue", "Microsoft YaHei", system-ui, sans-serif`。

---

## 三、尺寸与间距

### 页面卡片
| 项 | 值 |
|---|---|
| `.screen` 尺寸 | 宽 **375** / **`min-height: 812`**（iPhone 逻辑分辨率；内容多时向下延长，不裁切） |
| `.screen` 圆角 | 30px（`overflow:hidden` 仅护圆角；内层容器不要再加 overflow） |
| `.screen` 边框 | `1px solid --hairline` |
| 阴影 | 三层叠加（1/2、8/24、24/48），极淡 |

### 导航栏
| 项 | 值 |
|---|---|
| 高度 | **56px** |
| 左右内边距 | 16px |
| `.nav-btn` 按钮 | 32 × 32，圆角 8 |
| `.nav-btn.ghost` | 透明占位（用于平衡左右，使标题居中） |

### 列表项 `.list-item`
| 项 | 值 |
|---|---|
| 内边距 | `14px 20px` |
| 图标与文字 gap | 14px |
| 图标底 `.ic` | 44 × 44，圆角 12，底色 --fill |
| 主副文行距 `.t2 margin-top` | 3px |
| 分割线 | 底部 `1px --line-soft`，`:last-child` 去掉 |

### 开关 `.switch`（iOS 原生比例）
| 项 | 值 |
|---|---|
| 轨道 | 51 × 31，圆角 16 |
| 滑块 `.knob` | 27 × 27，圆，top/left 2 |
| 开启态 | 轨道变 --ink，`.knob` left 22 |

### 对话页
| 项 | 值 |
|---|---|
| `.chat-hero` 头像 | 72 × 72 圆 |
| hero 内边距 | `28px 0 24px` |
| `.perm-tip` | margin `16 16 4`，圆角 14，内边距 `13 15`，pic 36 圆 |
| `.bubble` | 圆角 20，max-width 78%，内边距 `11 15`，行距 1.45 |
| 入站气泡尾角 | `border-bottom-left-radius:5` |
| 出站气泡尾角 | `border-bottom-right-radius:5`，底 --ink 文白 |
| `.chat-input` | 高 60，field 高 40 圆角 20，send 40 圆 |

### 统计页
| 项 | 值 |
|---|---|
| `.stat-summary` | 内边距 `24 12 20`，三栏等分，栏间竖线 1×30 |
| `.stat-progress` 轨道 | 高 10，圆角 5，fill 用 --ink |
| `.chart-block` 柱状区 | 高 200，柱间 gap 12 |
| 柱 `.bar` | 宽 70%（max 30），圆角顶 `7 7 0 0`，普通色 --fill-2 |
| 高亮柱 `.bar-col.hot .bar` | 用 --ink（标注当日最高/重点） |

### 画布与流程
| 项 | 值 |
|---|---|
| `.canvas` | `width: max-content; margin: 0 auto; padding: 40px 48px 90px` |
| `.page-header` | 下边距 56，居中 |
| `.flow` | `display:flex; flex-wrap:nowrap; gap:4` |
| `.stage` | 纵向，`gap:16`，顶部是 `.stage-label` 编号 |
| `.stage, .arrow` | **`flex-shrink: 0`**（绝不被压缩） |
| `.arrow` | `min-width:118; padding-top:400`（对齐 812 高页面的纵向中部） |
| 箭头线 SVG | `width:104 height:12`，见下方画法 |

---

## 四、图标（Lucide）

统一通过 CDN：`https://unpkg.com/lucide@latest/dist/umd/lucide.min.js`，页面末尾 `lucide.createIcons()`。
写法：`<i data-lucide="图标名"></i>`。统一 `stroke-width: 1.75`，`color: --ink`。

| 位置 | 尺寸 |
|---|---|
| 列表图标 `.list-item .ic` | 24 × 24 |
| 导航按钮 `.navbar .nav-btn` | 20 × 20 |
| chevron `.chev` | 20 × 20（色 --line） |
| 对话头像 `.chat-hero .avatar` | 36 × 36 |
| 权限提示 `.perm-tip .pic` | 18 × 18 |
| 发送按钮 `.chat-input .send` | 18 × 18 |

常用图标名：`chevron-left` `chevron-right` `settings` `map-pin` `music` `bar-chart-3`
`message-circle-question` `volume-2` `unlink` `locate-fixed` `arrow-up` `user` `bell`
`shield` `heart` `search` `home` `shopping-cart` `credit-card` `clock` `calendar`
`plane` `train-front` `utensils` `building` `building-2` `landmark` `wallet` `receipt`
`ticket` `info` `map` `bed` `plus` `plus-circle` `check-circle-2` `more-horizontal`。

⚠ **只用确实存在的 Lucide 图标名**：拼错或用了不存在的名字（如 `torii-gate`、`temple` 等）
会渲染成**空白**，不报错，很隐蔽。拿不准时优先从上面清单里选；要用清单外的新图标，
先到 lucide.dev 确认该 kebab-case 名称存在，再使用。

---

## 五、箭头 SVG（直线 + 箭头一体绘制，绝不错位）

```html
<div class="arrow">
  <div class="tag">点击「xxx」</div>
  <div class="line">
    <svg viewBox="0 0 104 12" fill="none" xmlns="http://www.w3.org/2000/svg">
      <path d="M0 6 H102 M97 2 L102 6 L97 10"
            stroke="currentColor" stroke-width="1"
            stroke-linecap="round" stroke-linejoin="round"/>
    </svg>
  </div>
</div>
```

`.tag` 在上、箭头在下；`padding-top:400` 让整组对齐到页面纵向中部。多页面时每两页之间插一个 `.arrow`。
