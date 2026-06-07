# 组件积木库

> 复制下面的片段拼进 `template.html` 的 `.flow` 占位处即可。所有片段都依赖模板里已有的 CSS——
> 不要额外写样式。只替换文案、图标名(`data-lucide`)、数值。
> 图标名清单见 `design-system.md` 第四节。
>
> **目录**：0 流程骨架 · 0.5 分支（含 0.5.1 嵌套子分叉）· 0.6 跨页连线 xlink · 1 导航栏 · 2 列表/入口 · 3 对话 · 4 统计图表（柱状图）·
> **5 扩展组件**（Tab Bar / 搜索栏 / 分段 / 卡片 / 宫格 / 横滑 / Banner / 媒体列表 /
> 商品卡 / CTA / 评分 / Feed / 表单 / 胶囊 / 时间线 / 空状态 / **地图 / 折线图 / 圆环图**）。

---

## 0. 流程骨架：页面 + 箭头怎么串

一个完整流程 = `.stage`（页面） + `.arrow`（流转） + `.stage` + `.arrow` + …
每个 `.stage` 顶部是带圈编号的 `.stage-label`（① ② ③ ④ ⑤ …）。

```html
<div class="stage">
  <div class="stage-label">① 页面名</div>
  <div class="screen">
    <!-- navbar + body 内容 -->
  </div>
</div>

<!-- 箭头：连接前后两页，tag 写清点击了什么 -->
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

---

## 0.5 分支流程（一个来源分出多条路线）

当**同一个页面分出多条不同路线**（如首页有两个入口、某页之后流程分叉）时，
不要把所有页面挤在一行——那会让人误以为是顺序流转。改用**分支布局**：来源页在左，
向右分出**多行**，每行一条子流程，连线由 JS 自动绘制（折线 + 标准箭头 + 标注）。

**结构**：`.flow` 加 `branched` 类 → 来源页 `.stage.origin` → 占位 `.fork` → `.branches`（内含多个 `.branch` 行）。
每个 `.branch` 用 `data-tag` 写该分支的点击动作。

```html
<div class="flow branched">

  <!-- 分叉来源页（连线从它右侧中点发出；可以是第①页，也可以是中间任意页） -->
  <div class="stage origin">
    <div class="stage-label">① 首页</div>
    <div class="screen"><!-- … --></div>
  </div>

  <!-- 占位列：连线由脚本自动画进 SVG overlay，这里留空即可 -->
  <div class="fork"></div>

  <!-- 多条分支，每行一条子流程；data-tag = 进入该分支的点击动作 -->
  <div class="branches">
    <div class="branch" data-tag="点击「大理」">
      <div class="stage"> … 第②页 … </div>
      <div class="arrow"><!-- 行内后续页用普通箭头 --></div>
      <div class="stage"> … 第③页 … </div>
    </div>
    <div class="branch" data-tag="点击「我的行程」">
      <div class="stage"> … 第⑤页 … </div>
    </div>
    <!-- 需要几行就加几个 .branch，没有数量限制 -->
  </div>
</div>
```

**要点**
- **任意行数**：`.branches` 里放 N 个 `.branch` 即可，连线自动适配。
- **中间分叉**：`.flow.branched` 不一定从第①页开始——把分叉点那页设为 `.stage.origin`，前面可照常接 `.stage`+`.arrow`。
- **嵌套**：`.branch` 内部可以再放一个完整的 `.flow.branched`（某条分支自己又分叉），连线会逐层自动绘制。
- **连线全自动**：`.fork` 留空、`data-tag` 写文案，就这两件事。折线、箭头头、标注位置都由脚本按真实坐标算，与行数/各行高度/分叉位置无关。
- 分支内每行的**后续页面**之间，仍用普通 `.arrow`（见上一节）。

### 0.5.1 嵌套子分叉（某条分支自己又分出多支）

当一条分支内部还要再分叉时（如「我的」页既能进订单、也能进消费统计），把该分支的**首页**设为
新的 `.stage.origin`，在 `.branch` 内放一个完整的 `.flow.branched`。连线逐层自动绘制。

```html
<div class="branch" data-tag="点击「我的」">
  <div class="flow branched">          <!-- 嵌套：我的页再分两支 -->
    <div class="stage origin">…⑫ 我的…</div>
    <div class="fork"></div>
    <div class="branches">
      <div class="branch" data-tag="点击「订单」">
        <div class="stage">…⑬ 订单列表…</div>
        <div class="arrow">…查看物流…</div>
        <div class="stage">…⑭ 订单详情…</div>
      </div>
      <div class="branch" data-tag="点击「消费统计」">
        <div class="stage">…⑮ 消费统计…</div>
      </div>
    </div>
  </div>
