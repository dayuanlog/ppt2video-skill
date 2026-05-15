# 渲染安全 CSS 规则

HyperFrames、浏览器录制和视频合成通常更喜欢确定、简单、可预测的画面。复杂透明、滤镜、混合模式在浏览器里看着还行，导出视频时容易出现色块、闪烁、边缘脏、性能下降或文字变糊。

本文件优先级高于视觉炫技。画面要漂亮，但不能靠高风险 CSS 堆出来。

## 总原则

- 核心内容优先使用实色、高对比、清晰边界。
- 装饰可以轻微透明，但不要影响文字和主体视觉。
- 用结构、留白、字体、色块、线条、图表做设计，不依赖大量透明叠加。
- 动画优先移动位置、透明度、轻微缩放；少用滤镜动画。
- 如果某个效果不确定是否稳定，选择更朴素的实现。

## 推荐使用

### 背景

```css
.scene {
  background: #08111f;
}

.bg-layer {
  background:
    radial-gradient(circle at 80% 20%, rgba(34, 211, 238, .18), transparent 28%),
    linear-gradient(135deg, #0b1220, #08111f);
}
```

规则：
- 1 个实色底 + 1 个线性渐变 + 最多 1-2 个径向渐变。
- 径向渐变透明度建议 `.08` 到 `.24`。
- 不要超过 3 个全屏背景层。

### 卡片和面板

```css
.panel {
  background: #111827;
  border: 1px solid rgba(255, 255, 255, .18);
  box-shadow: 0 24px 60px rgba(0, 0, 0, .28);
}
```

规则：
- 核心卡片背景不低于 `.86` 不透明度，最好用实色。
- 边框可以轻微透明。
- 阴影 1 层即可，最多 2 层。

### 文字

```css
.headline {
  color: #f8fbff;
  text-shadow: 0 18px 50px rgba(0, 0, 0, .28);
}
```

规则：
- 正文和标题不要使用 `opacity < .88`。
- 不要用透明文字承载核心信息。
- 深色背景上，文字颜色尽量接近白色或高亮强调色。
- 文字阴影只能辅助对比，不要做发光字海报效果。

### 动画

```js
tl.from(".headline", { y: 56, opacity: 0, duration: 0.7, ease: "expo.out" }, 0.3);
tl.from(".panel", { x: 42, opacity: 0, duration: 0.55, ease: "power2.out" }, 0.75);
```

规则：
- 推荐动画属性：`x`、`y`、`scale`、`opacity`、`rotation`。
- 不推荐动画属性：`filter`、`backdrop-filter`、复杂 `clip-path`、`box-shadow`。
- 同一个元素不要同时有多个 transform 补间；需要 Ken Burns 或浮动时，用包裹层拆开。

## 谨慎使用

这些不是绝对禁止，但需要控制范围：

- `filter: blur()`：只用于小面积背景光晕，不超过 40px。
- `box-shadow`：最多 2 层，不要巨大多重发光。
- `border-radius`：可以使用，但工具型 UI 建议 0-8px。
- `clip-path`：只用于静态简单形状，不做复杂动画。
- `transform: perspective/rotateY`：用于设备框架可以，但不要叠太多层。

## 禁止或尽量避免

### 大面积 backdrop-filter

```css
/* 避免 */
.glass {
  backdrop-filter: blur(24px);
  background: rgba(255, 255, 255, .08);
}
```

问题：导出时可能出现模糊不一致、性能差、边缘闪烁。

替代：

```css
.panel {
  background: #111827;
  border: 1px solid rgba(255, 255, 255, .16);
}
```

### 多层透明玻璃拟态

```css
/* 避免 */
.card {
  background: rgba(255,255,255,.08);
  box-shadow:
    0 20px 80px rgba(0,0,0,.4),
    inset 0 0 40px rgba(255,255,255,.08);
}
```

问题：层叠后对比不可控，文字容易糊。

替代：使用实色卡片 + 轻边框 + 单层阴影。

### mix-blend-mode

```css
/* 避免用于关键内容 */
.title {
  mix-blend-mode: screen;
}
```

问题：不同渲染管线颜色结果可能不一致。

替代：直接指定高对比颜色。

### 复杂 mask / SVG filter

```css
/* 避免 */
.hero {
  mask-image: radial-gradient(...);
  filter: url(#distortion);
}
```

问题：视频导出兼容性差。

替代：用普通裁切容器、实色遮挡块、简单 overflow hidden。

### 滤镜动画

```js
/* 避免 */
tl.to(".image", { filter: "blur(0px) brightness(1.3)", duration: 1 });
```

问题：性能重，容易出现中间帧闪烁。

替代：用 opacity、位移、缩放表达入场。

## 可接受透明度范围

| 用途 | 建议透明度 |
|------|------------|
| 正文文字 | `1` |
| 次要文字 | `.88` - `1` |
| 边框 | `.14` - `.35` |
| 装饰线 | `.18` - `.45` |
| 背景光晕 | `.08` - `.24` |
| 幽灵文字 | `.035` - `.08` |
| 核心卡片背景 | `.86` - `1` |
| 非核心装饰卡片 | `.18` - `.45` |

## 每页复杂度预算

一个 scene 建议最多：
- 2 个渐变背景。
- 2 个背景装饰元素。
- 1 个幽灵文字。
- 1-2 层阴影。
- 3-6 个主要动画。
- 0 个 `backdrop-filter`。
- 0 个 `mix-blend-mode`。
- 0 个关键内容滤镜。

如果超过预算，必须在 `scene-design-plan.md` 的“渲染安全”字段说明为什么需要。

## 检查命令建议

生成后可以搜索高风险 CSS：

```bash
rg "backdrop-filter|mix-blend-mode|filter:|mask-|clip-path|blur\\(|repeat:\\s*-1" scene-*.html
```

出现结果不一定必须删除，但必须逐项确认：
- 是否用于核心内容？
- 是否有更简单替代？
- 是否影响文字可读？
- 是否会增加导出风险？
