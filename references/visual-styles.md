# 视觉风格说明

本文件现在是历史摘要，不再作为主风格来源。

## 现行规则

生成 scene 时，视觉风格的真实来源是：

- `theme-previews/all-themes-vertical.html`
- `theme-previews/<theme-name>/scene-*.html`

本文件只帮助快速理解当前已存在的主题家族，不替代实际 HTML。

## 当前主题家族

### `guizang-magazine-demo`

- 编辑感
- 衬线标题
- 纸感与期刊留白
- 适合案例、观点、图文叙事

### `guizang-swiss-demo`

- 瑞士风
- 高对比、网格、直角
- 单一强调色
- 适合数据、方法、结构

### `handdrawn-whiteboard-demo`

- 手绘白板
- 纸白底、黑字、柔和蓝标记
- 更像讲解和拆解

### `launch-minimal-demo`

- 发布会极简
- 黑页为主，可插白页
- 大字、少字、强结论

## 使用方式

当用户选中某个主题时：

1. 直接读取该主题目录的真实 scene HTML
2. 提取颜色、排版、留白、组件、动效
3. 在同一家族中生成新页面

不要从这里抄一个抽象风格名，再脱离 HTML 自己脑补。
