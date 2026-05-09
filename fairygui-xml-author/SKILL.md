---
name: fairygui-xml-author
order: 2
description: FairyGUI XML 写作技能包，提供直接编写、修改、排查和校验 FairyGUI 工程 XML 的规则、效果图结构判定方法和组件索引。
---

## 简介

FairyGUI XML 是一套受约束的 UI 配置语言，重点在于正确编写 `package.xml`、`package_branch.xml` 和组件 XML，保证标签、属性、结构、资源和引用都能被 FairyGUI 工程读取。

**注意事项**

- 本技能用于直接写 FairyGUI XML，不依赖 FairyGUI 编辑器操作。
- 编写前先判断目标文件类型：`package.xml`、`package_branch.xml` 或组件 XML。
- 任务来自效果图、截图、`res/效果图`、`切图/效果图`、`界面效果图` 等参考目录时，先判断视觉结构，再写 XML。
- 不要把效果图当作一张大图平铺，也不要把重复数据项写成多套静态节点。
- 不要把 FairyGUI XML 当作 HTML；只能写已确认合法的标签和属性。
- 无法确认标签、属性、资源或跨包引用时，先查项目样本和本技能索引；仍无法确认时标记缺口。
- 新写 XML 时优先复用现有样本的命名、拆分、布尔值、默认值和引用口径。
- 纯切图模式下，不要用 `graph`、文本色块或程序化形状脑补缺失美术。

## 核心特点

- **包与组件分离**：`package.xml` 只声明资源，组件 XML 只写显示结构。
- **引用闭合优先**：`id`、`src`、`pkg`、`fileName`、controller、page、transition 都必须能解析。
- **效果图先判定**：根据视觉内容先拆页面、面板、list、item、按钮、进度条和状态。
- **数据驱动拆分**：重复项、滚动区和互斥状态优先落地为 `list + item + controller + gear`。
- **按需读取文档**：根据任务只读取对应 `references/`、`components/`、`judgment/` 或 `validation/` 文档。

## 默认流程

1. 判断目标文件类型。
2. 收集包 id、资源 id/name/path、对象 id、controller/page、transition、`src/pkg/fileName`、`defaultItem` 和跨包引用。
3. 如果任务来自效果图或截图，先读取效果图判断文档。
4. 按目标文件读取对应参考文档和组件文档。
5. 写 XML 时优先复用项目已有样本。
6. 写完后读取校验文档，检查结构、属性、资源和引用。
7. 交付时说明已验证部分和未验证部分。

## 使用规则

以下规则直接决定 FairyGUI XML 应如何编写：

1. `package.xml` 只写包级资源声明、组件资源、图片资源、发布信息和资源语义。
2. `package_branch.xml` 只写分支相关包配置，不混入组件显示对象。
3. 组件 XML 根节点为 `<component>`，显示对象放在 `<displayList>`。
4. 扩展能力通过根 `extention` 或显示对象子节点表达。
5. 不要把包级资源声明写进组件 `displayList`。
6. 不要把组件显示对象写进 `package.xml`。
7. 不要发明未确认存在的标签或属性。
8. 不要引用不存在的 `id`、`src`、`pkg`、controller、page、transition。
9. 新写 XML 优先使用 canonical 属性名，不优先写 alias。
10. 不要随意改现有 `id`，除非任务本身要求重命名。
11. 命中重复数据项或滚动区域时，使用 `list + item`。
12. 不要用 `image` 伪装按钮、进度条、运行时头像、奖励或广告位。
13. 不要用 `transition` 代替基础状态显隐；状态优先用 controller + gear。
14. 不要把参考图当作运行时节点塞进 `displayList`。

## 效果图组件判定与自检

### 判定规则

