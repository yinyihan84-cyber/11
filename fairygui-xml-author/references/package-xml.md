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
- `scale="9grid"` + `scale9grid="x,y,w,h"`：可伸缩容器切图。
- `gridTile`：平铺语义。
- `smoothing="false"`：禁止平滑。
- `width` / `height`：样本或资源语义需要显式记录时再写。

## 跨包引用

- 本包资源常用 `src="res01"` 或 `url="ui://pkgidres01"`。
- 外部公共包资源必须保留 `pkg`，例如 `pkg="commonpkg" src="button01"`。
- 不要把跨包资源复制成本包资源，除非用户明确要求复制或当前工程无法解析原路径。

## 资源语义

- 面板底、按钮底、槽体、卡片框、进度条底/填充等可伸缩图，显示尺寸与原图不一致时优先检查九宫格。
- 图标、插画、照片、徽章主体、文字图通常不要补九宫格。
- 白模资源的颜色写在显示节点 `color` 上，不要修改资源文件。
- 资源语义不确定时标记“待确认”，不要猜 `scale9grid` 数值。

## 注意事项

- 不要为了方便生成而复制用户已有切图目录。
- 不要遗漏 item、ProgressBar、Button 等子组件资源声明。
- `id` 在同一包内必须唯一。
- `path + name` 应能解析到真实落盘文件。
