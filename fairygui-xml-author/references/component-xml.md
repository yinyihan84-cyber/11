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
- 组件 XML 第一版不是最终布局；根据效果图写完后，必须二次验收坐标、尺寸、缩放比例、颜色、透明度、层级、状态 gear、资源引用和缺图空 loader，再回改组件 XML。
- 对象 `id` 必须沿用项目导出口径；常见为 `n0`、`n1`、`n2`，语义放 `name`，不要为了可读性自造 `n0_bg`、`n1_btn`。
- 用户项目强制规范：组件 `displayList` 中凡引用文件夹资源里的图片切图，必须写 `<loader url="ui://包id资源id" fill="scaleFree"/>`。不要写 `<image src="资源id"/>` 来承载这些资源，即使 `package.xml` 里资源声明标签是 `<image>`。
- 组件实例的 `src`、列表的 `defaultItem`、loader 的 `url` 必须引用当前工程已有或刚写入 `package.xml` 的真实 id。已有工程中如果 `.objs/workspace.json` 已记录 `ui://包id组件id`，优先复用该组件 id 或同步更新 workspace，避免编辑器恢复打开文档时指向不存在组件。

## 协议规则

- 根 `<component>`、显示对象、结构节点和扩展节点只能写已确认合法的属性。
- 显示对象属性合法性可参考 [attribute-reference.md](attribute-reference.md)。
- `displayList` 标签变体可参考 [displaylist-variants.md](displaylist-variants.md)。
- 协议来源和可靠边界可参考 [openfairygui-derived-notes.md](openfairygui-derived-notes.md)。

## 禁令

- 不要发明未确认存在的标签或属性。
- 不要引用不存在的 `id`、`src`、`pkg`、controller、page、transition。
- 新写 XML 优先使用 canonical 属性名，不优先写 alias。
- 不要随意改现有 `id`，除非任务要求重命名。
- 判断依赖项目样本时，在结论里说明依据来自样本口径。
- 没有真实解析器返回的位置信息时，不伪造“第几行第几列”。

## displayList 写回口径

- 图片切图/文件夹资源图片实例强制写 `loader`，格式为 `<loader id="n0" name="bg" xy="0,0" size="100,100" url="ui://包id资源id" fill="scaleFree"/>`。这是用户项目规范，高于通用协议里的 `<image>` 可用性。
- `package.xml` 里的 `<image>` 只表示图片资源声明；组件显示层仍用 `loader url="ui://..." fill="scaleFree"` 引用它。
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
- 只允许引用切图时，虽然协议允许 `graph` 和 `image`，但不得用 `graph` 或 `<image src="..."/>` 参与文件夹资源图的视觉还原。
- 效果图上有但项目没有对应资源时，用带语义 `name`、坐标和尺寸的空 `loader` 占位，并在交付中说明对应的缺失资源；不要用 `graph`、文本、色块或程序化形状替代。
- 文件夹资源图片必须走 loader；已知资源写 `<loader url="ui://pkgIdresId" fill="scaleFree"/>`，缺失资源写空 `<loader fill="scaleFree"/>` 占位，不要写 `<image src="resId"/>`。
- 缺失资源的空 loader 也必须参与二次验收：`name` 要说明缺什么，`xy`、`size`、缩放方式和层级都要按效果图保留，不能随意放一个零尺寸或粗略占位。
- 详细映射见 [displaylist-variants.md](displaylist-variants.md)。

## 界面内group的划分

从效果图还原 component XML 时，先识别功能组件和复合区域，再写 `displayList`；不要把界面拆成一堆无归属散件。

- 按钮、标题、奖励格、进度条、头像/图标、状态块等复合 UI 应建立语义 `group`。
- `group` 只用于整体移动、显隐和绑定，不是父容器；组内元素仍使用组件内绝对坐标。
- 每个 `group` 必须有一个基准元素，例如底图、头像框、进度条底板、按钮底图。
- 子元素坐标应相对基准元素推导：居中、右下角、左右排列、等距排列等，而不是孤立抄坐标。
- `group` 的 `xy` 和 `size` 由内部元素外接矩形计算得出。
- 对象 `id` 沿用项目样本口径；语义写入 `name`，避免只有 `n31`、`n32` 这类不可维护名称。
- 重复卡片、行、格子必须抽成 item 组件，主界面只放 list；状态差异优先用 controller + gear 管理。

## 拼后视觉验收

任务来自效果图时，组件 XML 初稿完成后必须对照效果图复查：

- 坐标、尺寸、缩放比例是否贴合效果图，尤其是面板、列表、item、按钮、进度条、奖励格和缺图 loader。
- 颜色、透明度、文本描边和可染色资源颜色是否来自效果图；多状态颜色用 controller + gear 表达。不要按文件名枚举判断是否染色，应先看源图主体是否白色或近灰白、再看效果图中该实例目标区域是否非白色。
- 资源引用是否准确；不确定或缺失资源继续使用空 loader，不用相似图片替代。
- 层级和遮挡顺序是否正确；状态节点是否通过 gearDisplay、gearIcon、gearColor、gearText 表达。
- 如果视觉偏差来自九宫格、平铺或平滑，回改 `package.xml`；如果偏差来自布局、颜色、状态或引用，回改组件 XML。

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
- `fileName` 必须与 `package.xml` 中对应 `<component id="...">` 的 `name` 一致。
- `url="ui://..."` 的包 id 必须等于当前 `packageDescription id`，资源 id 必须来自同一 `package.xml`；不要使用示例包 id 或临时包 id。
- 不要随意改已有对象 id。
- 如果项目样本依赖显式默认值，不要擅自删掉。
- 没有样本时，先生成最小闭合结构，再扩展视觉细节。