1. 页面类型先行判断：游戏 HUD、弹窗、列表页、加载页、结算页、状态 item、商店、设置、排行榜。
2. 2 个以上重复数据单元，或明显可滚动区域，默认使用 `list + item`。
3. 可点击区域必须判断为 Button，优先复用 `IconButton`、`PayButton`、`TextButton` 或同包 `*Button.xml`。
4. 头像、奖励、皮肤、广告图、动态背景、运行时图标优先用 loader 或复用组件。
5. 固定底框、装饰和标题框才优先用 image。
6. 进度、血条、加载条、任务完成度优先用 `extention="ProgressBar"`。
7. 空数据、加载中、可领取、已领取、锁定、选中、禁用等互斥状态必须写 controller + gear。
8. 弹窗、商店、设置、排行榜优先复用公共底框、内框、标题框、关闭按钮、等待弹窗。
9. 可伸缩底框、按钮底、卡片底、标签底、进度槽必须补齐 `package.xml` 九宫格语义。

### 落地要求

1. 命中 list：面板必须写 `<list>` 和 `defaultItem`。
2. 命中 item：必须有独立 item XML，并在 `package.xml` 声明 `<component>`。
3. 命中状态：item 内必须写 controller 和对应 gear。
4. 命中基础控件复用：必须新建/复用基础组件，或说明保留在 item 内的理由。
5. 引用闭环必须包含 `package.xml` 声明、`defaultItem`、`component src`、`fileName`、controller page 和 gear pages。
6. 缺少 item 必需切图时，使用空 loader 占位并说明缺图，不要退回面板平铺静态项。

## 交付标准

只有满足下面条件，才可宣称 XML 已可交付：

1. XML 结构完整。
2. 标签合法。
3. 属性合法。
4. 本地引用闭合。
5. 没有明显重复 id。
6. 没有明显断裂的 controller/page/transition/resource 引用。
7. 用户明确给出的九宫格、平铺、平滑、白模染色信息已写入。
8. 如果效果图命中 list/item/state，已经实际落地组件拆分、`defaultItem`、controller/gear 和 package 声明。

## 索引

以下文档按任务需要读取。

### 包与组件

- [Package XML](references/package-xml.md)：`packageDescription`、`resources`、`publish`、资源声明、九宫格和跨包引用。
- [Component XML](references/component-xml.md)：`<component>`、`displayList`、组件拆分、`designImage`、扩展根组件和最小模板。
- [Package Branch XML](references/package-xml.md)：分支包配置相关写法。

### 效果图判断

- [Screenshot Judgment](judgment/screenshot-judgment.md)：页面类型、重复单元、滚动区域、按钮、loader、image、ProgressBar、controller 和 gear。
- [Component Splitting](judgment/component-splitting.md)：页面根、面板、列表项、基础控件和公共组件拆分边界。

### 显示对象与组件

- [Image](components/image.md)：静态切图、`src`、九宫格语义。
- [Loader](components/loader.md)：运行时资源位、`url="ui://..."`、空 loader 占位。
- [Text](components/text.md)：文本、输入文本、富文本的选择和基础属性。
- [Button](components/button.md)：Button 扩展节点和按钮实例。
- [List](components/list.md)：列表、`defaultItem`、item、滚动和重复项。
- [Controller](components/controller.md)：controller 命名、pages、selected。
- [Gear](components/gear.md)：gearDisplay、gearIcon、gearColor、gearText、gearDisplay2。
- [ProgressBar](components/progressbar.md)：ProgressBar 根组件和实例。
- [Transition](components/transition.md)：最小 transition、闪烁、显隐、缩放。
- [Relation](components/relation.md)：relation、group、pivot、anchor 和布局关系。

### 协议与资源

- [Attribute Reference](references/attribute-reference.md)：标签和属性名合法性。
- [DisplayList Variants](references/displaylist-variants.md)：`displayList` 标签变体，例如 `inputtext`、`treeView`、`loader3d`。
- [Resource Semantics](references/resource-semantics.md)：切图、九宫格、平铺、白模染色和纯切图约束。
- [OpenFairyGUI Derived Notes](references/openfairygui-derived-notes.md)：协议来源说明和可靠边界。

### 校验与诊断

- [Validation Checklist](validation/validation-checklist.md)：总校验清单。
- [Structure Validation](validation/structure-validation.md)：结构、标签、displayList、扩展节点位置。
- [Reference Validation](validation/reference-validation.md)：id、src、pkg、fileName、controller、page、transition 和资源路径引用。
- [Diagnostic Strategy](validation/diagnostic-strategy.md)：错误分级、源码定位、逻辑定位和修复建议格式。
