# TEST_REPORT — fellow99.github.io 个人主页

> specs-based-devflow · Testing 阶段 · 2026-09-13
> 环境：Chrome（DevTools 协议）· 页面经 `file://` 直接加载 · 无后端
> 说明：本模型不支持读取图片，视觉验证改用**计算样式 / 布局盒 / 对比度 / 控制台 / 网络**断言完成。

## 汇总

| 项 | 值 |
|---|---|
| 用例总数 | 18 |
| 通过 | 18 |
| 失败 | 0 |
| 控制台错误 | 0 |
| 外部网络请求 | 0 |

## 用例明细

| # | 用例 | 期望 | 实测 | 结果 |
|---|---|---|---|---|
| TC-01 | 英文页基本信息 | `lang=en`，标题含 fellow99 | `lang=en`，`fellow99 — Build with AI` | ✅ |
| TC-02 | 中文页基本信息 | `lang=zh-CN`，标题中文 | `lang=zh-CN`，`fellow99 — 用 AI 造物` | ✅ |
| TC-03 | 主题切换 | 亮↔暗 token 全量切换 | `--bg` `#f5f6fa`↔`#08090d`，`--accent` `#4b5cf6`↔`#8290ff` | ✅ |
| TC-04 | 主题持久化 | 写入 `localStorage['fellow99-theme']` | 点击后 `stored="light"` | ✅ |
| TC-05 | 跨页保持主题 | 语言互跳后主题不变 | EN 设为 light → 打开 ZH 页仍为 light | ✅ |
| TC-06 | 首访跟随系统 | 无存储时用 `prefers-color-scheme` | 系统暗 → 初始 `data-theme=dark`；`aria-label="Switch to light theme"` | ✅ |
| TC-07 | 首屏无闪烁 | `<head>` 内联脚本在 body 前定主题 | 内联脚本先于 CSS/body 执行（无语义依赖 JS 的默认态） | ✅ |
| TC-08 | 六个入口链接 | 六个目标 URL 全部正确，`target=_blank` + `rel=noopener` | 六 URL 与需求一致；`badBlankTargets=0` | ✅ |
| TC-09 | 语言切换 | 互链 + `aria-current` 标注当前语言 | EN 页 `aria-current` 在 EN；ZH 页在「中文」，`.lang.is-active` 正确 | ✅ |
| TC-10 | 响应式列数 | 1280→3 列，768→2 列，390→1 列；≤760 隐藏 nav | 357×3 / 343×2 / 354×1；390 时 `nav.display=none` | ✅ |
| TC-11 | 无横向溢出 | 三档 `scrollWidth-clientWidth ≤ 0` | 1280/768/390 均 `overflow=0`；390 无卡片越界 | ✅ |
| TC-12 | 控制台干净 | 无 error/warning | 两页均 `<no console messages found>` | ✅ |
| TC-13 | 零外部请求 | 仅同源 html/css/js | 3 个 `file://` 请求（index_zh / style.css / script.js），无外部 | ✅ |
| TC-14 | 滚动动效 + 导航高亮 | 滚动后 `.reveal` 全部可见；nav 高亮当前区块 | 21/21 `.is-visible`；`activeNav=#about` | ✅ |
| TC-15 | 对比度 | 正文与卡片次要文字 ≥ 4.5:1 | 亮：正文 16.74、次要 6.59；暗：正文 16.74、次要 7.19 | ✅ |
| TC-16 | 页脚年份 | `[data-year]` 自动填充 | `2026` | ✅ |
| TC-17 | EN/ZH 结构镜像 | 区块/类/元素计数一致 | project-card 6=6、reveal 21=21、section 4=4、h3 11=11、li 51=51 …全部 OK | ✅ |
| TC-18 | reduced-motion | 关闭平滑滚动与动效，内容直接可见 | CSS `prefers-reduced-motion` 块 + JS 直接置 `is-visible` 路径存在 | ✅ |

## 关键证据摘录

```text
# 主题切换（EN, 1280）
{"theme":"light","tokens":{"--bg":"#f5f6fa","--text":"#14161c","--accent":"#4b5cf6"},
 "toggleAria":"Switch to dark theme","ariaPressed":"false","stored":"light"}

# 中文页（跨页主题保持）
{"lang":"zh-CN","theme":"light","title":"fellow99 — 用 AI 造物","cardCount":6,
 "toggleAria":"切换到暗色主题","langActive":"中文","overflow":0,"badBlankTargets":0}

# 390px 设备仿真
{"width":390,"grid":"354.4px","navDisplay":"none","overflow":0,"cardsOffscreenX":0,
 "darkBodyContrast":16.74,"darkMutedContrast":7.19}

# 滚动后
{"revealTotal":21,"revealVisible":21,"activeNav":"#about"}

# 网络
reqid=13 index_zh.html [200] · reqid=14 assets/style.css [200] · reqid=15 assets/script.js [200]
```

## 结论

全部验收标准（README §验收 / DESIGN §8）满足：双页可离线渲染、双主题可切换且跨页保持、
六个入口链接正确、三档响应式无溢出、零外部请求、控制台干净。

---

# REGRESSION — 修复后回归（2026-09-13）

修复 4 项（C1 / I1+I2 / I3 / M1+M2，详见 `REVIEW_REPORT.md`）后重跑关键用例：

| 用例 | 复测结果 | 结果 |
|---|---|---|
| C1 卡图标 | `fill=none`、`stroke=rgb(75,92,246)`(亮)/`rgb(130,144,255)`(暗)、`strokeWidth=1.7px` | ✅ |
| I1 页脚说明对比度 | 亮 **5.69:1** / 暗 **7.4:1**（≥4.5） | ✅ |
| I2 卡片编号对比度 | 亮 **6.59:1** | ✅ |
| I3 脚本失败兜底 | `__fellow99Ready=true`，`html.js` 保留；2s 兜底逻辑就位 | ✅ |
| M1 color-scheme | 亮 `light` / 暗 `dark`（随主题） | ✅ |
| M2 reduced-motion | `transition-delay:0ms !important` | ✅ |
| TC-03/04 主题切换与持久化 | 亮↔暗正常，写入 `fellow99-theme` | ✅ |
| TC-10/11 响应式与溢出 | 1280/768/390 列数正确、`overflow=0` | ✅ |
| TC-12 控制台 | 修复后重载仍无任何 console 消息 | ✅ |
| TC-14 滚动动效 | `.reveal` 21/21 可见，nav 高亮正确 | ✅ |

> 无新增回归；`TEST_REPORT` 原 18 项用例结论不变。
