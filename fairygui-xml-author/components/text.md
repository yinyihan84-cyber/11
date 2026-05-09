# text / inputtext / richtext

## 用途

用于普通文本、输入文本和富文本。根据语义选择正确变体，不要把所有文本都写成普通 `text`。

## 合法位置

文本节点是 `displayList` 显示对象。

## 核心属性

- `id`、`name`
- `text`
- `fontSize`
- `color`
- `align`、`vAlign`
- `strokeColor`、`strokeSize`
- `input`

## 基础片段

```xml
<text id="n0_title" name="title" xy="0,0" size="220,48" fontSize="36" color="#ffffff" align="center" vAlign="middle" text="标题"/>
```

## 结构规则

- 可输入文本优先使用 `inputtext` 变体。
- 富文本需求明确时使用 `richtext`。
- 运行时文案只放预览值，不要把数据列表写死成多套节点。
- 文案跨状态变化时优先使用 `gearText`。

## 注意事项

- 颜色和描边沿用样本口径。
- 不要用文本或色块脑补缺失美术。
