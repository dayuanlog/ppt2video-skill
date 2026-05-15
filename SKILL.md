---
name: ppt2video-skills
description: 使用 HyperFrames 生成带 GSAP 动画的独立 HTML 视频 scene。收到口播文案后，必须先按 PPT 叙事逻辑拆分文案并输出结构确认稿；用户确认页结构后，再让用户打开 theme-previews/all-themes-vertical.html 选择主题；最后参考所选主题目录中的现成 HTML 样式生成 scene-XX.html、script.txt、outline.md、subtitle-scene-map.md 和 scene-design-plan.md。适用于“把文案做成视频 PPT”“按主题生成 HTML 视频画面”“根据 theme-previews 借鉴现成版式”的场景。强调可控、可编辑、渲染稳定，避免复杂透明叠加和高风险 CSS。
---

# PPT2Video Skills

## Source of Truth

这个 skill 现在以 `theme-previews/` 为唯一主题来源。

- `theme-previews/all-themes-vertical.html` 是主题选择入口。
- `theme-previews/<theme-name>/scene-*.html` 是真实样式参考。
- `references/` 目录现在只作为规则护栏和提炼总结使用，优先级低于所选主题目录中的真实 HTML。

先看现成主题 HTML，再写新 scene；不要先脑补风格，再回头找参考。

---

## Workflow

严格按下面的两阶段门禁执行，除非用户明确说“跳过确认，直接按你的方案继续”。

```text
用户提供口播文案
  -> 先确认画面尺寸与使用场景
  -> 生成 PPT 结构确认稿（structure-proposal.md）
  -> 等用户确认页结构
  -> 生成正式规划文件
  -> 让用户打开 theme-previews/all-themes-vertical.html 选主题
  -> 读取所选主题目录中的真实 scene HTML
  -> 按该主题生成 scene-XX.html
  -> 做数量、映射、HTML 结构与渲染安全检查
```

### 禁止事项

- 未确认 PPT 结构前，不生成 `scene-XX.html`
- 未确认主题前，不生成 `scene-XX.html`
- 不再存在旧 `templates/` 模板源
- 不复制旧 demo 的文案，只借鉴它的版式语言、颜色、留白、动效节奏和组件结构

---

## 0. Determine Canvas Size

开始前先确认尺寸。若用户没指定，按内容场景推荐：

| 尺寸 | 比例 | 适用 |
|---|---|---|
| `1080 x 1440` | 3:4 | 当前 `theme-previews` 竖屏主题默认尺寸 |
| `720 x 1280` | 9:16 | 短视频竖屏 |
| `1280 x 720` | 16:9 | 横屏讲解或长视频 |
| `1080 x 1080` | 1:1 | 方图视频 |

如果用户提到 `theme-previews/all-themes-vertical.html`、竖屏参考模板、现成竖屏主题，默认优先使用 `1080 x 1440`。

---

## 1. Split Script Into PPT Structure First

### First deliverable: `structure-proposal.md`

收到文案后，第一步不是写 HTML，而是生成结构确认稿。确认稿至少要回答：

- 建议拆成多少页 PPT
- 每页讲什么
- 每页对应原文哪一段或哪几句
- 为什么这样拆更适合 PPT，而不是字幕切片

推荐格式：

```md
# PPT 结构确认稿

- 核心主题：
- 目标观众：
- 建议尺寸：
- 建议页数：
- 拆分原则：

| 页码 | 页面主题 | 对应原文范围 | 口播摘要 | 拆分理由 | 状态 |
|---|---|---|---|---|---|
| 01 | 开场观点 | P01 | 抛出核心判断 | 适合作为第一眼钩子 | 待确认 |
```

### Split Rules

- 一页 PPT 对应一个相对完整的语义单元：观点、问题、例子、步骤、转折或结论
- 不要把 10 个字、几秒钟内容机械切成一页
- 普通内容页通常对应约 8-20 秒口播
- 标题页、章节页、金句页可以更短，但必须有明确叙事作用
- 过长段落应按语义边界拆分，过碎段落应优先合并
- 尊重用户原始分段，但允许提出更适合 PPT 的合并/拆分页建议
- `script.txt` 的内容必须保留原文含义，不要在这个阶段偷偷改写成营销文案

### Confirmation Gate 1

输出结构确认稿后必须停下，等待用户确认，例如：

