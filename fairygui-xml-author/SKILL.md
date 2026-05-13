---
name: fairygui-xml-author
description: FairyGUI XML 写作技能包，提供直接编写、修改、排查和校验 FairyGUI 工程 XML 的入口规则、参考文档和组件索引。
---

## 简介

FairyGUI XML 是一套受约束的 UI 配置语言，重点在于正确编写 `package.xml` 和组件 XML，保证标签、属性、结构、资源和引用都能被 FairyGUI 工程读取。

**注意事项**

- 本技能用于直接写 FairyGUI XML，不依赖 FairyGUI 编辑器操作。
- 编写前先判断目标文件类型：`package.xml` 或组件 XML。
- 任务来自效果图、截图、`res/效果图`、`切图/效果图`、`界面效果图` 等参考目录时，先判断视觉结构，再写 package/component 初稿，随后必须对照效果图做二次验收并回改 XML。
- .xml文件只能写已确认合法的标签和属性。
- 无法确认标签、属性、资源或跨包引用时，先查项目样本和本技能索引；仍无法确认时标记缺口。
- 新写 XML 时优先复用现有样本的命名、拆分、布尔值、默认值和引用口径。
- 项目样本优先于通用协议；尤其是显示对象标签、资源引用方式、对象 id 形态和组件拆分口径。
- 写入已有 FairyGUI 工程前，必须先识别真实包和资源索引：读取已有 `package.xml`、`.objs/workspace.json`、`.objs/metas/` 和现有组件 XML；`packageDescription id`、资源 `id`、组件 `id` 以工程已有值为准，不要自造 `pkg001`、`root01` 等示例 id。
- 用户项目规范：组件 XML 中引用文件夹资源图片时，必须使用 `<loader url="ui://包id资源id" fill="scaleFree"/>`；不要用 `<image src="资源id"/>` 承载这类切图。
- 效果图上有但项目没有对应切图或资源时，保留结构并使用空 `loader` 占位；不要用 `graph`、文本色块、程序化形状、相似图片或自造图片替代缺失美术。

## 核心特点

- **包与组件分离**：`package.xml` 只声明资源，组件 XML 只写显示结构。
- **引用闭合优先**：`id`、`src`、`pkg`、`fileName`、controller、page、transition 都必须能解析。
- **效果图先判定**：任务来自效果图或截图时，先进入 P2 效果图判断文档，再写 XML。
- **数据驱动拆分**：重复项、滚动区和互斥状态优先落地为 `list + item + controller + gear`。
- **按需读取文档**：根据任务只读取对应 `references/`、`components/`、`judgment/` 或 `validation/` 文档。

## 默认流程

1. 判断目标文件类型。
2. 收集包 id、资源 id/name/path、对象 id、controller/page、transition、`src/pkg/fileName`、`defaultItem` 和跨包引用；已有工程先从 `package.xml` 与 `.objs/workspace.json` 建立 id 映射。
3. 如果任务来自效果图或截图，先读取效果图判断文档，输出组件拆分、资源需求、缺图占位、可染色资源判断和九宫格候选。
4. 按目标文件读取对应参考文档和组件文档。
5. 写第一版 `package.xml` 和组件 XML 时优先复用项目已有样本；没有样本时才使用通用推荐。
6. 第一版写完后，必须把当前 XML 结果与效果图进行二次验收：检查颜色、坐标、尺寸、缩放比例、层级、引用图、九宫格拉伸、可染色资源取色和空 loader 占位。
7. 二次验收发现偏差时，资源语义回改 `package.xml`，显示结构、位置、尺寸、颜色、缩放、状态和引用回改组件 XML；不要把第一版 package/component 当最终依据。
8. 写完后读取校验文档，检查结构、属性、资源、引用和视觉二次验收项。
9. 交付时说明已验证部分、二次验收修正点和未验证部分。

## 交付标准

只有满足下面条件，才可宣称 XML 已可交付：

1. XML 结构完整。
2. 标签合法。
3. 属性合法。
4. 本地引用闭合。
5. 没有明显重复 id。
6. 没有明显断裂的 controller/page/transition/resource 引用。
7. 用户明确给出，或已选资源拉伸验证后能判断的九宫格、平铺、平滑、可染色资源颜色信息已写入。
8. 如果效果图命中 list/item/state，已经实际落地组件拆分、`defaultItem`、controller/gear 和 package 声明。
9. 已按工程真实 `packageDescription id` 校验所有 `ui://` 前缀，并确认 `src`、`defaultItem`、`fileName`、`.objs/workspace.json` 中已打开组件 id 不会指向不存在资源。

## 索引

以下文档按任务需要读取。

### P2 效果图 / 截图 -> FairyGUI 结构判断

- [Screenshot Judgment](judgment/screenshot-judgment.md)：页面类型、重复单元、滚动区域、按钮、loader、image、ProgressBar、controller、gear 和效果图反推九宫格。
- [Component Splitting](judgment/component-splitting.md)：页面根、面板、列表项、基础控件和公共组件拆分边界。

### package.xml / component.xml 如何撰写

- [Package XML](references/package-xml.md)：`packageDescription`、`resources`、`publish`、资源声明、图片资源属性、九宫格、平铺、平滑、可染色资源语义和包级协议来源。
- [Component XML](references/component-xml.md)：`<component>`、`displayList`、显示对象属性、标签变体、组件拆分、`designImage`、扩展节点、controller/gear 和组件级协议来源。

### displayList 节点与扩展组件

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

### 校验与诊断

- [Validation Checklist](validation/validation-checklist.md)：总校验清单。
- [Structure Validation](validation/structure-validation.md)：结构、标签、displayList、扩展节点位置。
- [Reference Validation](validation/reference-validation.md)：id、src、pkg、fileName、controller、page、transition 和资源路径引用。
- [Diagnostic Strategy](validation/diagnostic-strategy.md)：错误分级、源码定位、逻辑定位和修复建议格式。
