# component.xml 写法

## 用途

组件 XML 描述一个 FairyGUI 组件的结构、显示对象、控制器、过渡和扩展能力。组件根必须是 `<component>`。

## 基础结构

```xml
<?xml version="1.0" encoding="utf-8"?>
<component size="828,1656">
  <displayList>
    <component id="n0_panel" name="panel" src="panel01" fileName="PanelView.xml" xy="25,367">
      <relation target="" sidePair="center-center,bottom-bottom"/>
    </component>
  </displayList>
</component>
```

## 结构规则

- 组件根写 `<component>`，常见属性包括 `size`、`extention`、`designImage`、`designImageOffsetX`、`designImageOffsetY`、`designImageAlpha`。
- 显示对象写在 `<displayList>` 内。
- 控制器写在组件根下，通常位于 `displayList` 前。
- `transition` 写在组件根下，引用的 target 必须存在。
- `relation`、`gear*`、扩展子节点写在它们控制的显示对象内部。
- 页面根尽量只挂主面板组件，不要把复杂页面全部堆进根组件。

## 推荐拆分

- 页面根：承载总容器、整体定位关系和入口 controller。
- 主面板：承载背景、标题、关闭按钮、列表或主要内容。
- 列表项：承载 item 自身状态、文案、图标、进度、奖励和按钮。
- 基础控件：可复用按钮、进度条、奖励格、标签等。

## designImage

`designImage` 是编辑器参考图，不是运行时显示对象。

- 不要把参考图塞进 `displayList`。
- 如果样本已有 `designImage`、`designImageOffsetX`、`designImageOffsetY`、`designImageAlpha`，沿用其口径。
- 根据效果图复刻时，可把参考图挂在组件根作为编辑辅助。

## 扩展根组件

扩展组件通过根 `extention` 表达：

```xml
<component size="275,28" extention="ProgressBar">
  <displayList>
    <loader id="n0_bg" name="bg" size="275,28" url="ui://pkgidprogress_bg" fill="scaleFree"/>
    <loader id="n1_bar" name="bar" xy="2,2" size="271,24" url="ui://pkgidprogress_fill" fill="scaleFree"/>
  </displayList>
  <ProgressBar titleType="valueAndmax"/>
</component>
```

## 注意事项

- 组件 XML 中的 `src` 必须能在 `package.xml` 中解析。
- 不要随意改已有对象 id。
- 如果项目样本依赖显式默认值，不要擅自删掉。
- 没有样本时，先生成最小闭合结构，再扩展视觉细节。
