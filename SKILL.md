---
name: ppt2video-skills
description: 使用 HyperFrames 生成带 GSAP 动画的独立 HTML 视频 PPT scene。收到文章、口播稿、短视频文案或“把这篇内容做成 HTML PPT/视频 PPT”的请求时使用本 skill。默认只生成两个中间文件：structure-proposal.md 用于确认 PPT 页结构、叙事拆分和画面意图；script.txt 用于把原文整理成一行对应一个 scene 的口播脚本。用户确认结构和主题/自定义风格后，再参考 theme-previews 中的真实主题 HTML 或用户自定义风格要求生成 scene-XX.html。避免旧流程中 outline.md、subtitle-scene-map.md、scene-design-plan.md 等多份重复规划文件，除非用户明确要求兼容旧管线。
---

# PPT2Video Skills

## Core Principle

这个 skill 的目标是把文章或口播稿转成可渲染的视频 PPT HTML，而不是把文章切成字幕页。

默认维护两个中间文件：

- `structure-proposal.md`：页级结构确认稿，也是后续 HTML 的设计依据。
- `script.txt`：口播分行文件，每一行严格对应一个 `scene-XX.html`。

---

## Source Of Truth

风格来源有两种合法入口：

1. **选择已有主题**：以当前 skill 目录下的 `theme-previews/` 为准。
   - `theme-previews/all-themes-vertical.html` 是主题预览入口。
   - `theme-previews/<theme-name>/scene-*.html` 是真实样式参考。
   - `references/` 只作为规则护栏和提炼总结使用，优先级低于所选主题目录中的真实 HTML。
2. **用户自定义风格**：用户可以直接描述想要的尺寸、视觉风格、色彩、版式、气质、参考对象或行业感。
   - 自定义风格只影响具体视觉设计，不改变 `structure-proposal.md`、`script.txt`、scene 映射、HyperFrames 结构和校验规则。
   - 自定义风格也必须遵守 `references/` 中的布局、排版、动效、图片和渲染安全护栏。

如果用户选择已有主题，先看现成主题 HTML，再写新 scene；不要先脑补风格，再回头找参考。
如果用户选择自定义风格，先把用户要求整理成明确的风格约束，再生成新 scene；不要让风格自由发挥到破坏可读性、映射关系或渲染稳定性。

### Reference Loading Guide

按需读取 `references/`，不要一次性加载全部文件：

- 做页面构图时读 `references/layout-rules.md`。
- 做字体层级时读 `references/typography.md`。
- 提取或调整颜色时读 `references/color-palettes.md`。
- 安排图片、截图、长截图时读 `references/image-asset-rules.md`。
- 写动画前读 `references/motion-principles.md` 和 `references/animation-patterns.md`。
- 检查导出稳定性时读 `references/render-safe-css.md`。
- 只有用户明确要求转场时，才读 `references/scene-transition.md`。
- 需要快速理解主题家族时读 `references/visual-styles.md`，但最终仍以主题 HTML 为准。

---

## Workflow

除非用户明确说“跳过确认，直接继续”，否则按下面流程执行：

```text
用户提供文章/口播稿
  -> 判断画面尺寸和使用场景
  -> 生成 structure-proposal.md
  -> 等用户确认页结构
  -> 根据确认后的结构生成 script.txt
  -> 校验 script.txt 行数 = 确认后的 scene 数
  -> 让用户选择已有主题，或描述自定义风格要求
  -> 若选择已有主题，读取所选主题目录中的真实 scene HTML
  -> 若选择自定义风格，整理风格约束并按 references 护栏设计
  -> 按 structure-proposal.md + script.txt 生成 scene-XX.html
  -> 做数量、映射、HTML 结构和渲染安全检查
```

### Confirmation Gates

- 未确认 PPT 页结构前，不生成 `scene-XX.html`。
- 未确认已有主题或自定义风格前，不生成 `scene-XX.html`。
- 如果用户已经在初始需求里明确指定尺寸、主题、自定义风格或允许跳过确认，可以直接进入下一步，但要在回复中复述采用的决定。

---

## 0. Determine Canvas Size

开始前先确认尺寸。若用户没指定，按内容场景推荐：

| 尺寸 | 比例 | 适用 |
|---|---|---|
| `1080 x 1440` | 3:4 | 当前 `theme-previews` 竖屏主题默认尺寸 |
| `1080 x 1920` | 9:16 | 短视频竖屏 |
| `1920 x 1080` | 16:9 | 横屏讲解或长视频 |
| `1080 x 1080` | 1:1 | 方图视频 |

如果用户提到 `theme-previews/all-themes-vertical.html`、竖屏参考模板、现成竖屏主题，默认优先使用 `1080 x 1440`。

