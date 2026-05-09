---
name: fairygui-xml-author
description: 直接编写、修改、排查和校验 FairyGUI 工程 XML。用于根据界面需求手写 `component.xml`、`package.xml`、`package_branch.xml`，根据现有 FairyGUI 样本补全 `displayList`、扩展节点、控制器、过渡和资源引用，或在 XML 打不开、字段不合法、id/控制器/页面/资源引用断裂时进行诊断。适用于“不要进编辑器，直接让 agent 写 FairyGUI XML 拼界面”的场景。
---

# FairyGUI XML 写作

## 核心目标

把 FairyGUI XML 当作受约束的配置语言来写，优先保证：

- 标签合法
- 属性合法
- 结构合法
- 引用闭合
- 输出能被 FairyGUI 工程读取

不要把 FairyGUI XML 当作自由 HTML。无法确认标签、属性、资源或跨包引用时，先查样本和参考文档；仍无法确认时明确标记缺口，不要硬写。

## 默认流程

1. 判断目标文件类型：`package.xml`、`package_branch.xml` 或组件根 XML。
2. 收集上下文：包 id、资源 id/name/path、对象 id、controller/page、transition、`src/pkg/fileName`、`defaultItem`、跨包引用。
3. 如果任务来自效果图、截图或切图目录，先读判断文档，不要直接写 XML。
4. 按目标文件读取 `references/` 和 `components/` 中的具体写法。
5. 写 XML 时优先复用现有样本的命名、拆分、布尔值、默认值和引用口径。
6. 写完后读取 `validation/` 中的校验文档，检查结构、属性、资源、引用和诊断输出。
7. 交付时说明已验证部分和未验证部分。

## 文件类型判断

- `package.xml`：只写包级资源声明、组件资源、图片资源、发布信息和资源语义。
- `package_branch.xml`：只写分支相关包配置，不混入组件显示对象。
- 组件 XML：根节点为 `<component>`，显示对象放在 `<displayList>`，扩展能力通过根 `extention` 或显示对象子节点表达。

不要把包级资源声明写进组件 `displayList`，也不要把组件显示对象写进 `package.xml`。

## 按需读取索引

### 写 package

读取 [references/package-xml.md](references/package-xml.md)。

用于确认：

- `packageDescription`、`resources`、`publish`
- `<component>`、`<image>` 等资源声明
- `id/name/path/fileName/exported`
- 九宫格、平铺、平滑、资源宽高
- 当前包与跨包引用闭合

### 写 component

读取 [references/component-xml.md](references/component-xml.md)。

用于确认：

- `<component>` 根结构
- `displayList`
- 页面根、主面板、列表项、基础控件拆分
- `designImage`
- `extention="Button"` / `ProgressBar` 等扩展根组件
- 最小可打开组件模板

### 判断效果图和组件拆分

效果图判断不属于校验。它发生在写 XML 前，用来决定结构。

- 读 [judgment/screenshot-judgment.md](judgment/screenshot-judgment.md)：根据效果图判断页面类型、重复单元、滚动区域、按钮、loader、image、ProgressBar、controller 和 gear。
- 读 [judgment/component-splitting.md](judgment/component-splitting.md)：判断页面根、面板、列表项、基础控件和公共组件的拆分边界。

### 写具体节点或扩展组件

按节点读取 `components/` 子文档：

- [components/image.md](components/image.md)：静态切图、`src`、九宫格语义。
- [components/loader.md](components/loader.md)：运行时资源位、`url="ui://..."`、空 loader 占位。
- [components/text.md](components/text.md)：文本、输入文本、富文本的选择和基础属性。
- [components/button.md](components/button.md)：Button 扩展节点和按钮实例。
- [components/list.md](components/list.md)：列表、`defaultItem`、item、滚动和重复项。
- [components/controller.md](components/controller.md)：controller 命名、pages、selected。
- [components/gear.md](components/gear.md)：gearDisplay、gearIcon、gearColor、gearText、gearDisplay2。
- [components/progressbar.md](components/progressbar.md)：ProgressBar 根组件和实例。
- [components/transition.md](components/transition.md)：最小 transition、闪烁、显隐、缩放。
- [components/relation.md](components/relation.md)：relation、group、pivot、anchor 和布局关系。

### 查协议和属性

- [references/attribute-reference.md](references/attribute-reference.md)：确认标签和属性名是否合法。
- [references/displaylist-variants.md](references/displaylist-variants.md)：确认 `displayList` 标签变体，例如 `inputtext`、`treeView`、`loader3d`。
- [references/resource-semantics.md](references/resource-semantics.md)：确认切图、九宫格、平铺、白模染色和纯切图约束。
- [references/openfairygui-derived-notes.md](references/openfairygui-derived-notes.md)：查看来自 OpenFairyGUI 的协议来源说明和可靠边界。

### 校验和诊断

写完后读取 `validation/`，不要把效果图结构判断归入这里。

- [validation/validation-checklist.md](validation/validation-checklist.md)：总校验清单。
- [validation/structure-validation.md](validation/structure-validation.md)：结构、标签、displayList、扩展节点位置。
- [validation/reference-validation.md](validation/reference-validation.md)：id、src、pkg、fileName、controller、page、transition 和资源路径引用。
- [validation/diagnostic-strategy.md](validation/diagnostic-strategy.md)：错误分级、源码定位、逻辑定位和修复建议格式。

## 不可违反的规则

1. 不要发明未确认存在的标签。
2. 不要发明未确认存在的属性。
3. 不要引用不存在的 `id`、`src`、`pkg`、controller、page、transition。
4. 新写 XML 时优先使用 canonical 属性名，不优先写 alias。
5. 不要随意改现有 `id`，除非任务本身要求重命名。
6. 不要把重复数据项平铺成多套静态节点；命中列表结构时使用 `list + item`。
7. 不要用 `image` 伪装按钮、进度条、运行时头像、奖励或广告位。
8. 不要用 `transition` 代替基础状态显隐；状态优先用 controller + gear。
9. 不要把参考图当作运行时节点塞进 `displayList`。
10. 纯切图模式下，不要用 `graph`、文本色块或程序化形状脑补缺失美术。

## 交付标准

只有满足下面条件时，才可以说 XML 已可交付：

- XML 结构完整
- 标签合法
- 属性合法
- 本地引用闭合
- 没有明显重复 id
- 没有明显断裂的 controller/page/transition/resource 引用
- 用户明确给出的九宫格、平铺、平滑、白模染色信息已写入
- 如果效果图命中 list/item/state，已经实际落地组件拆分、`defaultItem`、controller/gear 和 package 声明

如果任何一步没法验证，在结论里显式说明未验证原因。
