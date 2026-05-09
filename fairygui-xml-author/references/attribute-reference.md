# FairyGUI XML 属性速查

## 目的

用这份文档约束 agent 在不同节点下能写哪些属性，并强调 canonical 名优先。

## canonical 优先规则

生成新 XML 时优先使用：

- `animation`，不要优先写 `animationName`
- `skin`，不要优先写 `skinName`
- `prompt`，不要优先写 `promptText`
- `colGap`，不要优先写 `columnGap`
- `lineItemCount`，不要优先写 `lineCount`
- `autoItemSize`，不要优先写 `autoResizeItem`
- `autoPlayRepeat`，不要优先写 `autoPlayTimes`

## `package.xml > resources > image`

当资源是图片切图时，除基础 `id` / `name` / `path` 外，还应关注这些包级属性：

- `scale`
- `scale9grid`
- `width`
- `height`
- `gridTile`
- `smoothing`

常见口径：

- 九宫格按钮：`scale="9grid"` + `scale9grid="x,y,w,h"`
- 平铺图：写 `gridTile`
- 禁止平滑：写 `smoothing="false"`
- 当用户已明确提供切图面板中的宽高时，可同步写 `width` / `height`

## 根节点 `<component>`

常用属性：

- `size`
- `pivot`
- `anchor`
- `margin`
- `restrictSize`
- `overflow`
- `clipSoftness`
- `opaque`
- `mask`
- `reversedMask`
- `hitTest`
- `customData`
- `scroll`
- `scrollBar`
- `scrollBarFlags`
- `scrollBarMargin`
- `scrollBarRes`
- `ptrRes`
- `extention`
- `bgColor`
- `bgColorEnabled`
- `idnum`
- `initName`
- `remark`
- `designImageAlpha`
- `designImageLayer`
- `designImageOffsetX`
- `designImageOffsetY`

## 公共显示对象标识层

常见公共字段：

- `id`
- `name`
- `relation`

## `<component>` 子组件实例

可用常见属性：

- `src`
- `controller`
- `pageController`
- `xy`
- `size`
- `locked`
- `restrictSize`
- `aspect`
- `pivot`
- `anchor`
- `scale`
- `group`
- `rotation`
- `alpha`
- `visible`
- `touchable`
- `grayed`
- `tooltips`
- `customData`
- `fileName`
- `pkg`
- `filter`
- `filterData`

## `<image>`

可用常见属性：

- `src`
- `color`
- `flip`
- `fillMethod`
- `fillOrigin`
- `fillClockwise`
- `fillAmount`
- `xy`
- `size`
- `locked`
- `aspect`
- `pivot`
- `anchor`
- `scale`
- `group`
- `rotation`
- `alpha`
- `visible`
- `grayed`
- `pkg`
- `filter`
- `filterData`

## `<graph>`

可用常见属性：

- `xy`
- `size`
- `locked`
- `restrictSize`
- `pivot`
- `anchor`
- `group`
- `rotation`
- `alpha`
- `visible`
- `touchable`
- `skew`
- `type`
- `lineSize`
- `lineColor`
- `fillColor`
- `corner`
- `points`
- `sides`
- `startAngle`
- `distances`

## `<movieclip>` / `<jta>`

可用常见属性：

- `src`
- `playing`
- `frame`
- `color`
- `xy`
- `size`
- `pivot`
- `group`
- `rotation`
- `alpha`
- `visible`
- `grayed`
- `fileName`
- `pkg`
- `filter`
- `filterData`

## `<loader>`

可用常见属性：

- `xy`
- `size`
- `pivot`
- `scale`
- `group`
- `grayed`
- `url`
- `align`
- `vAlign`
- `fill`
- `shrinkOnly`
- `autoSize`
- `useResize`
- `color`
- `playing`
- `frame`
- `fillMethod`
- `fillOrigin`
- `fillClockwise`
- `fillAmount`
- `clearOnPublish`
- `filter`
- `filterData`

## `<loader3d>`

可用常见属性：

- `xy`
- `size`
- `url`
- `align`
- `vAlign`
- `fill`
- `shrinkOnly`
- `autoSize`
- `animation`
- `skin`
- `playing`
- `frame`
- `loop`
- `color`

