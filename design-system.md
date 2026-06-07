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

扩展组件常用图标（均为真实存在的 Lucide 名）：`shopping-bag` `store` `compass`
`layout-grid` `grid-2x2` `package` `truck` `tag` `tags` `filter` `sliders-horizontal`
`image` `images` `camera` `play` `star` `message-circle` `message-square` `share-2`
`thumbs-up` `bookmark` `send` `gift` `percent` `flame` `eye` `sparkles` `list`
`check` `minus` `trash-2` `chevron-down` `arrow-right` `circle-user` `users`。

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

**三种连接关系**（详见 `components.md`）：
- **顺序** `.arrow`：相邻两页（本节）。
- **发散分支** `.flow.branched` + `.fork` + `.branch[data-tag]`：一页分多路，连线由 `drawForks()` 画；支持嵌套（子分支首页设为新 `origin`）。
- **跨页/汇聚** `.xlink`：`<div class="xlink" data-from data-to data-tag data-side data-dash>`，由 `drawXlinks()` 在 `.canvas` 级 overlay 画正交折线；多条指向同一 `id` 即汇聚。线条用 `--line`，虚线 `data-dash="1"`，tag 文字垫 `--bg` 底色保证压住穿过的线可读。`.canvas` 已设 `position:relative` 作为其定位上下文。

---

## 六、扩展组件（新增 · 主流 App 高频形态）

> 这批组件用于撑起电商 / 社交 / 内容 / 工具类 App。**全部只用第一节那 9 个颜色变量、
> 第二节那套 HIG 字号档**——没有新色、没有新字号、没有新字体。可复制的 HTML 片段见
> `components.md` 第 5 节。下表是它们的关键数值，便于理解与微调（同样**不要改 `<style>`**）。

### A. 框架 chrome

| 组件 | 类 | 关键数值 | 用色 / 字号 |
|---|---|---|---|
| 底部 Tab Bar | `.tabbar` / `.tab` / `.tab.active` | 内距 `8 6 10`；图标 24；标签 11px | active 用 `--ink`，未选 `--muted`；顶部 `1px --line-soft` |
| 搜索栏 | `.searchbar` | 外距 `12 16`，高 40，圆角 12，图标 18 | 底 `--fill`，占位文字 15px `--muted` |
| 分段控制器 | `.segment` / `.seg` / `.seg.active` | 轨道圆角 10，内距 3，段内距 `7 0` | 轨道 `--fill`；选中段白底 + `--ink` 600 |
| 底部 CTA 栏 | `.cta-bar` | 内距 `12 16 16`，顶部发丝线 | 容器白底；按钮见下 |
| 通用按钮 | `.btn` `.btn.primary/.ghost/.outline` `.btn.sm` | 高 48 圆角 14（sm：36/10）；17px 600 | primary 实心 `--ink`；ghost `--fill`；outline 白底 + 发丝边 |

### B. 内容容器

| 组件 | 类 | 关键数值 | 用色 / 字号 |
|---|---|---|---|
| 通用卡片 | `.card` / `.card-hd` / `.card-bd` | 外距 `12 16`，圆角 14，发丝边；头部内距 `14 16` | 标题 15px 600；「更多」13px `--muted` |
| 功能宫格 | `.grid` / `.grid-item` | 一行 4 列（25%）；图标底 48 圆角 14 | 图标 24；标签 12px `--ink` |
| 横滑卡片 | `.hscroll` / `.hcard` | `overflow-x:auto`；卡宽 140 | 标题 15px 600；副文 13px `--muted` |
| Banner | `.banner` / `.dots` | 高 140，圆角 14；圆点 6，激活变 16 宽条 | 底 `--fill`，占位图标 32 `--line` |

### C. 媒体 / 列表变体

| 组件 | 类 | 关键数值 | 用色 / 字号 |
|---|---|---|---|
| 缩略图占位 | `.thumb` | 圆角 10，居中 `image` 图标 24 | 底 `--fill`，图标 `--line` |
| 媒体列表项 | `.media-item` | 缩略图 72×72；内距 `12 20` | 主 17px 600 / 副 15px `--muted` / 元信息 12px `--line` |

### D. 电商