---

## 1. Create `structure-proposal.md`

收到文案后，第一步生成结构确认稿。它要同时承担“页结构”“口播对应关系”“画面设计意图”三件事，避免拆成多份重复文件。

### Required Content

`structure-proposal.md` 至少包含：

- 核心主题
- 目标观众
- 建议尺寸
- 建议页数
- 风格来源：待选择 / 已有主题 / 自定义风格
- 拆分原则
- scene 表格

推荐格式：

```md
# PPT 结构确认稿

- 核心主题：
- 目标观众：
- 建议尺寸：
- 建议页数：
- 风格来源：
- 拆分原则：

| Scene | 页面角色 | 对应原文范围 | 口播内容预览 | 屏上文字 | 主视觉/构图 | 设计注意 |
|---|---|---|---|---|---|---|
| 01 | 开场钩子 | P01-P03 | 你有没有遇到过领导单独谈话、委婉劝退？ | 被劝退时，先别主动辞 | 大标题 + 情境压迫感 | 第一眼要像观点页，不像字幕页 |
```

### Split Rules

- 一页 PPT 对应一个相对完整的语义单元：观点、问题、例子、步骤、转折、提醒或结论。
- 普通内容页通常对应约 8-20 秒口播。
- 标题页、章节页、金句页可以更短，但必须有明确叙事作用。
- 过长段落按语义边界拆分，过碎段落优先合并。
- 尊重用户原始分段，但允许提出更适合 PPT 的合并和拆分页建议。
- 不要把 10 个字、几秒钟内容机械切成一页。
- 不要把每页设计成文章截图或字幕截图。

### Confirmation Message

输出 `structure-proposal.md` 后停下，等待用户确认。可以这样说：

```text
请先确认 PPT 页结构和文案对应关系。你可以回复“确认”，或告诉我需要合并、拆分、调整哪几页。确认后我再生成 script.txt，并进入主题选择。
```

---

## 2. Create `script.txt`

用户确认结构后，再生成 `script.txt`。

`script.txt` 只处理口播分行，不承担设计说明，不写页码，不写标题，不写编号。

### Scene Count Lock

生成 `script.txt` 前，先从用户确认后的 `structure-proposal.md` 锁定一个唯一总数：

```text
SCENE_COUNT = structure-proposal.md 中确认的 scene 数量
```

生成 `script.txt` 时必须按 `01 -> SCENE_COUNT` 的顺序逐个 scene 写口播，不要自由发挥增删行。每写一行，都要能回答“这一行对应结构表里的哪一个 scene”。

如果某一页没有口播，不能省略这一行，必须写 `[no-voice]` 占位。这样可以保证行号永远不漂移。

### Script Rules

- 每一行对应一个 scene。
- 第 1 行对应 `scene-01.html`，第 2 行对应 `scene-02.html`。
- 行数必须等于 `structure-proposal.md` 中确认的 scene 数量。
- 不要出现空行。
- 不要硬换行；一个 scene 的完整口播必须放在同一行。
- 可以合并原文中过碎的自然段，也可以按语义拆开过长段落。
- 保留原文含义和语气，只做必要的顺句、去重、口播化处理。
- 不要把原文偷偷改写成营销文案、鸡汤文案或另一种观点。
- 无口播页只有在用户明确需要时才允许出现；这时对应行写 `[no-voice]`。

### Script Validation

生成 `script.txt` 后，必须先做数量校验，再进入主题选择或 HTML 生成。校验不通过时，不要继续生成 HTML，先修正 `script.txt`。

PowerShell 校验示例：

```powershell
$scriptLines = Get-Content -LiteralPath ".\script.txt" | Where-Object { $_.Trim() -ne "" }
$sceneRows = Select-String -Path ".\structure-proposal.md" -Pattern '^\|\s*\d+\s*\|'
"script.txt lines: $($scriptLines.Count)"
"structure scenes: $($sceneRows.Count)"
if ($scriptLines.Count -ne $sceneRows.Count) { throw "script.txt 行数和 structure-proposal.md scene 数量不一致" }
```

如果 `structure-proposal.md` 的 scene 表格不是以 `| 01 |`、`| 1 |` 这种形式开头，就用人工方式数确认后的 scene 行，并在回复中明确写出：

```text
已锁定 SCENE_COUNT = 10，因此 script.txt 必须正好 10 行。
```

### Good `script.txt` Example

```txt
你有没有被领导叫去办公室单独谈话、委婉劝退的情况？如果换作是你，当时你会怎么处理？
现实中，很多打工人都很要面子，也很心软。领导一边安抚你，一边给你画饼，很多人当场就动摇了。
但我真心提醒你一句：这一步，千万别随便走。
```

