# 动画模式

本文件只保留当前 `ppt-to-video-skills` 还推荐使用的安全模式。

目标不是“动画花”，而是“节奏清楚、可导出、可重做”。

## 总规则

- 所有主动画都写在同一个 GSAP timeline 中
- 默认只做页内动画，不做跨页过渡
- 避免 `repeat: -1`
- 避免滤镜、mask、shader、复杂 3D 作为主动画
- 优先 `opacity`、`x/y`、轻微 `scale`

## 1. 标题入场

```js
tl.fromTo(
  ".headline",
  { opacity: 0, y: 32 },
  { opacity: 1, y: 0, duration: 0.46, ease: "power3.out" },
  0.3
);
```

适用：

- 开场标题
- 金句页
- 结论页

## 2. 副文本跟进

```js
tl.fromTo(
  ".sub",
  { opacity: 0, y: 18 },
  { opacity: 1, y: 0, duration: 0.24, ease: "power2.out" },
  0.62
);
```

适用：

- 标题后的补充句
- 标签说明

## 3. 卡片组交错入场

```js
tl.fromTo(
  ".card",
  { opacity: 0, y: 22, rotation: -2 },
  { opacity: 1, y: 0, rotation: 0, duration: 0.26, stagger: 0.08, ease: "power2.out" },
  0.74
);
```

适用：

- 三列能力
- 数据卡片
- 白板便签

## 4. 分隔线展开

```js
tl.fromTo(
  ".divider",
  { opacity: 0, scaleX: 0 },
  { opacity: 1, scaleX: 1, duration: 0.2, ease: "power2.out" },
  0.56
);
```

适用：

- 上下递进
- 章节分隔
- 极简发布会风

## 5. 数字或关键词强调

```js
tl.fromTo(
  ".stat",
  { opacity: 0, scale: 0.96, y: 18 },
  { opacity: 1, scale: 1, y: 0, duration: 0.32, ease: "power3.out" },
  0.4
);
```

适用：

- 大数字
- 单关键词聚焦
- 白页强调

## 6. 图片或截图入场

```js
tl.fromTo(
  ".image-frame",
  { opacity: 0, y: 28 },
  { opacity: 1, y: 0, duration: 0.34, ease: "power2.out" },
  0.58
);
```

适用：

- 产品截图
- 证据图
- 对照图

## 7. 长截图缓慢滚动

```js
tl.fromTo(
  ".scroll-image",
  { y: 0 },
  { y: -220, duration: 3.2, ease: "none" },
  0.9
);
```

适用：

- 长网页截图
- 产品长界面

只动图片 `y`，不要动容器高度。

## 8. 推荐组合节奏

一页最常用的顺序：

1. meta / 标签
2. 主标题
3. 分隔线或强调词
4. 卡片 / 图 / 结构

常见时间布局：

- `0.08` meta
- `0.28~0.44` 主标题
- `0.56~0.74` 分隔或补充
- `0.74~1.10` 卡片 / 图 / 结构

## 9. 不再推荐的旧模式

以下模式不再作为当前 skill 的推荐默认值：

- 打字机逐字输入
- `repeat: -1` 环境漂浮
- glitch / shader / thermal distortion
- 依赖 `clip-path` 的复杂过渡
- 跨 scene 转场编排

如果用户明确要做实验性风格，可以单独评估；默认不要启用。