</div>
```

> 嵌套子树会占较高的垂直空间（子分支越多越高），属正常现象——内容尽量精简即可。

---

## 0.6 跨页连线 xlink（任意两页相连 / 多条汇聚到同一页）

普通箭头(`.arrow`)和分支(`.fork`)都是**顺序/发散**关系。当需要**非相邻的两页相连**，或
**多个入口指向同一个页面**（如「商品列表」和「搜索结果」都能进入同一个「商品详情」）时，
用 `xlink`：给目标页 `.screen` 加 `id`，再在 `.flow`/`.canvas` 同级放一个声明，脚本自动按真实坐标画正交折线+箭头+标注。

```html
<!-- 1) 给会被指向的页面 .screen 加 id -->
<div class="screen" id="pdp"> …④ 商品详情… </div>
…
<div class="screen" id="search-result"> …⑨ 搜索结果… </div>

<!-- 2) 在 .canvas 内（通常放在 .flow 之后）声明连线；可放多条指向同一 id（汇聚） -->
<div class="xlink" data-from="search-result" data-to="pdp"
     data-tag="点击商品 → 商品详情" data-side="auto" data-dash="1"></div>
```

**参数**
- `data-from` / `data-to`：起点、终点元素的 `id`（任意块都行，通常是 `.screen`）。
- `data-tag`：连线上的说明文字（可省）。
- `data-side`：出/入边，`auto`(默认，按相对位置自动) / `right` / `left` / `top` / `bottom`。
- `data-dash="1"`：画成虚线，用来区分「次要/跨区/返回」类跳转（实线留给主流程）。

**要点**
- **汇聚**：多条 `xlink` 的 `data-to` 指向同一个 `id` 即可，无需改目标页本身。
- 连线画在 `.canvas` 级 overlay 上，**可跨行、跨分支**；与 `.arrow`/`.fork` 共存、互不干扰。
- 截图正常（用内联 path，非 `<use>`）。窗口缩放自动重绘。
- 主流程仍优先用顺序 `.arrow` 和分支 `.fork`；`xlink` 用于它们表达不了的「跨越」与「汇聚」。

---

## 1. 导航栏 `.navbar`（三种）

`.nav-btn.ghost` 是透明占位，用来平衡左右、让标题保持居中。

```html
<!-- A. 首页型：两侧留空，仅标题 -->
<div class="navbar">
  <span class="nav-btn ghost"></span>
  <span class="title">功能中心</span>
  <span class="nav-btn ghost"></span>
</div>

<!-- B. 次级页：左返回 + 右功能键 -->
<div class="navbar">
  <span class="nav-btn"><i data-lucide="chevron-left"></i></span>
  <span class="title">景区伴游</span>
  <span class="nav-btn"><i data-lucide="settings"></i></span>
</div>

<!-- C. 详情页：左返回 + 右占位 -->
<div class="navbar">
  <span class="nav-btn"><i data-lucide="chevron-left"></i></span>
  <span class="title">设置</span>
  <span class="nav-btn ghost"></span>
</div>
```

---

## 2. 列表页 / 功能入口

`.body` 包裹内容；`.section-cap` 是分组小标题；`.list-item` 是行，可带 chevron / 开关。

```html
<div class="body">
  <div class="section-cap">智能助手</div>

  <!-- 带跳转箭头的入口行 -->
  <div class="list-item">
    <div class="ic"><i data-lucide="map-pin"></i></div>
    <div class="txt">
      <div class="t1">景区伴游</div>
      <div class="t2">实时讲解 · 路线推荐</div>
    </div>
    <div class="chev"><i data-lucide="chevron-right"></i></div>
  </div>

  <div class="list-item">
    <div class="ic"><i data-lucide="music"></i></div>
    <div class="txt">
      <div class="t1">歌剧老师</div>
      <div class="t2">声乐陪练与曲目讲解</div>
    </div>
    <div class="chev"><i data-lucide="chevron-right"></i></div>
  </div>