| 组件 | 类 | 关键数值 | 用色 / 字号 |
|---|---|---|---|
| 商品卡网格 | `.product-grid` / `.product` | 两列（`calc(50% - 6px)`）；图大 150 高；卡圆角 14 | 名 15px；价 `.price` 17px 600；加购钮 28 圆 `--ink` |
| 详情大价格 | `.price-lg` | 28px 700 | `--ink`，货币符号 `<small>` 15px |

### E. 社交 / Feed

| 组件 | 类 | 关键数值 | 用色 / 字号 |
|---|---|---|---|
| Feed 动态卡 | `.feed-item` / `.fhead` / `.facts` | 内距 `16 20`；配图 180 高；操作行 gap 28 | 名 15px 600 / 时间 12px / 正文 15px；操作 13px `--muted` 图标 18 |
| 评论行 | `.comment` | 内距 `12 20`，小头像 + 文案 | 名 13px 600；正文 15px |
| 小头像 | `.avatar-sm` | 40 圆 | 底 `--fill`；可放 20 图标或首字母 15px 600 |

### F. 表单 / 小部件 / 状态

| 组件 | 类 | 关键数值 | 用色 / 字号 |
|---|---|---|---|
| 表单输入 | `.form-field` / `.finput` `.finput.ph/.area` | 输入框 min 44 圆角 10；多行 `area` min 88 | 底 `--fill`；已填 `--ink` / 占位 `.ph` `--line`；标签 13px `--muted` |
| 筛选胶囊 | `.chips` / `.chip` / `.chip.active` | 圆角 16，内距 `7 14` | 普通 `--fill` + `--ink`；激活实心 `--ink` 白字；13px |
| 空状态 | `.empty` | 居中；图标底 72 圆 | 图标 32 `--line`；主 17px 600 / 副 14px `--muted` |
| 时间线 | `.timeline` / `.tl-item` `.tl-item.done` | 圆点 14；竖线 2px 贯穿 | 未达点描边 `--line`；已达 `.done` 实心 `--ink`；主 15px 600 / 副 13px |
| 星级评分 | `.rating` | 5 星 15px；`.off` 为空心 | 实心 `--ink`（`fill`）；空心 `--line`；数值 13px `--muted` |

**关于贴底组件**：`.tabbar` / `.cta-bar` 都带 `flex-shrink:0`，作为 `.screen` 的**最后一个子节点**
自然贴底（`.screen` 是 `flex-column` + `min-height:812`，中间的 `.body`/`.stat-scroll` 用 `flex:1` 撑开）。
一个页面里 Tab Bar 与 CTA 栏二选一，不要同时出现。

### G. 地图 / 图表（线框风，纯 CSS / 内联 SVG，无图片依赖、可正常截图）

| 组件 | 类 | 关键数值 | 用色 / 字号 |
|---|---|---|---|
| 地图 | `.map` / `.road`(`.v`) / `.pin` / `.map-card` | 高 240；网格 32px；定位针 30 圆头尖尾；信息卡浮底 | 底 `--fill`，网格 `--line-soft`，道路 `--hairline`，针 `--ink` |
| 折线图 | `.linechart` + `svg(viewBox 0 0 300 160)` | x 10→290 均分、y 顶 30/底 130；点 r3 | 线/点描边 `--ink`，面积 `.ln-area` `--fill-2`，网格 `--line-soft`，标签 12px |
| 圆环图 | `.donut` / `.d-seg`(`.soft`) / `.donut-legend` | 半径 50、周长≈314；环宽 14；段长=占比×314，offset=−前段累计 | 首段 `--ink`，次段 `.soft` `--line`，轨道 `--fill-2`；中心数 24px 700 |

> 三种图表配齐：**柱状图** `.bars`（第二节统计页）/ **折线图** `.linechart` / **圆环图** `.donut`。
> 折线、圆环都用内联 SVG（不用 `<use>`，html2canvas 截图正常）。坐标/段长由作者按真实数据算，
> `components.md` 第 5.18 / 5.19 节有可直接套用的算法说明。

**商品卡对齐**：`.product .pname` 用 `height:2.7em` + 两行截断**恒占两行**——单行标题也预留第二行空间，
保证同排卡片的价格行与加购钮始终水平对齐（单/双行混排不再错位）。
