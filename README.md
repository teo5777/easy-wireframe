# Easy Wireframe

一个生成**移动端 App 产品原型 / 页面流程线框图**的 Skill：把"想做什么 App、有哪几页、怎么点击流转"变成一张协调、专业的线框流程图——多个 iPhone 尺寸(375×812)页面横向平铺，箭头标注页面间跳转。产出是**零依赖的单文件 HTML**。

> 通用 Skill，遵循 `SKILL.md` 约定，可被 Claude Code、Codex 等支持 skill 的编码 Agent 使用。

## 风格定位

- **移动端线框**：iPhone 逻辑尺寸 375×812 的页面卡片，无手机壳、无状态栏，干净。
- **极简黑白灰**：一套冷白灰 + 近黑文字，无彩色。
- **iOS / Apple HIG 标准**：尺寸、字号、图标都按设计系统锁定。
- **平铺 + 箭头**：所有页面横排，两页之间用箭头说明"点击了什么 → 到哪一页"。

## 文件说明

| 文件 | 内容 |
|---|---|
| `SKILL.md` | Skill 清单与工作流（生成原型的权威流程，入口文件） |
| `design-system.md` | 颜色 / 字号(HIG 映射) / 尺寸 / 图标 / 箭头 的权威数值表 |
| `template.html` | 骨架文件：已含全部 CSS + 标注 JS + 截图 JS，复制后填充 `.flow` |
| `components.md` | 可复制的 HTML 积木：导航栏 / 列表 / 对话 / 统计 / 箭头 / 分支布局 |
| `example.html` | 黄金样例（轻记·习惯打卡，4 页），演示列表 / 详情 / 统计 / 分支布局 |

---

## 安装

### Claude Code 手动安装

把 skill 文件复制到 Claude Code 的 skills 目录：

```bash
# 创建 skill 目录
mkdir -p ~/.claude/skills/easy-wireframe

# 复制全部用户面文件
cp SKILL.md design-system.md components.md template.html example.html ~/.claude/skills/easy-wireframe/
```

或者直接克隆到 skills 目录：

```bash
git clone https://github.com/teo5777/easy-wireframe.git ~/.claude/skills/easy-wireframe
```

之后在 Claude Code 里输入 `/easy-wireframe` 即可使用。独立 skill 不带命名空间前缀。

### Codex 手动安装

Codex 采用相同的 `SKILL.md` 约定，目录结构一致。复制到 Codex 的 skills 目录即可（项目级放 `.codex/skills/`，全局安装路径以 Codex 文档为准）：

```bash
# 项目级：放到当前项目的 .codex/skills 下
mkdir -p .codex/skills/easy-wireframe
cp SKILL.md design-system.md components.md template.html example.html .codex/skills/easy-wireframe/
```

或克隆：

```bash
git clone https://github.com/teo5777/easy-wireframe.git .codex/skills/easy-wireframe
```

### 其他编码 Agent

Kimi Code、OpenCode、Gemini CLI 等本地编码助手可以使用同一套核心 skill。最简单的方式是把本仓库链接发给 Agent，让它使用 Easy Wireframe skill：

```
https://github.com/teo5777/easy-wireframe
```

如果 Agent 能读取 GitHub 仓库或浏览文件，它应从 `SKILL.md` 入手，并只按需加载其中引用的支持文件：

- `design-system.md`
- `template.html`
- `components.md`
- `example.html`

部分 Agent 若有文件系统访问权限和已知的本地 skills 目录，也能帮你完成安装；否则它也可以在当前会话里直接遵循 `SKILL.md` 工作。

---

## 使用方式

安装后，描述你的 App 需求（叫什么、要哪几页、怎么流转），即可生成单文件 HTML 原型。

生成的 HTML 是零依赖单文件，**直接用浏览器打开**即可交互：

- **标注模式**：点击页面落图钉、写想法，可导出标注文本，数据存 localStorage。
- **整页截图**：用 html2canvas 导出 2× 高清 PNG（横向溢出视口的页面也会完整拍下）。
- **可编辑标题**：大标题 / 副标题点击即改，失焦保存。
- **分支连线**：分支布局的折线 + 箭头 + 标注由脚本按真实坐标自动绘制。

## 内置依赖（CDN，无需本地安装）

- [Lucide](https://lucide.dev) — 图标
- [html2canvas](https://html2canvas.hertzen.com) — 截图导出