</div>
```

### 2b. 开关行（设置页用）

```html
<div class="list-item">
  <div class="ic"><i data-lucide="volume-2"></i></div>
  <div class="txt">
    <div class="t1">语音播报</div>
    <div class="t2">以语音方式朗读讲解内容</div>
  </div>
  <!-- 开启态加 .on -->
  <div class="switch on"><div class="knob"></div></div>
</div>
```

### 2c. 危险/账户操作行

```html
<div class="list-item danger">
  <div class="ic"><i data-lucide="unlink"></i></div>
  <div class="txt">
    <div class="t1">解除绑定</div>
    <div class="t2">断开与当前设备的连接</div>
  </div>
  <div class="chev"><i data-lucide="chevron-right"></i></div>
</div>
```

---

## 3. 对话页

由 `.chat-hero`（头部） + `.perm-tip`（可选权限条） + `.chat-stream`（气泡流） + `.chat-input`（输入栏）组成。
注意：导航栏之后这四块是兄弟节点，**不要**再包 `.body`（聊天页要自己控制 flex 伸缩）。

```html
<div class="chat-hero">
  <div class="avatar"><i data-lucide="map-pin"></i></div>
  <div class="name">景区伴游</div>
  <div class="sub">在线 · 正在为你讲解</div>
</div>

<!-- 可选：权限/提示条 -->
<div class="perm-tip">
  <div class="pic"><i data-lucide="locate-fixed"></i></div>
  <div class="ptxt">
    <div class="pt1">需要开启位置权限</div>
    <div class="pt2">开启后才能根据你的位置实时讲解与推荐路线</div>
  </div>
  <div class="pbtn">去开启</div>
</div>

<div class="chat-stream">
  <!-- 对方气泡 -->
  <div class="bubble in">
    <div class="who">景区伴游</div>
    你好，我是你的景区伴游。现在位于「东门广场」，需要我推荐一条游览路线吗？
  </div>
  <!-- 自己气泡 -->
  <div class="bubble out">
    <div class="who">我</div>
    帮我规划两小时的精华路线
  </div>
</div>

<div class="chat-input">
  <div class="field">输入你的问题…</div>
  <div class="send"><i data-lucide="arrow-up"></i></div>
</div>
```

---

## 4. 统计 / 图表页

由三块组成，外层用 `.stat-scroll` 包裹：顶部数字摘要 → 进度条 → 柱状图。

```html
<div class="stat-scroll">
  <!-- 4a. 顶部三栏数字摘要（栏间自动竖线分隔） -->
  <div class="stat-summary">
    <div class="stat">
      <div class="num">6</div>
      <div class="lbl">游览景点</div>
    </div>
    <div class="stat">
      <div class="num">23</div>
      <div class="lbl">讲解条目</div>
    </div>
    <!-- 数字带单位用 <small> -->
    <div class="stat">
      <div class="num">1<small>h</small>48<small>m</small></div>
      <div class="lbl">陪游时长</div>
    </div>
  </div>

  <!-- 4b. 进度条 -->
  <div class="stat-progress">
    <div class="pg-head">
      <span class="pg-title">景区探索度</span>
      <span class="pg-meta">6 / 10 个景点</span>
    </div>
    <div class="pg-track"><div class="pg-fill" style="width:60%"></div></div>
  </div>

  <!-- 4c. 柱状图：每根柱 height% 表示相对高度；最高/重点柱加 .hot 用深色 -->
  <div class="chart-block">
    <div class="ch-title">各景点讲解条目</div>
    <div class="bars">
      <div class="bar-col">
        <div class="bar-track"><span class="bar-cap">3</span><div class="bar" style="height:30%"></div></div>
        <div class="bar-lbl">东门</div>
      </div>
      <div class="bar-col">
        <div class="bar-track"><span class="bar-cap">4</span><div class="bar" style="height:40%"></div></div>
        <div class="bar-lbl">听涛亭</div>
      </div>
      <div class="bar-col hot">
        <div class="bar-track"><span class="bar-cap">7</span><div class="bar" style="height:100%"></div></div>
        <div class="bar-lbl">古寺</div>
      </div>
      <div class="bar-col">
        <div class="bar-track"><span class="bar-cap">5</span><div class="bar" style="height:58%"></div></div>
        <div class="bar-lbl">观景台</div>
      </div>
      <div class="bar-col">
        <div class="bar-track"><span class="bar-cap">4</span><div class="bar" style="height:40%"></div></div>
        <div class="bar-lbl">碑林</div>
      </div>
    </div>
  </div>