---

## 3. Theme Or Custom Style Selection

`script.txt` 完成并通过行数校验后，让用户选择“已有主题”或“自定义风格”，再生成 HTML。

### Option A: Existing Theme

优先让用户打开当前 skill 目录下的：

```text
theme-previews/all-themes-vertical.html
```

用户返回主题名称、目录名或示例文件名后，再继续。

如果在 Agent、远程环境或聊天环境中，用户可能无法直接通过链接打开 `all-themes-vertical.html`，就不要卡住流程。此时直接列出当前 `theme-previews/` 中实际存在的主题目录，并给每个主题一句简洁介绍，让用户可以根据文字选择。

当前内置主题可按下面方式介绍；如果实际目录有变化，以 `theme-previews/` 当前目录为准：

| 主题目录 | 简洁介绍 |
|---|---|
| `guizang-magazine-demo` | 电子杂志 × 电子墨水，适合观点、案例、图文叙事 |
| `guizang-swiss-demo` | 瑞士国际主义，适合数据、方法论、系统结构 |
| `handdrawn-whiteboard-demo` | 白板手绘极简，适合讲解、拆解、步骤说明 |
| `launch-minimal-demo` | 发布会极简，适合大结论、品牌感、少字强表达 |

给用户的选择提示可以这样写：

```text
你可以打开 theme-previews/all-themes-vertical.html 看效果；如果打不开，也可以直接从下面 4 个主题里选：
1. guizang-magazine-demo：电子杂志 × 电子墨水，适合观点、案例、图文叙事
2. guizang-swiss-demo：瑞士国际主义，适合数据、方法论、系统结构
3. handdrawn-whiteboard-demo：白板手绘极简，适合讲解、拆解、步骤说明
4. launch-minimal-demo：发布会极简，适合大结论、品牌感、少字强表达

或者你也可以直接描述自定义风格，比如“黑底科技感、少字、大标题、蓝绿色强调”。
```

不要在 skill 中硬编码过时主题名。以 `theme-previews/` 当前实际存在的目录为准。

如果用户已经指定主题，在开始生成 HTML 前明确复述一次：

```text
将使用 <theme-name> 主题生成本次 scene。
```

### Option B: Custom Style

用户可以不选择已有主题，而是直接描述自定义风格。自定义风格可以包含：

- 画面气质：科技感、杂志感、白板感、发布会感、纪录片感、商业汇报感等
- 色彩倾向：黑底、白底、品牌色、冷色、暖色、低饱和、高对比等
- 版式偏好：大标题、图文分栏、流程图、数据卡、时间线、卡片组等
- 动效偏好：克制、利落、轻微手绘、发布会式入场等
- 参考对象：某类 App、品牌、海报、PPT 或视频风格

收到自定义风格后，先整理为一段“风格约束”，并在生成 HTML 前复述：

```text
将使用自定义风格生成本次 scene：<一句话总结风格>。
具体约束：背景 <...>，字体 <...>，强调色 <...>，版式 <...>，动效 <...>。
```

自定义风格不是跳过规范。仍然必须遵守：

- `structure-proposal.md` 和 `script.txt` 的映射关系
- HyperFrames 独立 HTML 结构
- 画布尺寸和安全区
- `references/layout-rules.md`
- `references/typography.md`
- `references/color-palettes.md`
- `references/motion-principles.md`
- `references/render-safe-css.md`

---

## 4. Use Theme Previews Or Custom Style Correctly

如果用户选择已有主题，先读取该主题目录下 3-5 个代表性 scene HTML，总结它的真实风格语法，再生成新 scene。

重点提取：

- 背景处理方式
- 主副标题层级
- 颜色角色：bg / fg / accent / muted / line
- 留白和安全区
- 常见构图
- 卡片、分栏、标签、流程线等组件语言
- 动画节奏与常见入场方式

不要照抄：

- 示例文案
- 示例标题
- 某一页的固定结构

要做的是“同一主题家族下的新页面”，不是复制粘贴旧 scene。

同一条视频可以在一个主题家族内使用不同骨架页。不要全片只套一个布局；如果同类布局连续出现，标题层级、构图重心和强调方式要变化。

如果用户选择自定义风格，不读取主题 HTML 作为主参考；改为根据用户要求建立“风格约束”，再用 `references/` 做护栏：

- 先明确背景、字体、颜色、构图、组件、动效的基本规则。
- 每个 scene 可以换骨架，但必须像同一套视觉系统。
- 不要因为自定义风格而堆叠高风险 CSS。
- 不要让风格盖过叙事结构；scene 的页面角色仍来自 `structure-proposal.md`。
- 如果用户的自定义要求和渲染安全冲突，优先选择更稳定的实现，并在回复中说明采用的折中。

