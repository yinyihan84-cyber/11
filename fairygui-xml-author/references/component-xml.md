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
- 组件 XML 只引用资源，不决定图片资源的九宫格、平铺或禁用平滑语义。
- 对象 `id` 必须沿用项目导出口径；常见为 `n0`、`n1`、`n2`，语义放 `name`，不要为了可读性自造 `n0_bg`、`n1_btn`。
- 图片资源显示实例要跟样本选择 `loader url="ui://包id资源id"` 或 `image src="资源id"`；同一项目不要混用两套口径。

## 协议规则

- 根 `<component>`、显示对象、结构节点和扩展节点只能写已确认合法的属性。
- 显示对象属性合法性可参考 [attribute-reference.md](attribute-reference.md)。
- `displayList` 标签变体可参考 [displaylist-variants.md](displaylist-variants.md)。
- 协议来源和可靠边界可参考 [openfairygui-derived-notes.md](openfairygui-derived-notes.md)。

## 组件禁令

- 不要发明未确认存在的标签或属性。
- 不要引用不存在的 `id`、`src`、`pkg`、controller、page、transition。
- 新写 XML 优先使用 canonical 属性名，不优先写 alias。
- 不要随意改现有 `id`，除非任务要求重命名。
- 判断依赖项目样本时，在结论里说明依据来自样本口径。
- 没有真实解析器返回的位置信息时，不伪造“第几行第几列”。

## displayList 写回口径

- 图片写 `image` 或 `loader`，以项目样本为准；样本使用 `loader url="ui://..." fill="scaleFree"` 时继续使用 loader。
- 普通文本写 `text`。
- 输入文本写 `inputtext`。
- 富文本写 `richtext`。
- 图形写 `graph`。
- 分组写 `group`。
- 普通 Loader 写 `loader`。
- Loader3D 写 `loader3d`。
- 动画资源实例优先写 `jta`。
- 子组件实例写 `component`。
- 普通列表写 `list`。
- 树写 `list treeView="true"`。
- 扩展节点例如 `<Button>`、`<ProgressBar>` 写在对应扩展组件内部。

注意：

- `loader3D` 只作内部变体名，原始 XML 标签仍写 `loader3d`。
- `tree` 是语义变体，不是常规最终写回标签。
- `inputtext` 是输入文本的优先写回口径。
- 只允许引用切图时，虽然协议允许 `graph`，但不得用 `graph` 参与视觉还原。
- 缺图占位优先用空 loader，并在交付中说明对应的缺失资源。
- 用户要求切图必须走 loader 时，已知资源也写 `<loader url="ui://pkgIdresId" fill="scaleFree"/>`，不要写 `<image src="resId"/>`。
- 详细映射见 [displaylist-variants.md](displaylist-variants.md)。

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
