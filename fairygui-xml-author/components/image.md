# image

## 用途

仅用于项目样本明确在组件 `displayList` 中使用 `<image src="..."/>` 的固定静态切图。不要因为 `package.xml` 里用 `<image>` 声明资源，就推断显示对象也应该写 `<image>`。

## 合法位置

`image` 是 `displayList` 显示对象。资源本体必须先在 `package.xml` 中以 `<image>` 声明。

## 核心属性

- `id`、`name`
- `src`
- `xy`、`size`
- `scale`、`scale9grid`
- `color`
- 公共显示对象属性见 `attribute-reference.md`

## 基础片段

```xml
<image id="n0_bg" name="bg" src="panel_bg" xy="0,0" size="640,480"/>
```

## 结构规则

- `src` 指向 `package.xml` 中的图片资源 id。
- 固定切图也必须先看项目样本；样本显示层用 `loader url="ui://..." fill="scaleFree"` 时，新 XML 必须继续用 `loader`。
- 只有样本明确在 `displayList` 中写 `<image src="..."/>` 时，才使用 `image`。
- 可伸缩底框的九宫格语义写在 `package.xml` 资源声明中。
- 白模染色可在显示节点写 `color="#RRGGBB"`。

## 注意事项

- 不要用 `image` 伪装按钮、进度条、头像、奖励或广告位。
- 不要把参考图当作正式 `image` 节点。
- 不要在同一项目中无依据地混用 image 和 loader 两种图片实例口径。
