# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

星露谷物语像素风格个人品牌网站，作为 AI 产品经理作品集。纯原生前端（Vanilla HTML + CSS + JS），单文件 `index.html` 承载全部逻辑，零框架零依赖，可直接用静态服务器部署。

## 开发与预览

```bash
# 本地预览（任意静态服务器均可）
npx serve .          # 或用 python -m http.server、live-server
```

无构建步骤、无 lint、无测试。改完直接刷新浏览器。

## 架构

`index.html` 内联全部 CSS（第 9-441 行）和 JS（第 541-1274 行）。核心是一个三阶段的状态机：

- **Stage 1「办公室」** — 3 个随机位置弹窗依次弹出，全部关闭后嫩芽气泡出现，点击进入 Stage 2
- **Stage 2「四季循环」** — 鼠标滚轮在春夏秋冬间切换（800ms 防抖），底部物品栏跟踪四个道具收集状态。秋季道具需要点两次（第一次瞬移逃跑）
- **Stage 3「终章」** — 四道具全部收集后自动触发黑屏转场。道具飞入 NPC 手中 → 随机信件 → 点击 NPC 展示社交面板（技能条 + 链接）

**状态管理**：`State` 对象（第 548 行）维护 currentStage、inventory[]、dialogueDone{}、autumnClickCount 等字段。每次关键操作后调用 `saveGame()` 写入 `localStorage`（key: `stardew_gundam_save`）。页面加载时检查存档并弹窗询问是否继续。

**季节切换**：`applySeasonChange()` 通过 opacity 淡入淡出实现，`SEASON_CONFIG` 对象定义每个季节的背景图、NPC 图、道具图及其定位。

**对话系统**：`showDialog()` 实现 45ms/字的打字机效果，`State.isTyping` 锁防止重复触发。点击对话框可跳过动画或关闭。

## 素材规范

所有素材在 `assets/` 下，遵循命名约定：`bg_{season}.png`（场景背景）、`wang_{season}.png`（NPC）、`icon_item{1-4}.png`（道具）。详见 PRD 文档「视觉资产命名规范」表。

## 需要注意的限制

- **PC 端优先**，无移动端适配，禁止原生滚动条（`body { overflow: hidden }`）
- 光标图 `cursor_stardew.png` 当前尺寸过大（1040×1428px），浏览器可能忽略，需降采样至 32×32px
- 当前链接为占位符（GitHub、Email、简历 PDF），部署前需替换为真实链接
- 背景图合计约 40MB，建议 TinyPNG 压缩
- localStorage 操作已用 try-catch 包裹，兼容隐私模式
