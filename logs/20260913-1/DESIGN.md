# Design — fellow99.github.io 个人主页

> Brainstorming 产出物。记录策划思路、信息架构、文案矩阵、设计系统与实现约定。
> 阶段：Specification/Brainstorming · 日期：2026-09-13

---

## 1. 问题与目标

`fellow99.github.io` 是 GitHub 用户主页（User Pages），当前仓库仅有 LICENSE 与一行 README。
现存六个互相独立、已部署的子站点，但**缺少统一入口**：访客无法一眼看清作者是谁、做了哪些项目、彼此关系。

**目标**：用一个纯静态、零依赖的主页把人、项目、能力三件事讲清楚，并作为六个子站点的总入口。

**非目标（YAGNI）**：
- 不做博客/文章系统、不做后端、不做搜索、不做访客统计（无外部请求）。
- 不做单页 i18n 运行时切换（用两页镜像，避免 JS 与 SEO 复杂度）。
- 不注册到 `specs/`（用户明确要求本轮不登记）。

## 2. 受众

| 受众 | 关心什么 | 主页要回答 |
|---|---|---|
| 招聘/合作方 | 这个人是谁、能力边界 | Hero + About（含邮箱） |
| 同行开发者 | 有哪些可复用的开源项目 | 六张项目卡片 |
| 偶然到访者 | 快速找到想玩的/想用的 | 卡片直达六个入口 |

## 3. 六个入口的内容挖掘（事实来源：各工程 README）

| # | 入口 URL | 工程 | 定位（一句话） | 技术标签 |
|---|---|---|---|---|
| 01 | /deepseek-harness-desktop-website/ | d:\deepseek-harness-workspace | 把开源 AI Agent 运行环境 DeepSeek Harness 装进桌面：Electron 覆盖 Windows/Linux/macOS，并移植到 HarmonyOS 2in1/平板，复用官方 Web UI、零上游改动 | Electron · HarmonyOS · ArkTS · Node.js |
| 02 | /tpl-website/ | d:\tpl-workspace | 多端业务应用框架「一套骨架，贯通五端」：Web/Android/HarmonyOS/微信小程序 + 管理后台，统一工程结构与双后端 | Vue 3 · Spring Boot · Kotlin · ArkTS · Docker |
| 03 | /fellow99-skills/ | d:\GitHub\fellow99\fellow99-skills | 可复用的 AI Agent 技能集：HarmonyOS 开发与测试、小程序自动化与 CI、图像生成、规范驱动开发 | OpenCode · Claude Code · Node.js |
| 04 | /llm-router/ | d:\GitHub\fellow99\llm-router | OpenAI 兼容的多模型聚合路由网关：前缀路由、模型别名、加权负载均衡、故障回退 | TypeScript · Express · Proxy |
| 05 | /games-index/ | d:\GitHub\fellow99\games-index | 原生 HTML/JS/CSS 小游戏合集入口：视觉小说、竖版打飞机、扫雷，即点即玩 | HTML5 · Canvas · Games |
| 06 | /three-editor-by-ai/ | d:\GitHub\fellow99\three-editor-by-ai | 由 AI 全程编写代码的 Vue 3 + Three.js 3D 场景编辑器：变换/材质/灯光/动画/GLTF 导入/虚拟文件系统 | Vue 3 · Three.js · WebGL |

**叙事主线**：*「一个人，六个开源项目，一条 AI-native 的跨端产品线」* —
从桌面端（01）到跨端框架（02）到 Agent 能力（03）到模型网关（04）到轻量娱乐（05）到 3D 工具（06）。

> 事实守则：卡片文案只使用各工程 README 中可验证的描述，不编造能力、版本或官方关系。

## 4. 备选方案与取舍

