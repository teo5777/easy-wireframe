# 组件积木库

> 复制下面的片段拼进 `template.html` 的 `.flow` 占位处即可。所有片段都依赖模板里已有的 CSS——
> 不要额外写样式。只替换文案、图标名(`data-lucide`)、数值。
> 图标名清单见 `design-system.md` 第四节。
>
> **目录**：0 页面摆放（网格坐标）· 0.5 页面连接（统一 .link 边）· 1 导航栏 · 2 列表/入口 · 3 对话 · 4 统计图表（柱状图）·
> **5 扩展组件**（Tab Bar / 搜索栏 / 分段 / 卡片 / 宫格 / 横滑 / Banner / 媒体列表 /
> 商品卡 / CTA / 评分 / Feed / 表单 / 胶囊 / 时间线 / 空状态 / **地图 / 折线图 / 圆环图** /
> **浮层（弹窗 / 半屏弹层 / 操作菜单 / Toast）**）。

---

## 0. 页面摆放：网格坐标 `data-cell`

页面用 **CSS Grid 网格坐标**摆放，不再靠 DOM 顺序。每个页面是一个 `.stage`，
顶部带圈编号 `.stage-label`（① ② ③ …），用 `data-cell="行,列"` 声明它在网格里的位置，
内含一个**带唯一 `id`** 的 `.screen`（连线靠 id 找两端，所以 id 是必须的）。

```html
<div class="stage" data-cell="1,1">
  <div class="stage-label">① 首页</div>
  <div class="screen" id="home">
    <!-- navbar + body 内容 -->
  </div>
</div>

<div class="stage" data-cell="1,2">
  <div class="stage-label">② 列表</div>
  <div class="screen" id="list">
    <!-- … -->
  </div>
</div>
```

**坐标怎么排**
- `data-cell="行,列"`：行从 1 起、自上而下；列从 1 起、自左而右。行 1 一般放主流程，
  支线（搜索 / 发现 / 我的 …）各占一行（行 2、行 3 …）。
- **主流程同一行从左往右铺**：`1,1` → `1,2` → `1,3` …，它们之间会自动画一条**水平直线**（线上压说明文字、无箭头）。
- **新增页面 = 追加一个 `.stage` + 给一个空格坐标，其它页一律不用动**。这是网格相对旧布局的最大好处：
  加页不会再让整张图重排错乱。
- **漏写 `data-cell` 不会报错**：脚本启动时会把没坐标的页面顺序补到空格里（退化成一行横排、互不重叠）。
  但**能写就写**——精确坐标才好控制版面。
- **可拖拽微调**：生成的 HTML 在浏览器里打开后，点工具栏「布局模式」即可直接拖动页面卡片换位置
  （拖到已占用格 = 两页交换），满意后点「导出布局」把坐标贴回对话框，我据此固化 `data-cell`。

---

## 0.5 页面连接：统一的 `.link` 边

所有页面间的跳转都用**同一种声明** `.link`：给两端 `.screen` 各一个 `id`，再放一个 `.link` 元素。
脚本(`drawLinks`)按真实坐标自动画线——**只画线、不画箭头**，方向靠左→右 / 上→下的摆位和线上文字表达：

- **同一行相邻两页**（行相同、列差 1、目标在右）→ 自动画一条**水平直线**，`data-tag` 文字压在线中点上。
- **其余所有情况**（跨行、跨列、隔页、多入口汇聚、返回）→ 自动走**障碍物正交布线器**：把每个页面当
  障碍矩形，用最短路（Dijkstra + 拐弯惩罚）在页面空隙里找一条**不穿任何页面、拐弯最少**的正交折线，
  且与沿途页面留出足够间距、不贴边。

```html
<!-- 1) 两端 .screen 都要有 id（见上一节） -->
<div class="screen" id="home"> … </div>
<div class="screen" id="pdp"> …商品详情… </div>
<div class="screen" id="search-result"> …搜索结果… </div>

<!-- 2) 在 .flow 内（通常放在所有 .stage 之后）声明连线 -->
<!-- 同行相邻：自动水平直线 + 线上文字 -->
<div class="link" data-from="home" data-to="list" data-tag="点击「进入」"></div>
<!-- 跨页 / 汇聚：自动绕页折线；多条指向同一 id 即汇聚 -->
<div class="link" data-from="search-result" data-to="pdp"
     data-tag="点击商品 → 详情" data-side="auto" data-dash="1"></div>
```

