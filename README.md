# fellow99.github.io

> GitHub 个人主页（User Pages）——**六个开源项目的统一入口**。
>
> 线上地址：<https://fellow99.github.io>
>
> 本文件同时作为本站的 **设计文档（Design Record）**：记录定位、信息架构、文案矩阵、
> 设计系统、交互与实现约定。与兄弟站点 [deepseek-harness-desktop-website] 与
> [tpl-website] 保持同一套「双页镜像 + 纯原生 + 双主题」的工程约定。

---

## 一、定位与目标

`fellow99.github.io` 是 GitHub 用户主页。站点的唯一职责是：**用一个纯静态、零依赖的页面，
把「人、项目、能力」三件事讲清楚，并作为六个子站点的总入口。**

| 受众 | 关心什么 | 主页对应区块 |
|---|---|---|
| 招聘 / 合作方 | 这个人是谁、能力边界在哪 | Hero + About（含邮箱） |
| 同行开发者 | 有哪些值得一看的开源项目 | 六张项目卡片 |
| 偶然到访者 | 快速找到想玩的 / 想用的 | 卡片直达六个入口 |

**叙事主线**：*一个人，六个开源项目，一条 AI-native 的跨端产品线*。
从桌面端 → 跨端框架 → Agent 能力 → 模型网关 → 轻量娱乐 → 3D 工具，逐层展开。

### 非目标（YAGNI）

- 不做博客 / 文章系统、不做后端、不做站内搜索、不做访客统计（**不产生任何外部请求**）。
- 不做单页运行时 i18n（采用双页镜像，避免 JS 与 SEO 复杂度）。
- 本轮需求不登记到 `specs/`。

---

## 二、六个入口（事实来源：各工程 README）

| # | 入口 URL | 相关工程 | 定位 | 技术标签 |
|---|---|---|---|---|
| 01 | `/deepseek-harness-desktop-website/` | `d:\deepseek-harness-workspace` | 把开源 AI Agent 运行环境 DeepSeek Harness 装进桌面：Electron 覆盖 Windows/Linux/macOS，并移植到 HarmonyOS 2in1/平板，复用官方 Web UI、零上游改动 | Electron · HarmonyOS · ArkTS · Node.js |
| 02 | `/tpl-website/` | `d:\tpl-workspace` | 多端业务应用框架「一套骨架，贯通五端」：Web / Android / HarmonyOS / 微信小程序 + 管理后台，统一工程结构与双后端 | Vue 3 · Spring Boot · Kotlin · ArkTS |
| 03 | `/fellow99-skills/` | `d:\GitHub\fellow99\fellow99-skills` | 可复用的 AI Agent 技能集：HarmonyOS 开发与真机测试、小程序自动化与 CI、图像生成、代码知识图谱导航、规范驱动开发 | OpenCode · Claude Code · Node.js |
| 04 | `/llm-router/` | `d:\GitHub\fellow99\llm-router` | OpenAI 兼容的多模型聚合路由网关：前缀路由、模型别名、加权负载均衡、故障回退、流式响应 | TypeScript · Express · Proxy |
| 05 | `/games-index/` | `d:\GitHub\fellow99\games-index` | 原生 HTML/JS/CSS 小游戏合集入口：视觉小说、竖版打飞机、扫雷，即点即玩 | HTML5 · Canvas · Vanilla JS |
| 06 | `/three-editor-by-ai/` | `d:\GitHub\fellow99\three-editor-by-ai` | 由 AI 全程编写代码的 Vue 3 + Three.js 3D 场景编辑器：变换 / 材质 / 灯光 / 动画 / GLTF 导入 / 虚拟文件系统 | Vue 3 · Three.js · WebGL |

> **事实守则**：卡片文案只使用各工程 README 中可验证的描述，不编造能力、版本或官方关系。
> 产品事实变化时，同步更新两个语言页面与本表。

---

## 三、信息架构

两个语言页面共享**完全一致**的纵向结构（三个 landmark 区块 + Hero）：

```
┌─ Header (sticky) ─────────────────────────────────────────┐
│ brand「</> fellow99」 · nav(项目/关于) · 语言 EN|中文 · 主题开关  │
├───────────────────────────────────────────────────────────┤
│ Hero  $ whoami · H1 fellow99 · 角色行 · lede · CTA · 3 项统计 │
├───────────────────────────────────────────────────────────┤
│ 项目   6 张卡片（编号 01–06 + 图标 + 标题 + 描述 + 标签 + 进入）│
├───────────────────────────────────────────────────────────┤
│ 关于   一段自述 + 三条事实清单 + 联系方式（邮箱）              │
├───────────────────────────────────────────────────────────┤
│ Footer brand · 说明 · © 2026 · GitHub · 邮箱 · 语言互链       │
└───────────────────────────────────────────────────────────┘
```

