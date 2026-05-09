# image

## 用途

仅用于非文件夹资源图、或项目样本明确证明 FairyGUI 编辑器可读的特殊 `<image src="..."/>` 场景。用户项目规范下，凡引用文件夹资源里的图片切图，组件 `displayList` 必须使用 `<loader url="ui://包id资源id" fill="scaleFree"/>`，不要因为 `package.xml` 里用 `<image>` 声明资源，就推断显示对象也应该写 `<image>`。

## 合法位置

`image` 是 `displayList` 显示对象，但不是本项目文件夹资源图片的默认承载方式。文件夹资源图片的资源本体仍在 `package.xml` 中以 `<image>` 声明，组件显示层用 `loader` 引用。

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
- 固定切图、按钮底、面板底、装饰、图标等只要来自文件夹资源，必须写 `loader url="ui://..." fill="scaleFree"`。
- 只有资源不是文件夹资源图，且项目样本明确在 `displayList` 中写 `<image src="..."/>` 并能被编辑器读取时，才使用 `image`。
- 可伸缩底框的九宫格语义写在 `package.xml` 资源声明中。
- 白模染色可在显示节点写 `color="#RRGGBB"`。

## 注意事项

- 不要用 `image` 承载文件夹资源图片，也不要用 `image` 伪装按钮、进度条、头像、奖励或广告位。
- 不要把参考图当作正式 `image` 节点。
- 不要在同一项目中无依据地混用 image 和 loader 两种图片实例口径。