**参数**
- `data-from` / `data-to`：起点、终点 `.screen` 的 `id`。
- `data-tag`：连线上的说明文字，写清「点击了什么」（可省）。
- `data-side`：约束**目标页进入边**，`auto`(默认) / `left` / `right` / `top` / `bottom`。一般留 `auto`。
- `data-dash="1"`：画成虚线，用来区分「次要 / 跨区 / 返回」类跳转（实线留给主流程）。

**自动布线 · 永不压页**
- **任意布局都不压页**：连线只走页面之间的空隙，绝不穿过页面卡片。
- **方向清晰、弯折最少**：拐弯有惩罚，优先走直，被挡住才拐，总拐弯数最小。
- **汇聚不打架**：多条指向同一页的线自然选不同端口/通道进入，互不重叠。
- **就近进入**：`auto` 下由算法按实际几何选最优进入边，无需手填 `data-side`。

**要点**
- 每条跳转一个 `.link`；表达「一页分出多条路线」就放多条 `data-from` 相同的 `.link`。
- 连线画在 `.canvas` 级 overlay，可跨行、跨列；截图正常、缩放/拖拽后自动重绘。
- **全部只画线、不画箭头**；`data-tag` 文字带页面底色压在线上，列间距已留足让线比文字更长、不会被文字盖满。
- 旧的 `.xlink` 仍被识别（向后兼容），新文件统一用 `.link`。

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

放在 `.screen` 末尾（详情页、表单页常用）。**横排**适合两个并列、地位相当的按钮；
**纵向**适合按钮各占一行、或「一个主按钮 + 一行弱化小字」的结构。

```html
<!-- A. 横排两钮（地位相当、并列） -->
<div class="cta-bar">
  <div class="btn ghost"><i data-lucide="shopping-cart"></i>加入购物车</div>
  <div class="btn primary">立即购买</div>
</div>

<!-- B. 纵向两钮：各占一行、通栏等宽（主次分明，或并排会太挤时） -->
<!--    给 .cta-bar 加 .stack 即变纵向；按钮顺序按重要性从上到下 -->
<div class="cta-bar stack">
  <div class="btn primary">立即购买</div>
  <div class="btn ghost">加入购物车</div>
</div>

<!-- C. 一个主按钮 + 一行弱化小字（开通/订阅/登录页常见） -->
<!--    .cta-hint 是说明/次要操作小字，里面可点的文字用 .link 提为墨色 -->
<div class="cta-bar stack">
  <div class="btn primary">确认开通会员</div>
  <div class="cta-hint">7 天后将自动续费 ¥25/月 · <span class="link">暂不开通</span></div>
</div>
```

**为什么要用 `.stack` 而不是塞两个 `.cta-bar`**：底部按钮被压成矮条的根因，是按钮被父级 flex
当成可收缩的项压扁。`.cta-bar.stack` 用列方向 + `align-items:stretch`，配合 `.btn` 自带的
`min-height:48`，保证**每个按钮都是正常 48 高、各占整行**，绝不会变矮。小字 `.cta-hint` 也在这套
纵向节奏里与按钮匀称留白。需要「按钮上方放小字」就把 `.cta-hint` 写在 `.btn` 前面。

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

### 5.20 浮层 / 覆盖层（弹窗 / 半屏弹层 / 操作菜单 / Toast）

表达「这一页弹出了一个浮层」。**关键：浮层放进它所属的 `.screen` 内、作为内容之后的子节点**——
它相对这一页绝对定位、被手机屏圆角裁切，绝不会盖住整张流程图。一页一般只放一个浮层。