```text
请先确认 PPT 页结构和文案对应关系。你可以回复“确认”，或告诉我需要合并、拆分、调整哪几页。确认后我再生成正式规划文件，并进入主题选择。
```

---

## 2. Generate Planning Files After Structure Confirmation

用户确认后，再生成下面这些正式文件：

- `script.txt`
- `outline.md`
- `subtitle-scene-map.md`
- `scene-design-plan.md`
- 保留 `structure-proposal.md`

### `script.txt`

- 每行对应一个 scene
- 第 1 行对应 `scene-01.html`，第 2 行对应 `scene-02.html`
- 一页对应一行，不要一个 scene 对多行，也不要多页共用一行
- 无口播页只有在用户明确需要时才允许出现，并在映射表中标记 `no-voice`

### `outline.md`

写清每页：

- 页面标题
- 对应 `script.txt` 第几行
- 页面目的
- 口播摘要

### `subtitle-scene-map.md`

强制保持一一对应：

- `script.txt` 第 N 行 = `scene-NN.html`
- `outline.md` 第 NN 页 = `scene-NN.html`
- `scene-design-plan.md` 第 NN 页 = `scene-NN.html`

### `scene-design-plan.md`

每页在写 HTML 前先写清：

- 对应口播
- 对应 `script.txt` 行号
- 页面目的
- 页面标题与屏上文字
- 推荐构图
- 推荐主视觉
- 推荐动效节奏
- 渲染安全注意点
- 如有图片，说明图片来源、用途、比例处理方式

---

## 3. Theme Selection Workflow

规划文件完成后，必须让用户选主题，再生成 HTML。

请让用户打开：

[all-themes-vertical.html](D:/DOC/PPT2VIDEO/ppt-to-video-skills/theme-previews/all-themes-vertical.html)

用户返回主题名称、目录名或示例文件名后，再继续。

### Theme Selection Rule

不要在 skill 中硬编码过时主题名。以 `theme-previews/` 当前实际存在的目录为准。

当前常见主题包括：

- `guizang-magazine-demo`
- `guizang-swiss-demo`
- `handdrawn-whiteboard-demo`
- `launch-minimal-demo`

如果未来新增目录，以 `all-themes-vertical.html` 和对应目录为准。

### Confirmation Gate 2

在开始生成 HTML 前，明确复述一次：

```text
将使用 <theme-name> 主题生成本次 scene。
```

---

## 4. How To Use Theme Previews Correctly

选定主题后，先读取该主题目录下 3-5 个代表性 scene HTML，总结它的真实风格语法，再生成新 scene。

重点提取：

- 背景处理方式
- 主副标题层级
- 颜色角色（bg / fg / accent / muted / line）
- 留白和安全区
- 常见构图
- 卡片、分栏、标签、流程线等组件语言
- 动画节奏与常见入场方式

不要照抄：

- 示例文案
- 示例标题
- 某一页的固定结构

要做的是“同一主题家族下的新页面”，不是复制粘贴旧 scene。

### Theme-Driven Layout Rule

- 同一条视频可以在一个主题家族内使用不同骨架页
- 不要全片只套一个布局
- 如果同类布局连续出现，标题层级、构图重心、强调方式必须变化

---

## 5. References Usage Policy

`references/` 现在是次级规则层，不是风格主来源。

### Active references

优先使用这些：

- [references/layout-rules.md](references/layout-rules.md)
- [references/image-asset-rules.md](references/image-asset-rules.md)
- [references/color-palettes.md](references/color-palettes.md)
- [references/motion-principles.md](references/motion-principles.md)
- [references/animation-patterns.md](references/animation-patterns.md)
- [references/render-safe-css.md](references/render-safe-css.md)
- [references/typography.md](references/typography.md)

### Legacy references

下面这些不再作为主工作流入口：

- `references/visual-styles.md`：只做历史风格摘要，不替代 `theme-previews`
- `references/scene-transition.md`：当前输出是独立 scene，默认不设计跨页过渡

---

## 6. Image Asset Rules

如用户提供图片、截图、品牌图或长截图：

1. 先编号并记录
2. 判断它的内容类型和证据价值
3. 再分配到合适的页面
4. 最后写进 `scene-design-plan.md`

图片必须服务当前口播页，不要把图当装饰随便塞。

### Asset delivery rule

