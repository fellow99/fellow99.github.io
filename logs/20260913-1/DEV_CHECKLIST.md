# DEV_CHECKLIST — fellow99.github.io 个人主页

> specs-based-devflow · Development 阶段 · 2026-09-13
> 约束：纯原生 HTML+JS+CSS · 不使用子 agent · 不改动 Git 分支 · 本轮不登记 specs/

## 0. 环境评估

| 项 | 结果 |
|---|---|
| 工作目录 | `D:\GitHub\fellow99\fellow99.github.io` |
| 是否 Git 仓库 | 是，分支 `main`（用户要求不改动分支） |
| 起始状态 | 仅 `LICENSE` + 一行 `README.md`（greenfield） |
| 构建系统 | 无（纯静态站点，无需编译步骤） |
| Dev server | 无；测试用 `file://` 直开（站点本身零依赖、可离线） |
| 既有设计参考 | `deepseek-harness-desktop-website`（双页镜像/主题约定）、`tpl-website`（Design Record 写法）、`games-index`（原生实现） |

## 1. 已加载并遵循的技能

| 技能 | 确认 | 使用方式 |
|---|---|---|
| `git-commit` | ✅ | 提交信息严格使用 Conventional Commits 前缀 `<type>[scope]: <desc>` |
| `brainstorming` | ✅ | 产出策划与设计（`DESIGN.md` + `README.md` 设计文档章） |
| `requesting-code-review` | ✅ | Code Review 阶段：自审并分类 Critical/Important/Minor（无子 agent） |
| `receiving-code-review` | ✅ | 逐条核验反馈、修复 Critical/Important、更新报告 |
| `specs-based-devflow` | ✅ | 本流程编排（本轮按用户要求不登记 `specs/`） |

## 2. 开发步骤与产物

| # | 步骤 | 产物 | 状态 |
|---|---|---|---|
| 1 | 深读六个入口工程 README，提炼定位/卖点/技术标签 | 见 `DESIGN.md` §3 | ✅ |
| 2 | brainstorming：受众、叙事、方案取舍、结构、设计系统 | `logs/20260913-1/DESIGN.md` | ✅ |
| 3 | 设计文档写入 README | `README.md`（重写为 Design Record） | ✅ |
| 4 | 英文主页 | `index.html` | ✅ |
| 5 | 中文主页（1:1 结构镜像） | `index_zh.html` | ✅ |
| 6 | 共享样式（token 双主题 + 响应式 + reduced-motion） | `assets/style.css` | ✅ |
| 7 | 共享脚本（主题记忆/切页保持、导航高亮、滚动淡入、年份） | `assets/script.js` | ✅ |
| 8 | `<head>` 内联防闪烁脚本（首屏前定主题 + `js` 门控） | 两个 HTML 内联脚本 | ✅ |
| 9 | 浏览器实测（功能/响应式/控制台/网络/对比度） | `TEST_REPORT.md` | ✅ |
| 10 | 代码评审与修复 | `REVIEW_REPORT.md` | ✅ |

## 3. 实现要点（供评审核对）

- **零依赖**：无框架、无构建、无 CDN、无外部字体/图片/统计；仅 3 个同源请求（html/css/js）。
- **双主题**：`<html data-theme>` + CSS 语义 token；亮/暗仅覆盖变量层。
- **防闪烁**：`<head>` 内联脚本在 body 之前解析 `localStorage['fellow99-theme']` → 否则 `prefers-color-scheme`。
- **跨页主题保持**：两页共用同一 localStorage 键，语言互跳不丢主题。
- **无 JS 降级**：`.reveal` 动效由 `html.js` 门控；JS 关闭时内容直接可见。
- **无障碍**：`aria-label`/`aria-current`/`aria-pressed`、语义标签、`:focus-visible` 焦点环、reduced-motion 关闭动效。
- **EN/ZH 镜像**：区块顺序、类名、交互一致，仅文案翻译（已用脚本核验计数一致）。

## 4. 已知限制

- 视觉验证以**程序化断言**（计算样式、布局盒、对比度、控制台、网络）完成；本模型无法读取截图，
  故未做人工看图评审。如需，可用浏览器打开两页在 1280/768/390 与亮/暗两态下复检。
- 浏览器窗口存在最小宽度，390px 档位通过 DevTools 设备仿真验证。