---

## 5. Generate HTML Scenes

HTML 生成时只以这两个文件做映射来源：

- `structure-proposal.md`
- `script.txt`

映射关系固定为：

```text
structure-proposal.md 第 NN 个 scene = script.txt 第 NN 行 = scene-NN.html
```

生成 HTML 时以 `SCENE_COUNT` 为循环上限：

```text
for NN in 01..SCENE_COUNT:
  read script.txt line NN
  read structure-proposal.md scene NN
  write scene-NN.html
```

不要根据临时设计灵感额外增加 HTML 页，也不要因为某页文字少就跳过 HTML。需要调整页数时，必须先回到 `structure-proposal.md` 和 `script.txt` 同步修改，再重新校验。

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

### HTML Rules

- `.composition` 是 HyperFrames 识别容器。
- `.scene` 只负责视觉内容。
- 所有主要动画必须在同一个 GSAP timeline 里。
- 禁止 `repeat: -1`。
- 禁止把复杂 WebGL、横向翻页、无限循环 canvas 动效从旧 deck 搬进来。
- 先写最终静态画面，再加动画。
- 核心内容优先用 `grid` / `flex` / `padding`。
- 装饰元素可以 absolute，正文和主视觉尽量在正常布局容器中。
- 视频场景要留足安全区，不要把小字贴边。

---

## 6. Image Asset Rules

如用户提供图片、截图、品牌图或长截图：

1. 先编号并记录在 `structure-proposal.md` 的对应 scene 行中。
2. 判断它的内容类型和证据价值。
3. 再分配到合适页面。
4. 生成 HTML 时保持原始比例，不要压扁。

图片必须服务当前口播页，不要把图当装饰随便塞。

如果输出要被“单文件 HTML 渲染器”直接消费，外部资源可能失效。优先级如下：

1. 单文件场景：字体/小图标/小贴纸优先内嵌 `data:` URL。
2. 多文件场景：资源与 HTML 同目录或稳定相对目录。
3. 远程图床：仅在确认渲染环境支持联网时使用。

不要假设 `localhost`、本地相对路径或工具临时目录一定能正确解析。

---

## 7. Render-Safe CSS Rules

优先使用：

- 实色背景
- 简单线性或径向渐变
- 高对比文字
- 轻量边框与单层阴影

避免使用：

- `backdrop-filter`
- `mix-blend-mode`
- 多层 `filter: blur()`
- 复杂 `mask` / SVG filter
- 多个全屏透明层叠加
- 对关键文字和关键 UI 使用低透明度

生成后建议检查：

```bash
rg "backdrop-filter|mix-blend-mode|filter:|mask-|clip-path|blur\\(|repeat:\\s*-1" scene-*.html
```

---

## 8. Validation Checklist

### Count And Mapping

- `scene-*.html` 数量 = `script.txt` 非空行数。
- `structure-proposal.md` 中的 scene 数量 = `script.txt` 非空行数。
- `scene-NN.html` 的编号连续，不跳号。
- 如果三者数量不一致，停止交付并先修正映射关系。

最终校验示例：

```powershell
$scriptLines = Get-Content -LiteralPath ".\script.txt" | Where-Object { $_.Trim() -ne "" }
$sceneRows = Select-String -Path ".\structure-proposal.md" -Pattern '^\|\s*\d+\s*\|'
$htmlFiles = Get-ChildItem -LiteralPath "." -Filter "scene-*.html"
"script.txt lines: $($scriptLines.Count)"
"structure scenes: $($sceneRows.Count)"
"html scenes: $($htmlFiles.Count)"
if (($scriptLines.Count -ne $sceneRows.Count) -or ($scriptLines.Count -ne $htmlFiles.Count)) {
  throw "scene 数量不一致：请先同步 structure-proposal.md、script.txt 和 scene-*.html"
}
```

### Confirmation Gates

- 已输出 `structure-proposal.md`。
- 用户已确认页结构，或明确授权跳过确认。
- 用户已确认已有主题或自定义风格，或初始需求中已明确指定主题/风格。

### HTML Integrity

- 每个 HTML 都有 `.composition` 和 `.scene`。
- viewport 是固定画布尺寸。
- `data-composition-id` / `data-width` / `data-height` / `data-duration` 存在。
- 注册 `window.__timelines`。
- 包含 `hyperframes:play/pause/reset`。
- 没有 `repeat: -1`。

### Design Quality

- 页面不是字幕截图。
- 页面不是文章截图。
- 每页有清晰页面目的。
- 每页有明确主视觉。
- 文字量受控。
- 图片保持原始比例。
- 长截图按滚动窗口处理，而不是压扁。
