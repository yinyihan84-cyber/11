# package.xml 写法

## 用途

`package.xml` 只负责包级资源声明和发布信息。不要把组件显示对象、坐标布局或 `displayList` 内容写进这里。

## 基础结构

```xml
<?xml version="1.0" encoding="utf-8"?>
<packageDescription id="pkgid01">
  <resources>
    <component id="root01" name="RootView.xml" path="/" exported="true"/>
    <component id="panel01" name="PanelView.xml" path="/"/>
    <image id="bg01" name="panel_bg.png" path="/res/" scale="9grid" scale9grid="12,12,8,8"/>
  </resources>
  <publish name=""/>
</packageDescription>
```

## 结构规则

- 根节点使用 `<packageDescription id="...">`。
- 资源声明放在 `<resources>` 内。
- 组件资源使用 `<component>`，`name` 通常是组件 XML 文件名。
- 图片资源使用 `<image>`，`name` 是真实图片文件名，`path` 是相对包资源目录的路径。
- 组件先在 `package.xml` 声明，组件 XML 中的 `src` / `defaultItem` 才能引用。
- `exported="true"` 只用于需要被外部创建或导出的组件。

## 常用属性

- `id`：包内唯一资源 id。
- `name`：资源文件名或显示名称，组件通常写 XML 文件名。
- `path`：资源所在目录，保留项目已有口径。
- `fileName`：组件实例中用于说明目标组件文件名，不写在资源声明上。
- `scale="9grid"` + `scale9grid="x,y,w,h"`：九宫格切图。
- `gridTile`：平铺切图。
- `smoothing="false"`：禁用平滑。
- `width` / `height`：样本或资源语义需要显式记录时再写。

## 图片资源属性

图片是否九宫格、平铺、禁用平滑，在 `package.xml` 的 `<image>` 资源声明阶段确定。

- 九宫格：`scale="9grid"` + `scale9grid="x,y,w,h"`。
- 平铺：按项目样本写 `gridTile`。
- 禁用平滑：按项目样本或资源语义写 `smoothing="false"`。
- 宽高：样本或资源面板明确记录时写 `width` / `height`。

## 跨包引用

- 本包资源常用 `src="res01"` 或 `url="ui://pkgidres01"`。
- 外部公共包资源必须保留 `pkg`，例如 `pkg="commonpkg" src="button01"`。
- 不要把跨包资源复制成本包资源，除非用户明确要求复制或当前工程无法解析原路径。

## 资源语义

- 面板底、按钮底、槽体、卡片框、进度条底/填充等可伸缩图，显示尺寸与原图不一致时优先检查九宫格。
- 可重复纹理、连续背景、条纹、噪声、格子底等优先检查平铺。
- 图标、插画、照片、徽章主体、文字图通常不要补九宫格。
- 白模资源在 package 阶段只判断语义；最终颜色写在组件显示节点 `color` 上。
- 资源语义不确定时标记“待确认”，不要猜 `scale9grid` 数值。

## 资源禁令

- 不要在未获要求时复制、搬运用户资源。
- 用户已指定资源目录时，`package.xml` 必须优先回源到该目录。
- 用户明确只允许引用切图时，不得用 `graph` 参与视觉还原。
- 缺失切图时，不要擅自补造 UI；反馈缺口，或只生成空 loader 占位。
- 用户要求切图必须走 loader 时，组件中使用 `<loader url="ui://pkgIdresId" fill="scaleFree"/>`，不要使用 `<image src="resId"/>`。
- 无法确认资源语义时写“不确定”，不要猜九宫格、平铺、平滑等属性。

## 协议来源

- 包级标签、属性和资源语义可参考 [attribute-reference.md](attribute-reference.md)、[resource-semantics.md](resource-semantics.md) 和 [openfairygui-derived-notes.md](openfairygui-derived-notes.md)。
- 这些参考文件只用于确认协议，不改变 `package.xml` 只声明资源的边界。

## 注意事项

- 不要为了方便生成而复制用户已有切图目录。
- 不要遗漏 item、ProgressBar、Button 等子组件资源声明。
- `id` 在同一包内必须唯一。
- `path + name` 应能解析到真实落盘文件。