```html
<!-- A. 居中对话框（确认 / 删除 / 提示）。放在某页 .screen 内的最后。 -->
<div class="sheet-mask center">
  <div class="dialog">
    <!-- 可选顶部图标圈；危险操作加 .danger 变砖红 -->
    <div class="dlg-ic danger"><i data-lucide="trash-2"></i></div>
    <div class="dlg-title">删除这张草稿？</div>
    <div class="dlg-text">删除后无法恢复，确定要继续吗？</div>
    <!-- 按钮默认上下堆叠；要左右并排给 .dlg-actions 加 .row -->
    <div class="dlg-actions">
      <div class="btn danger">删除</div>
      <div class="btn ghost">取消</div>
    </div>
  </div>
</div>

<!-- B. 底部半屏弹层（选规格 / 筛选 / 详情）。内部可放任意已有组件。 -->
<div class="sheet-mask bottom">
  <div class="sheet">
    <div class="sheet-grip"></div>
    <div class="sheet-hd">
      <span class="sh-title">选择规格</span>
      <span class="sh-close"><i data-lucide="x"></i></span>
    </div>
    <div class="sheet-bd">
      <div class="form-field"><div class="flabel">颜色</div></div>
      <div class="chips"><span class="chip active">米白</span><span class="chip">雾蓝</span><span class="chip">炭灰</span></div>
      <div class="form-field"><div class="flabel">尺码</div></div>
      <div class="chips"><span class="chip">S</span><span class="chip active">M</span><span class="chip">L</span></div>
    </div>
    <div class="sheet-foot"><div class="btn primary">确定</div></div>
  </div>
</div>

<!-- C. iOS 操作菜单（成组操作项 + 独立取消组） -->
<div class="sheet-mask bottom">
  <div class="action-sheet">
    <div class="as-group">
      <div class="as-cap">这条动态将对所有人可见</div>
      <div class="as-item strong">分享到动态</div>
      <div class="as-item">保存到相册</div>
      <div class="as-item danger">举报</div>
    </div>
    <div class="as-cancel">取消</div>
  </div>
</div>

<!-- D. Toast 轻提示（无遮罩，屏内浮一条短反馈）。默认居中偏下，加 .top 浮到顶部。 -->
<div class="toast"><i data-lucide="check"></i>已加入购物车</div>
```

**要点**
- `.sheet-mask` 是半透明遮罩底：`.center` 居中托 `.dialog`，`.bottom` 贴底托 `.sheet` / `.action-sheet`。
- `.dialog` 按钮、`.sheet-foot` 里的按钮都直接用既有 `.btn`（含本次新增的 `.btn.danger` 危险态）。
- `.sheet-bd` 里随意嵌 `.form-field` / `.chips` / `.list-item` / `.product-grid` 等已有组件，不必新写。
- **Toast 不需要遮罩**，直接把 `.toast` 放进 `.screen` 即可；其余三种都要外层 `.sheet-mask`。
- 想表达「点击某项弹出此浮层」，给**带浮层的那一页**单独画一帧，用 `.link` 从触发页指过来即可。

---

## 拼装清单（自检）

- [ ] 每个 `.stage` 有唯一编号 `.stage-label`（① ② ③ …）和 `data-cell="行,列"` 坐标
- [ ] 每个 `.screen` 有**唯一 `id`**（连线靠 id 连两端）
- [ ] 页面间每个跳转都有一个 `.link`，`data-tag` 写清「点击了什么」；同行相邻自动走水平直线、其余自动绕页（只画线不画箭头）
- [ ] 主流程铺在同一行（`1,1`→`1,2`→…）；支线各占一行，避免把支线挤进主流程行
- [ ] 首页导航用 ghost 占位居中；次级页左侧是 `chevron-left`
- [ ] 图标名都来自 Lucide（拼错会显示空白）
- [ ] 没有改动 `<style>` 里的任何数值
- [ ] 用了扩展组件时：贴底的 `.tabbar` / `.cta-bar` 放在 `.screen` **最后**，且二者不同时出现
- [ ] 底部按钮各占一行 / 主按钮配弱化小字时，用 `.cta-bar.stack`（+ `.cta-hint`），不靠堆多个 `.cta-bar`——按钮高度才不会被压矮
- [ ] 跨页 `.link` 优先用 `data-side="auto"`：中间夹页时会自动绕场外，不要手动改 side 去硬绕；返回/次要跳转加 `data-dash="1"`
- [ ] 浮层（`.dialog`/`.sheet`/`.action-sheet`/`.toast`）放进所属 `.screen` 内、作为内容之后的子节点；除 Toast 外都套一层 `.sheet-mask`，一页一般只放一个浮层
- [ ] `.tabbar` 全页只有一个 `.tab.active`；`.segment`/`.chips` 的选中态也唯一
- [ ] 商品卡按两列摆放（偶数个最齐）；宫格按 4 的倍数最齐
- [ ] 时间线已完成节点加 `.done`；空状态 `.empty` 直接放进 `.body`
- [ ] 折线图改了数据后，`.ln-path`/`.ln-area`/各 `.ln-dot` 坐标与底部 `.ln-labels` 数量一致
- [ ] 圆环图各段 `stroke-dashoffset` = 负的前段累计长；段长之和 ≤ 314（周长）
