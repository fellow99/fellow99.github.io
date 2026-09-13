# REVIEW_REPORT — fellow99.github.io 个人主页

> specs-based-devflow · Code Review 阶段 · 2026-09-13
> 说明：用户要求**不使用子 agent**，故未派发 reviewer agent；按 `requesting-code-review`
> 的输出规范（Critical / Important / Minor）自审，并按 `receiving-code-review` 的规范
> **逐条对照真实代码与浏览器实测**核验后再修复。

## 审查范围

变更文件：`index.html`、`index_zh.html`、`assets/style.css`、`assets/script.js`、`README.md`、`logs/…`
基线：仓库初始 commit `09075bc`（仅 LICENSE + 一行 README）→ 本次全部为新增。

## 优点

- EN/ZH 严格 1:1 结构镜像（脚本核验计数一致），仅文案翻译。
- 零依赖、零外部请求；`file://` 可离线渲染；仅 3 个同源请求。
- 主题双通道降级：`<head>` 内联脚本防闪烁 + `prefers-color-scheme` 跟随 + localStorage 记忆 + 跨页保持。
- 语义标签、`aria-label`/`aria-current`/`aria-pressed`、`:focus-visible`、reduced-motion 降级齐备。
- 无障碍与视觉均走 CSS 语义 token，单一强调色，无第三方资源。

## 发现（按严重级别）

### Critical

| # | 发现 | 证据 | 处置 |
|---|---|---|---|
| C1 | 六张项目卡图标渲染为**实心黑块**：`<svg class="card-icon">` 未套用 `.icon` 的 `fill:none;stroke:currentColor`，`<use>` 内 symbol 走默认 `fill:black` | 实测 `cardFill=rgb(0,0,0)`、`cardStroke=none` | ✅ 已修：卡图标改为 `class="card-icon icon"`（两页各 6 处）；复测 `fill=none`、`stroke=强调色`、`strokeWidth=1.7px` |

### Important

| # | 发现 | 证据 | 处置 |
|---|---|---|---|
| I1 | 页脚说明文字用 `--faint`（亮色 `#868da0` on `#eceef6`）对比度约 **2.8:1**，低于 WCAG AA 4.5:1 | 计算对比度 | ✅ 已修：改用 `--muted`；复测 **5.69:1**（暗色 7.4:1） |
| I2 | 卡片编号 `--faint` 同属正文级文字，对比度不足 | 计算对比度 | ✅ 已修：改用 `--muted`；复测 **6.59:1** |
| I3 | 若 `assets/script.js` 加载失败，`html.js .reveal{opacity:0}` 会使**整页内容永久不可见** | CSS 门控 + 脚本缺失的组合失败态 | ✅ 已修:`<head>` 脚本加 2s 兜底：未收到 `window.__fellow99Ready` 则移除 `js` 类；`script.js` 末尾置位该标志 |

### Minor

| # | 发现 | 处置 |
|---|---|---|
| M1 | `color-scheme` 初值写死 `light dark`，未随主题切换（滚动条/表单控件配色不跟随） | ✅ 已修：CSS 加 `:root{color-scheme:light}` / `html[data-theme=dark]{color-scheme:dark}`；复测亮/暗分别 `light`/`dark` |
| M2 | reduced-motion 下仍保留 0–350ms 的 `transition-delay` | ✅ 已修：在 reduced-motion 块加 `transition-delay:0ms !important` |
| M3 | 缺 `<link rel="canonical">` / OG 标签 / `hreflang` alternates | ⏸ 暂缓（YAGNI：个人主页静态枢纽，当前曝光渠道为 GitHub，收益低于维护成本）；如需再补 |
| M4 | `<use href>` 无旧版 `xlink:href` 兜底（Safari < 12） | ⏸ 暂缓（目标浏览器已普遍支持 `href`；如需兼容旧 Safari 再加一行） |

## 未发现问题的检查项

- XSS/注入：纯静态、无用户输入、无 `innerHTML` 写入（仅 `textContent`）——无风险面。
- 秘密泄露：无凭据、无 `.env`、无追踪脚本。
- 链接安全：所有 `target="_blank"` 均带 `rel="noopener"`（实测 `badBlankTargets=0`）。
- EN/ZH 结构漂移：计数核验全部一致（project-card/reveal/section/h3/li…）。
- 控制台：两页均无 error/warning。

## 复评（re-review）

Critical 与 Important 全部修复，并以浏览器实测复验：

```text
C1  → cardFill=none, cardStroke=rgb(75,92,246)[亮]/rgb(130,144,255)[暗], strokeWidth=1.7px
I1  → footerNoteContrast = 5.69 (亮) / 7.4 (暗)
I2  → cardIndexContrast  = 6.59 (亮)
I3  → __fellow99Ready=true, html.js 保留；兜底逻辑就位
M1  → colorScheme = light / dark 随主题
M2  → reduced-motion 关闭 delay
console → 无消息
```

## 结论

**无遗留 Critical / Important 项**，可进入（并已完成）回归验证。Minor 中 M1/M2 已修复，M3/M4 已记录并有意暂缓。