**联系方式**：`fellow99@163.com` —— 以 `mailto:` 链接出现在「关于」区块与页脚，
全站共 2 处（每页）。不引入表单 / 后端，保持零依赖。

EN / ZH 必须**结构完全一致** —— 区块顺序、元素类名、交互行为绝不分化，只有文案被翻译。

---

## 四、设计系统

### 4.1 主题 Token（`assets/style.css`）

`<html data-theme="light|dark">` 驱动，全部走语义变量；暗色只覆盖变量层。

| Token | Light | Dark | 用途 |
|---|---|---|---|
| `--bg` | `#f5f6fa` | `#08090d` | 页面背景 |
| `--bg-soft` | `#eceef6` | `#0d0f16` | 交替区块 / 页脚 |
| `--surface` | `#ffffff` | `#101319` | 卡片、按钮 |
| `--surface-2` | `#f8f9fc` | `#151922` | 次级面、标签底 |
| `--text` | `#14161c` | `#eceef4` | 正文 |
| `--muted` | `#565d6e` | `#9aa1b2` | 次要文字 |
| `--faint` | `#868da0` | `#6b7286` | 弱化文字 |
| `--hairline` | `#e3e6ef` | `#20242f` | 1px 边框 |
| `--accent` | `#4b5cf6` | `#8290ff` | 唯一强调色（靛蓝） |
| `--accent-soft` | `#eceefe` | `#161a29` | 强调色浅底 |
| `--accent-line` | `rgba(75,92,246,.42)` | `rgba(130,144,255,.48)` | hover 描边 |
| `--glow` | `rgba(75,92,246,.16)` | `rgba(130,144,255,.20)` | 径向辉光 |
| `--grid` | `rgba(20,24,50,.045)` | `rgba(255,255,255,.035)` | Hero 网格 |
| `--header-bg` | `rgba(245,246,250,.82)` | `rgba(8,9,13,.78)` | 顶栏毛玻璃底 |

阴影 `--shadow-sm/md/lg`、圆角 `--radius 16 / --radius-sm 11 / --radius-lg 20`、
缓动 `--ease = cubic-bezier(.22,.61,.36,1)` 亦随主题切换。

**主题解析顺序**：`<head>` 内联脚本在首屏前决定 `data-theme`
（`localStorage['fellow99-theme']` → 否则 `prefers-color-scheme`）→ CSS 渲染 token。
两页共用同一 localStorage 键，因此**跳转语言时主题不丢失**。

### 4.2 字体与视觉语言

- **字体**：只用系统字体栈。正文含 CJK（`PingFang SC` / `Microsoft YaHei` / `Noto Sans SC`）；
  等宽（`ui-monospace` / `JetBrains Mono` / `Consolas`）用于 eyebrow、编号、标签、统计数字。
- **单一加速色**：靛蓝，仅用于 eyebrow、图标、按钮、hover 描边、链接——不堆砌渐变。
- **视觉签名**：`$` 前缀的 mono eyebrow（`$ whoami`、`$ ls -1 projects/`、`$ man fellow99`）；
  极细 hairline 边框；Hero 一层径向辉光 + 64px 网格（token 驱动、mask 渐隐）。
- **图标**：内联 `<svg><symbol>` 雪碧图（`#i-desktop`/`#i-layers`/`#i-spark`/`#i-route`/`#i-gamepad`/`#i-cube`/`#i-sun`/`#i-moon`/`#i-external`），零外部资源。

### 4.3 无障碍

- 正文对比度 ≥ 4.5:1；交互元素最小约 44px（按钮 46px、图标按钮 40×40 + padding）。
- 图标按钮带 `aria-label`；语言切换用 `role="group"` + `aria-current="page"`。
- 语义标签 `<header>/<nav>/<main>/<section>/<footer>`；`:focus-visible` 强调色 2px 焦点环、偏移 3px。

---

## 五、交互

1. **主题切换** —— 图标按钮。亮色显示**月亮**、暗色显示**太阳**（图标＝目标状态）；`aria-label`
   用 `data-label-dark/light` 描述**动作**并在切换时实时更新；选择写入 `localStorage['fellow99-theme']`。
2. **语言切换** —— 两个 `<a>` 互链：`index.html`(EN, `hreflang="en"`) ↔ `index_zh.html`
   (`hreflang="zh-CN"`)；当前语言 `aria-current="page"`。无 JS 依赖，主题经 localStorage 保持。
3. **滚动进入动效** —— `IntersectionObserver` 给 `.reveal` 加 `.is-visible`，轻淡入 + 上移复位。
   **JS 门控**（`html.js .reveal`）：禁用 JS 时内容直接可见，绝不留白。
4. **导航高亮** —— 滚动时按当前区块高亮对应 nav 链接。
5. **页脚年份** —— `[data-year]` 自动填当前年份。
6. **降级** —— `prefers-reduced-motion: reduce` 下关闭平滑滚动、进入动效与 hover 位移。