如果输出要被“单文件 HTML 渲染器”直接消费，外部资源可能失效。优先级如下：

1. 单文件场景：字体/小图标/小贴纸优先内嵌 `data:` URL
2. 多文件场景：资源与 HTML 同目录或稳定相对目录
3. 远程图床：仅在确认渲染环境支持联网时使用

不要假设 `localhost`、本地相对路径或工具临时目录一定能正确解析。

---

## 7. HTML Scene Rules

每个 scene 必须是独立 HTML 文件，并遵守下面的 HyperFrames 结构：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width={宽度}, height={高度}">
  <title>scene-01</title>
  <style>
    * { box-sizing: border-box; }
    html, body {
      margin: 0;
      width: {宽度}px;
      height: {高度}px;
      overflow: hidden;
      background: #000;
    }
    .composition,
    .scene {
      width: {宽度}px;
      height: {高度}px;
      position: relative;
      overflow: hidden;
    }
  </style>
</head>
<body>
  <div class="composition" data-composition-id="scene-01" data-width="{宽度}" data-height="{高度}" data-start="0" data-duration="4">
    <div class="scene"></div>
    <script src="https://cdn.jsdelivr.net/npm/gsap@3.14.2/dist/gsap.min.js"></script>
    <script>
      const tl = gsap.timeline({ paused: true });
      window.__timelines = window.__timelines || {};
      window.__timelines["scene-01"] = tl;
      window.addEventListener("hyperframes:play", function() { tl.play(); });
      window.addEventListener("hyperframes:pause", function() { tl.pause(); });
      window.addEventListener("hyperframes:reset", function() { tl.restart().pause(); });
      setTimeout(function() { if (tl.paused()) tl.play(); }, 200);
    </script>
  </div>
</body>
</html>
```

### Mandatory rules

- `.composition` 是 HyperFrames 识别容器
- `.scene` 只负责视觉内容
- 所有主要动画必须在同一个 GSAP timeline 里
- 禁止 `repeat: -1`
- 禁止把复杂 WebGL、横向翻页、无限循环 canvas 动效从旧 deck 搬进来

---

## 8. Layout And Motion Rules

### Layout

- 先写最终静态画面，再加动画
- 核心内容优先用 `grid` / `flex` / `padding`
- 装饰元素可以 absolute，正文和主视觉尽量在正常布局容器中
- 主题参考来自真实 HTML，布局护栏来自 `references/layout-rules.md`
- 视频场景要留足安全区，不要把小字贴边

### Motion

- 每页 3-6 个主要入场动作足够
- 以 `opacity`、`x/y`、轻微 `scale` 为主
- 需要环境动效时，也必须挂在场景 timeline 上
- 默认不做跨 scene 过渡；每页内部完成自己的节奏即可

---

## 9. Render-Safe CSS Rules

优先：

- 实色背景
- 简单线性或径向渐变
- 高对比文字
- 轻量边框与单层阴影

避免：

- `backdrop-filter`
- `mix-blend-mode`
- 多层 `filter: blur()`
- 复杂 `mask` / SVG filter
- 多个全屏透明层叠加
- 对关键文字和关键 UI 使用低透明度

生成后建议用：

```bash
rg "backdrop-filter|mix-blend-mode|filter:|mask-|clip-path|blur\\(|repeat:\\s*-1" scene-*.html
```

---

## 10. Validation Checklist

### Count and mapping

- `scene-*.html` 数量 = `script.txt` 行数
- `outline.md` 页数 = `script.txt` 行数
- `scene-design-plan.md` 页数 = `script.txt` 行数
- `subtitle-scene-map.md` 行数 = `script.txt` 行数

### Confirmation gates

- 已输出 `structure-proposal.md`
- 用户已确认页结构，或明确授权跳过确认
- 用户已确认主题，或初始需求中已明确指定主题

### HTML integrity

- 每个 HTML 都有 `.composition` 和 `.scene`
- viewport 是固定画布尺寸
- `data-composition-id` / `data-width` / `data-height` / `data-duration` 存在
- 注册 `window.__timelines`
- 包含 `hyperframes:play/pause/reset`
- 没有 `repeat: -1`

### Design quality

- 页面不是字幕截图
- 页面不是文章截图
- 每页有清晰页面目的
- 每页有明确主视觉
- 文字量受控
- 图片保持原始比例
- 长截图按滚动窗口处理，而不是压扁