| 方案 | 说明 | 取舍 |
|---|---|---|
| A. 单页 + JS 运行时 i18n | 一份 HTML，JS 切语言 | ❌ 首屏闪烁、SEO 差、要求两页分离 |
| **B. 双页镜像（选中）** | `index.html`(en) + `index_zh.html`(zh-CN)，结构 1:1 | ✅ 零 JS 依赖、可离线、SEO 友好、与既有子站约定一致 |
| C. 双页 + 卡片内嵌截图 | 每卡配项目截图 | ❌ 需二进制资源与联网，违背零依赖/离线目标 |

**主题**：`<head>` 内联脚本在首屏前写入 `data-theme`（localStorage → prefers-color-scheme），
CSS 变量驱动；两页共用同一 `localStorage['fellow99-theme']`，跳转语言时主题不丢。

## 5. 信息架构（两页结构完全一致）

```
Header(sticky)  brand「fellow99」 · nav(Projects/About) · 语言 EN|中文 · 主题开关
Hero            $ whoami · H1 fellow99 · 角色行 · lede · CTA(GitHub / Browse) · 3 项统计
Projects        6 张项目卡片（响应式 3/2/1 列），编号 01–06、图标、标题、描述、标签、进入链接
About           一段自述 + 事实清单（跨端、AI-native、开源）+ 联系方式
Footer          © 2026 fellow99 · MIT · GitHub · 邮箱 · 语言互链
```

## 6. 设计系统

- **配色**：单一靛蓝强调色，中性背景；亮 `--accent #4b5cf6` / 暗 `--accent #8290ff`；
  其余全部走语义 token（`--bg/--surface/--text/--muted/--hairline/--accent-soft`）。
- **字体**：仅系统字体栈，含 CJK（`PingFang SC`/`Microsoft YaHei`/`Noto Sans SC`）；等宽用于 eyebrow、编号、标签。
- **视觉语言**：`$` 前缀 mono eyebrow、极细 hairline 边框、卡片 hover 微抬升 + 强调色描边；
  Hero 背景为一层径向辉光 + 64px 网格（token 驱动）；不堆砌渐变。
- **动效**：150–300ms、`cubic-bezier(.22,.61,.36,1)`；卡片 hover `translateY(-3px)`；
  区块进入用 IntersectionObserver 轻淡入；**全部在 `prefers-reduced-motion` 下关闭**。
- **无障碍**：正文对比度 ≥ 4.5:1；交互元素 ≥ 44px；图标按钮带 `aria-label`；
  `:focus-visible` 强调色 2px 焦点环；语义标签 `<header>/<nav>/<main>/<section>/<footer>`。
- **响应式**：移动优先；≤980px 卡片 2 列、≤720px 1 列；≤760px 隐藏 nav 链接；容器 max-width 1160px。

## 7. 实现约定

- 纯原生 HTML + CSS + JS；**无框架、无构建、无 CDN、无外部请求**，`file://` 直接可开。
- 文件结构：`index.html` / `index_zh.html` / `assets/style.css` / `assets/script.js`。
- 图标用内联 `<svg><symbol>` 雪碧图，零外部资源。
- 语言切换 = 两个 `<a>` 链接；主题切换 = 一个按钮 + `data-label-*` 驱动 `aria-label`。

## 8. 验收标准

1. 两个页面均可在 `file://` 与 `http://` 下正常渲染，控制台无报错、无外部网络请求。
2. 主题切换生效、刷新记忆、首访跟随系统、无首屏闪烁；跨语言跳转主题保持。
3. 语言切换在 EN/中文间正确互跳，`lang` 属性正确。
4. 六个入口链接全部指向需求给出的六个 URL，`target="_blank" rel="noopener"`。
5. 375 / 768 / 1280 三档无横向溢出、布局不破版；两种主题下均可读。
6. `prefers-reduced-motion` 下无强制动效。

## 9. 设计自查（brainstorming spec self-review）

- 占位符扫描：无 TBD/TODO。✅
- 内部一致：结构、文案、token 三层相互对应；EN/ZH 镜像规则明确。✅
- 范围：单一实现计划可完成，无需拆分。✅
- 歧义：语言方案已明确为双页镜像；主题持久化键已固定为 `fellow99-theme`。✅