</div>
```

**柱状图小贴士**：`height%` 是相对柱区(200px高)的百分比；把最大值那根设成 `100%`，其余按比例换算
（例如最大 7 条 → 5 条 ≈ 71%，但视觉上可微调到舒服为止）。重点/最高柱加 `.hot` 即变深色。

---

## 5. 扩展组件（主流 App 高频形态）

> 这一批用来撑起电商 / 社交 / 内容 / 工具类 App。和上面一样：**只复制、改文案/图标，不写样式**。
> 数值含义见 `design-system.md` 第六节。贴底组件（Tab Bar / CTA 栏）放在 `.screen` 的**最后**。

### 5.1 底部 Tab Bar `.tabbar`

放在 `.screen` 末尾（`.body` 之后）。当前页对应的 `.tab` 加 `.active`，且**全页只有一个 active**。

```html
<div class="tabbar">
  <div class="tab active">
    <i data-lucide="home"></i>
    <div class="tlabel">首页</div>
  </div>
  <div class="tab">
    <i data-lucide="compass"></i>
    <div class="tlabel">发现</div>
  </div>
  <div class="tab">
    <i data-lucide="shopping-bag"></i>
    <div class="tlabel">购物车</div>
  </div>
  <div class="tab">
    <i data-lucide="user"></i>
    <div class="tlabel">我的</div>
  </div>
</div>
```

### 5.2 搜索栏 `.searchbar`

通常紧跟在 navbar 之后，或直接做首页顶部。

```html
<div class="searchbar">
  <i data-lucide="search"></i>
  <span class="sph">搜索商品 / 店铺</span>
</div>
```

### 5.3 分段控制器 `.segment`

一组互斥标签页切换，选中段加 `.active`。

```html
<div class="segment">
  <div class="seg active">推荐</div>
  <div class="seg">最新</div>
  <div class="seg">附近</div>
</div>
```

### 5.4 通用卡片 `.card`

白底圆角容器，装任何内容。头部 `.card-hd` 可选（标题 + 「更多」）。
卡片内可直接放 `.list-item`（分隔线会自动通栏）。

```html
<div class="card">
  <div class="card-hd">
    <span class="ct">我的订单</span>
    <span class="cmore">全部<i data-lucide="chevron-right"></i></span>
  </div>
  <div class="card-bd">
    卡片正文内容（任意：文字、宫格、列表…）。
  </div>
</div>
```

### 5.5 功能宫格 `.grid`（九宫格入口）

一行 4 列，自动换行成多行。每格 = 图标底 + 文字标签。

```html
<div class="grid">
  <div class="grid-item">
    <div class="gic"><i data-lucide="ticket"></i></div>
    <div class="glabel">优惠券</div>
  </div>
  <div class="grid-item">
    <div class="gic"><i data-lucide="truck"></i></div>
    <div class="glabel">物流</div>
  </div>
  <div class="grid-item">
    <div class="gic"><i data-lucide="wallet"></i></div>
    <div class="glabel">钱包</div>
  </div>
  <div class="grid-item">
    <div class="gic"><i data-lucide="gift"></i></div>
    <div class="glabel">积分</div>
  </div>
  <!-- 再加 4 个即第二行 -->
