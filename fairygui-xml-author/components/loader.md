# loader

## 用途

用于运行时可替换资源位，例如头像、奖励、皮肤、广告图、动态图标、远程图和缺图占位。

## 合法位置

`loader` 是 `displayList` 显示对象。

## 核心属性

- `id`、`name`
- `url`
- `xy`、`size`
- `fill`
- `color`
- `gearIcon`

## 基础片段

```xml
<loader id="n0_icon" name="icon" xy="12,12" size="96,96" url="ui://pkgidicon01" fill="scaleFree"/>
```

## 结构规则

- 本包资源可写完整 `ui://包id资源id`。
- 缺图占位可保留空 `loader`，但名称应表达缺什么，并保留效果图推断出的坐标和尺寸。
- 状态换图优先在 loader 内写 `gearIcon`，不要复制多份节点。

## 注意事项

- 纯切图模式且用户要求“不直接写 image”时，视觉资源统一用 `loader`。
- 效果图上有但项目没有对应资源时，生成空 `loader` 占位；不要用 `graph`、文本、色块或程序化形状替代缺失图片，除非用户明确允许。