## `<text>`

可用常见属性：

- `xy`
- `size`
- `restrictSize`
- `customData`
- `group`
- `font`
- `fontSize`
- `color`
- `align`
- `vAlign`
- `autoSize`
- `singleLine`
- `text`
- `input`
- `ubb`
- `leading`
- `letterSpacing`
- `underline`
- `italic`
- `bold`
- `strikethrough`
- `strokeColor`
- `strokeSize`
- `shadowColor`
- `shadowOffset`
- `autoClearText`
- `demoText`
- `faceDilate`
- `underlaySoftness`
- `vars`
- `prompt`
- `maxLength`
- `restrict`
- `password`
- `keyboardType`

## `<inputtext>`

可用常见属性：

- `prompt`
- `maxLength`
- `restrict`
- `password`
- `keyboardType`

## `<richtext>`

可用常见属性：

- `restrictSize`
- `underlaySoftness`

## `<group>`

可用常见属性：

- `xy`
- `size`
- `locked`
- `group`
- `visible`
- `layout`
- `lineGap`
- `colGap`
- `advanced`
- `excludeInvisibles`
- `autoSizeDisabled`
- `mainGridIndex`

## `<list>`

可用常见属性：

- `src`
- `layout`
- `align`
- `vAlign`
- `lineGap`
- `colGap`
- `lineItemCount`
- `autoItemSize`
- `selectionMode`
- `selectionController`
- `defaultItem`
- `pageController`
- `controller`
- `overflow`
- `scroll`
- `scrollBar`
- `scrollBarFlags`
- `scrollBarMargin`
- `scrollBarRes`
- `ptrRes`
- `margin`
- `clipSoftness`
- `treeView`
- `indent`
- `clickToExpand`
- `autoClearItems`
- `xy`
- `size`
- `group`
- `touchable`

## 扩展节点

### `<Button>`

- `mode`
- `sound`
- `soundVolumeScale`
- `downEffect`
- `downEffectValue`
- `title`
- `selectedTitle`
- `icon`
- `selectedIcon`
- `titleColor`
- `titleFontSize`
- `controller`
- `page`
- `checked`

### `<Label>`

- `title`
- `icon`
- `titleColor`
- `titleFontSize`
- `prompt`

### `<ComboBox>`

- `dropdown`
- `title`
- `icon`
- `visibleItemCount`
- `selectionController`

### `<ProgressBar>`

- `titleType`
- `reverse`
- `value`
- `max`
- `min`

### `<Slider>`

- `titleType`
- `reverse`
- `wholeNumbers`
- `changeOnClick`
- `value`
- `max`
- `min`

### `<ScrollBar>`

- `fixedGripSize`

## 结构节点

### `<relation>`

- `target`
- `sidePair`

### `<gear*>`

- `controller`
- `pages`
- `values`
- `default`
- `tween`
- `positionsInPercent`
- `condition`
- `ease`
- `duration`

### `<controller>`

- `name`
- `pages`
- `selected`

### `<action>`

- `type`
- `fromPage`
- `toPage`
- `transition`
- `repeat`
- `delay`
- `stopOnExit`
- `objectId`
- `controller`
- `targetPage`

### `<transition>`

- `name`
- `autoPlay`
- `autoPlayRepeat`
- `autoPlayDelay`
- `options`
- `fps`

### `<transition><item>`

- `time`
- `target`
- `tween`
- `duration`
- `repeat`
- `yoyo`
- `label`
- `label2`
- `path`
- `ease`
- `type`
- `value`
- `startValue`
- `endValue`

### `<list><item>`

- `title`
- `icon`
- `url`
- `name`
- `selectedTitle`
- `selectedIcon`
- `level`
- `isFolder`
- `controllers`

### `<ComboBox><item>`

- `title`
- `value`
- `icon`

## 使用规则

1. 某属性不在对应节点列表里时，不要写。
2. 读到 alias 可以接受，但重写优先 canonical。
3. 没有现成样本支撑时，不要擅自扩展协议。