</div>
```

### 5.6 横向滚动卡片 `.hscroll`

一排可横滑的小卡，每张 `.hcard` = 缩略图占位 + 标题 + 副文。

```html
<div class="hscroll">
  <div class="hcard">
    <div class="thumb" style="height:90px"><i data-lucide="image"></i></div>
    <div class="htitle">春日新品</div>
    <div class="hsub">限时 8 折</div>
  </div>
  <div class="hcard">
    <div class="thumb" style="height:90px"><i data-lucide="image"></i></div>
    <div class="htitle">人气榜单</div>
    <div class="hsub">每日更新</div>
  </div>
  <div class="hcard">
    <div class="thumb" style="height:90px"><i data-lucide="image"></i></div>
    <div class="htitle">附近好店</div>
    <div class="hsub">3km 内</div>
  </div>
</div>
```

### 5.7 Banner / 轮播占位 `.banner`

首页大图位。底部小圆点指示当前页，激活点加 `.on`。

```html
<div class="banner">
  <i data-lucide="image"></i>
  <div class="dots"><i class="on"></i><i></i><i></i></div>
</div>
```

### 5.8 媒体列表项 `.media-item`（带缩略图的列表）

适合资讯 / 视频 / 笔记列表。左缩略图 + 右标题/副文/元信息。

```html
<div class="media-item">
  <div class="thumb"><i data-lucide="image"></i></div>
  <div class="mtxt">
    <div class="m1">如何用线框图快速对齐需求</div>
    <div class="m2">3 分钟读懂产品原型的表达力</div>
    <div class="m3">设计周刊 · 1.2万阅读</div>
  </div>
</div>
```

### 5.9 商品卡网格 `.product-grid`（电商两列）

两列等宽。大图占位 + 商品名 + 价格 + 加购按钮。

```html
<div class="product-grid">
  <div class="product">
    <div class="thumb"><i data-lucide="image"></i></div>
    <div class="pinfo">
      <div class="pname">轻薄羊毛针织衫 多色可选</div>
      <div class="prow">
        <span class="price"><small>¥</small>199</span>
        <span class="add"><i data-lucide="plus"></i></span>
      </div>
    </div>
  </div>
  <div class="product">
    <div class="thumb"><i data-lucide="image"></i></div>
    <div class="pinfo">
      <div class="pname">纯棉基础款 T 恤</div>
      <div class="prow">
        <span class="price"><small>¥</small>89</span>
        <span class="add"><i data-lucide="plus"></i></span>
      </div>
    </div>
  </div>
</div>
```

### 5.10 底部 CTA 操作栏 `.cta-bar`

放在 `.screen` 末尾（详情页、表单页常用）。一到两个 `.btn`。

```html
<div class="cta-bar">
  <div class="btn ghost"><i data-lucide="shopping-cart"></i>加入购物车</div>
  <div class="btn primary">立即购买</div>
</div>
```

按钮也可单独用在卡片内 / 表单里：`.btn.primary`（实心）/`.btn.ghost`（浅底）/`.btn.outline`（描边），
行内小按钮加 `.sm`。

### 5.11 星级评分 `.rating`

满星实心，空星加 `.off`。可附数值。

```html
<div class="rating">
  <i data-lucide="star"></i>
  <i data-lucide="star"></i>
  <i data-lucide="star"></i>
  <i data-lucide="star"></i>
  <i data-lucide="star" class="off"></i>
  <span class="rnum">4.0 · 1.2万评价</span>
</div>
```

### 5.12 Feed 动态卡 `.feed-item`

社交流：头部（头像 + 名字 + 时间）+ 正文 + 配图 + 操作行。

```html
<div class="feed-item">
  <div class="fhead">
    <div class="avatar-sm"><i data-lucide="user"></i></div>
    <div>
      <div class="fname">林小一</div>
      <div class="ftime">2 小时前</div>
    </div>
  </div>
  <div class="ftext">用 easy-wireframe 半小时拼出了一版完整流程，标注完直接发给开发，太省事了。</div>
  <div class="thumb fmedia"><i data-lucide="image"></i></div>
  <div class="facts">
    <span class="fact"><i data-lucide="thumbs-up"></i>238</span>
    <span class="fact"><i data-lucide="message-circle"></i>46</span>
    <span class="fact"><i data-lucide="share-2"></i>分享</span>
  </div>