---

## 六、响应式

| 断点 | 行为 |
|---|---|
| > 980px | 项目卡片 3 列；关于区 2 列（文案 : 事实 ≈ 1.15 : 0.85）；顶栏单行（brand · nav · 语言/主题） |
| ≤ 980px | 项目卡片 2 列；关于区单列 |
| ≤ 760px | **顶栏改为两行**：第 1 行 brand + 语言/主题，第 2 行导航作为**可横向滚动的整行**保留（不再隐藏）；主题按钮 42px、语言项 ≥36px |
| ≤ 620px | 项目卡片 1 列；Hero 标题/内边距收窄；页脚纵向堆叠 |
| ≤ 440px | CTA 按钮整行铺满；统计条改 3 列网格；卡片内边距收窄 |
| ≤ 380px | 进一步收窄左右边距，确保 brand 与控件同处一行 |

- 容器 `max-width: 1160px`，横向内边距 24px（≤760px 收为 18px，≤380px 收为 14px）。
- 移动端锚点跳转由 `--header-h`（≤760px 置 104px）驱动 `scroll-margin-top`，标题不会被吸顶栏遮挡。
- 已验证竖屏档位：**320 / 360 / 390 / 414 / 768 / 1280**，两种主题下均无横向溢出、无元素越界。
- 目标（竖屏）基准为 360×780、390×844 设备仿真；触控目标 ≥36px（关键按钮 ≥42px）。


---

## 七、文件结构

```
fellow99.github.io/
├── .github/workflows/pages.yml   # 部署到 GitHub Pages（见第八节）
├── index.html          # 英文版（lang="en"，含 head 内联防闪烁脚本）
├── index_zh.html       # 简体中文版（lang="zh-CN"），index.html 的 1:1 结构镜像
├── assets/
│   ├── style.css       # 全部样式：token + 主题 + 布局 + 组件 + 响应式 + 动效
│   └── script.js       # 主题切换/记忆、导航高亮、滚动淡入、年份（语言中立）
├── logs/               # 分阶段开发产物（设计、checklist、review、测试报告）
├── README.md           # 本文件（设计文档）
└── LICENSE             # MIT
```

**约束**：纯原生 HTML + CSS + JS；无框架、无构建步骤、无 CDN、无第三方 JS 库；
无 `fetch`、无外部字体/图片请求——`file://` 直接双击即可完整渲染。

---

## 八、开发与部署

```bash
# 本地预览：任选其一
npx serve .                       # 然后访问 http://localhost:3000/
# 或直接用浏览器打开 index.html（file:// 亦可）
```

### 部署（GitHub Pages）

由 [`.github/workflows/pages.yml`](./.github/workflows/pages.yml) 自动发布到
<https://fellow99.github.io/>：

- **触发**：推送到 `main`，或 Actions 页手动 `workflow_dispatch`。
- **权限**：`contents: read` + `pages: write` + `id-token: write`（最小权限）。
- **并发**：`group: pages`、`cancel-in-progress: true`（新构建取消旧构建）。
- **产物口径**：先把站点文件复制到 `site/`（`index.html`、`index_zh.html`、`assets/`）再上传，
  因此 **README / LICENSE / logs 不会被发布**；站点为纯静态，无 build 步骤。
- **官方 Actions**：`actions/checkout@v4` → `actions/configure-pages@v5` →
  `actions/upload-pages-artifact@v3`（`path: site`）→ `actions/deploy-pages@v4`。

> **一次性设置**：仓库 **Settings → Pages → Build and deployment → Source = "GitHub Actions"**。
> 本仓库是用户主页（User Pages），根路径即为站点根；若仍保留 "Deploy from a branch"，
> 请改为 "GitHub Actions"，否则二者会冲突。

- 语言互链、主题 token 与卡片链接均为相对/绝对静态地址，不影响部署。
- 文档同步：改文案/结构后按第九节清单同时更新两个语言页面。


---

## 九、贡献核对清单

- [ ] 改文案？**同时**更新 `index.html` 与 `index_zh.html`，并核对第「二」节事实表。
- [ ] 加区块？两个页面 1:1 镜像 markup，并在 `style.css` 补 token/样式。
- [ ] 改主题/交互？保持 token 驱动 + 无 JS 降级（`html.js` 门控）+ reduced-motion 路径。
- [ ] 离线自检：`file://` 打开两页，控制台无报错、无网络请求。
- [ ] 视觉自检：375 / 768 / 1280 三档 × 亮/暗两种主题。

---

## License

[MIT](./LICENSE) © 2026 fellow99

[deepseek-harness-desktop-website]: https://fellow99.github.io/deepseek-harness-desktop-website/
[tpl-website]: https://fellow99.github.io/tpl-website/
