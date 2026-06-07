# 组件积木库

> 复制下面的片段拼进 `template.html` 的 `.flow` 占位处即可。所有片段都依赖模板里已有的 CSS——
> 不要额外写样式。只替换文案、图标名(`data-lucide`)、数值。
> 图标名清单见 `design-system.md` 第四节。

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

## 拼装清单（自检）

- [ ] 每个 `.stage` 有唯一编号 `.stage-label`（① ② ③ …）
- [ ] 相邻两页之间都有一个 `.arrow`，`.tag` 写清「点击了什么」
- [ ] 首页导航用 ghost 占位居中；次级页左侧是 `chevron-left`
- [ ] 图标名都来自 Lucide（拼错会显示空白）
- [ ] 没有改动 `<style>` 里的任何数值