</div>
```

评论行（可接在动态下方或评论页）：

```html
<div class="comment">
  <div class="avatar-sm"><i data-lucide="user"></i></div>
  <div class="ctext">
    <div class="cname">阿May</div>
    <div class="cbody">这个标注导出功能太实用了，求教程！</div>
  </div>
</div>
```

### 5.13 表单输入 `.form-field`

静态展示已填 / 占位两态。多行用 `.area`，占位文字用 `.ph`。

```html
<div class="form-field">
  <div class="flabel">收货人</div>
  <div class="finput">张三</div>
</div>
<div class="form-field">
  <div class="flabel">手机号</div>
  <div class="finput ph">请输入手机号</div>
</div>
<div class="form-field">
  <div class="flabel">备注</div>
  <div class="finput area ph">选填：对本单的特殊说明…</div>
</div>
```

### 5.14 筛选胶囊 `.chips`

一排可选标签，选中加 `.active`。

```html
<div class="chips">
  <span class="chip active">全部</span>
  <span class="chip">上衣</span>
  <span class="chip">裤装</span>
  <span class="chip">鞋包</span>
  <span class="chip">配饰</span>
</div>
```

### 5.15 时间线 / 步骤 `.timeline`

订单进度、流程节点。已完成节点加 `.done`（圆点变实心）。

```html
<div class="timeline">
  <div class="tl-item done">
    <div class="tl-dot"></div>
    <div class="tl-txt">
      <div class="tl-1">已下单</div>
      <div class="tl-2">今天 09:24</div>
    </div>
  </div>
  <div class="tl-item done">
    <div class="tl-dot"></div>
    <div class="tl-txt">
      <div class="tl-1">已发货</div>
      <div class="tl-2">今天 14:02 · 顺丰速运</div>
    </div>
  </div>
  <div class="tl-item">
    <div class="tl-dot"></div>
    <div class="tl-txt">
      <div class="tl-1">运输中</div>
      <div class="tl-2">预计明天送达</div>
    </div>
  </div>
  <div class="tl-item">
    <div class="tl-dot"></div>
    <div class="tl-txt">
      <div class="tl-1">已签收</div>
      <div class="tl-2">—</div>
    </div>
  </div>
</div>
```

### 5.16 空状态 `.empty`

列表为空 / 无结果时占满 `.body`。

```html
<div class="body">
  <div class="empty">
    <div class="eic"><i data-lucide="shopping-bag"></i></div>
    <div class="e1">购物车还是空的</div>
    <div class="e2">去逛逛，挑点喜欢的吧</div>
  </div>
</div>
```

### 5.17 地图 `.map`

线框风地图：纯 CSS 网格底 + 道路 + 居中定位针，底部可浮一张信息卡。无图片依赖。
`.road` 默认横向斜线，加 `.v` 变纵向，可放多条。`.pin` 自动居中。

```html
<div class="map">
  <div class="road"></div>
  <div class="road v"></div>
  <div class="pin"></div>
  <!-- 可选：底部浮动信息卡 -->
  <div class="map-card">
    <div class="mc-ic"><i data-lucide="store"></i></div>
    <div class="mc-tx">
      <div class="mc-1">悠集生活馆 · 文一路店</div>
      <div class="mc-2">营业中 · 距你 1.2km · 09:00–22:00</div>
    </div>
    <div class="chev"><i data-lucide="chevron-right"></i></div>
  </div>
