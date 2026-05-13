# ProgressBar

## 用途

用于进度、血条、加载条、任务完成度。只有确认是纯静态装饰时，才保留普通图片结构。

## 合法位置

ProgressBar 可以是扩展根组件，也可以通过组件实例引用。

## 核心属性

- `value`
- `max`
- `titleType`

## 根组件片段

```xml
<component size="275,28" extention="ProgressBar">
  <displayList>
    <loader id="n0_bg" name="bg" size="275,28" url="ui://pkgidprogress_bg" fill="scaleFree"/>
    <loader id="n1_bar" name="bar" xy="2,2" size="271,24" url="ui://pkgidprogress_fill" fill="scaleFree"/>
    <text id="n2_value" name="value" size="275,35" fontSize="25" color="#ffffff" align="center" vAlign="middle" text="5/20"/>
  </displayList>
  <ProgressBar titleType="valueAndmax"/>
</component>
```

## 实例片段

```xml
<component id="n0_progress" name="progress" src="progress01" fileName="ProgressView.xml" xy="138,145" size="274,28">
  <ProgressBar value="24" max="100"/>
</component>
```

## 注意事项

- 进度槽和填充被缩放使用时，按资源语义做普通拉伸验证，再判断九宫格、平铺、裁切或待确认。
- 填充条如果源图主体为白色/近白/近灰且效果图目标区域非白，颜色写在 loader 上；目标仍白时不写 `color`。
- 复用进度条时确保 `package.xml` 已声明组件。