</div>
```

### 5.18 折线图 `.linechart`（内联 SVG）

近 N 期趋势。`viewBox` 固定 `0 0 300 160`：x 从 10→290 横向均分，y 越小越高（顶 30、底 130 是舒服区间）。
改数据＝改 `.ln-path`/`.ln-area`/各 `.ln-dot` 的坐标，并同步底部 `.ln-labels`。
**面积** `.ln-area` 是折线再闭合到底边（`L290 160 L10 160 Z`）。截图可正常导出。

```html
<div class="chart-block">
  <div class="ch-title">近 6 个月支出趋势</div>
  <div class="linechart">
    <svg viewBox="0 0 300 160" xmlns="http://www.w3.org/2000/svg">
      <line class="ln-grid" x1="0" y1="40" x2="300" y2="40"/>
      <line class="ln-grid" x1="0" y1="80" x2="300" y2="80"/>
      <line class="ln-grid" x1="0" y1="120" x2="300" y2="120"/>
      <path class="ln-area" d="M10 110 L68 90 L126 100 L184 60 L242 70 L290 30 L290 160 L10 160 Z"/>
      <path class="ln-path" d="M10 110 L68 90 L126 100 L184 60 L242 70 L290 30"/>
      <circle class="ln-dot" cx="10" cy="110" r="3"/>
      <circle class="ln-dot" cx="68" cy="90" r="3"/>
      <circle class="ln-dot" cx="126" cy="100" r="3"/>
      <circle class="ln-dot" cx="184" cy="60" r="3"/>
      <circle class="ln-dot" cx="242" cy="70" r="3"/>
      <circle class="ln-dot" cx="290" cy="30" r="3"/>
    </svg>
    <div class="ln-labels">
      <span>1月</span><span>2月</span><span>3月</span><span>4月</span><span>5月</span><span>6月</span>
    </div>
  </div>
</div>
```

### 5.19 圆环 / 占比图 `.donut`（内联 SVG）

环形占比 + 中心数字 + 右侧图例。半径 50、周长 ≈ **314**。
每段一个 `<circle class="d-seg">`：`stroke-dasharray="本段长 314"`，`stroke-dashoffset` = **负的“前面所有段长之和”**。
段长 = 占比 × 314（如 55% ≈ 172、30% ≈ 94）。颜色：首段 `--ink`、次段 `.soft`(`--line`)、余下留给浅色轨道。

```html
<div class="chart-block">
  <div class="ch-title">消费品类占比</div>
  <div class="donut-wrap">
    <div class="donut">
      <svg viewBox="0 0 120 120" xmlns="http://www.w3.org/2000/svg">
        <circle class="d-track" cx="60" cy="60" r="50"/>
        <!-- 服饰 55% -->
        <circle class="d-seg" cx="60" cy="60" r="50" stroke-dasharray="172 314" stroke-dashoffset="0"/>
        <!-- 居家 30%，接在 55% 之后 -->
        <circle class="d-seg soft" cx="60" cy="60" r="50" stroke-dasharray="94 314" stroke-dashoffset="-172"/>
      </svg>
      <div class="d-center">
        <div class="d-num">¥2.4k</div>
        <div class="d-lbl">本月</div>
      </div>
    </div>
    <div class="donut-legend">
      <div class="lg-row"><span class="lg-dot"></span>服饰穿搭<span class="lg-val">55%</span></div>
      <div class="lg-row"><span class="lg-dot soft"></span>居家生活<span class="lg-val">30%</span></div>
      <div class="lg-row"><span class="lg-dot faint"></span>其他<span class="lg-val">15%</span></div>
    </div>
  </div>
</div>
```

> 既有的**柱状图**见第 4 节（`.bars`）。折线/柱状/圆环三种图表配齐，按数据形态选用。

---

## 拼装清单（自检）

- [ ] 每个 `.stage` 有唯一编号 `.stage-label`（① ② ③ …）
- [ ] 相邻两页之间都有一个 `.arrow`，`.tag` 写清「点击了什么」
- [ ] 首页导航用 ghost 占位居中；次级页左侧是 `chevron-left`
- [ ] 图标名都来自 Lucide（拼错会显示空白）
- [ ] 没有改动 `<style>` 里的任何数值
- [ ] 用了扩展组件时：贴底的 `.tabbar` / `.cta-bar` 放在 `.screen` **最后**，且二者不同时出现
- [ ] `.tabbar` 全页只有一个 `.tab.active`；`.segment`/`.chips` 的选中态也唯一
- [ ] 商品卡按两列摆放（偶数个最齐）；宫格按 4 的倍数最齐
- [ ] 时间线已完成节点加 `.done`；空状态 `.empty` 直接放进 `.body`
- [ ] 折线图改了数据后，`.ln-path`/`.ln-area`/各 `.ln-dot` 坐标与底部 `.ln-labels` 数量一致
- [ ] 圆环图各段 `stroke-dashoffset` = 负的前段累计长；段长之和 ≤ 314（周长）
