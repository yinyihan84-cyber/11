---
name: fairygui-xml-author
description: FairyGUI XML 写作技能包，提供直接编写、修改、排查和校验 FairyGUI 工程 XML 的入口规则、参考文档和组件索引。
---

## 简介

FairyGUI XML 是一套受约束的 UI 配置语言，重点在于正确编写 `package.xml` 和组件 XML，保证标签、属性、结构、资源和引用都能被 FairyGUI 工程读取。

**注意事项**

- 本技能用于直接写 FairyGUI XML，不依赖 FairyGUI 编辑器操作。
- 编写前先判断目标文件类型：`package.xml` 或组件 XML。
- 任务来自效果图、截图、`res/效果图`、`切图/效果图`、`界面效果图` 等参考目录时，先判断视觉结构，再写 XML。
- 不要把效果图当作一张大图平铺，也不要把重复数据项写成多套静态节点。
- 不要把 FairyGUI XML 当作 HTML；只能写已确认合法的标签和属性。
- 无法确认标签、属性、资源或跨包引用时，先查项目样本和本技能索引；仍无法确认时标记缺口。
- 新写 XML 时优先复用现有样本的命名、拆分、布尔值、默认值和引用口径。
- 项目样本优先于通用协议；尤其是显示对象标签、资源引用方式、对象 id 形态和组件拆分口径。
- 效果图上有但项目没有对应切图或资源时，保留结构并使用空 `loader` 占位；不要用 `graph`、文本色块、程序化形状或自造图片替代缺失美术。

## 核心特点

- **包与组件分离**：`package.xml` 只声明资源，组件 XML 只写显示结构。
- **引用闭合优先**：`id`、`src`、`pkg`、`fileName`、controller、page、transition 都必须能解析。
- **效果图先判定**：任务来自效果图或截图时，先进入 P2 效果图判断文档，再写 XML。
- **数据驱动拆分**：重复项、滚动区和互斥状态优先落地为 `list + item + controller + gear`。
- **按需读取文档**：根据任务只读取对应 `references/`、`components/`、`judgment/` 或 `validation/` 文档。

## 默认流程

1. 判断目标文件类型。
2. 收集包 id、资源 id/name/path、对象 id、controller/page、transition、`src/pkg/fileName`、`defaultItem` 和跨包引用。
3. 如果任务来自效果图或截图，先读取效果图判断文档。
4. 按目标文件读取对应参考文档和组件文档。
5. 写 XML 时优先复用项目已有样本；没有样本时才使用通用推荐。
6. 写完后读取校验文档，检查结构、属性、资源和引用。
7. 交付时说明已验证部分和未验证部分。

## 使用规则

以下规则直接决定 FairyGUI XML 应如何编写：

1. `package.xml` 只写包级资源声明、组件资源、图片资源、发布信息和资源语义。
2. 组件 XML 根节点为 `<component>`，显示对象放在 `<displayList>`。
3. 扩展能力通过根 `extention` 或显示对象子节点表达。
4. 不要把包级资源声明写进组件 `displayList`。
5. 不要把组件显示对象写进 `package.xml`。
6. 不要发明未确认存在的标签或属性。
7. 不要引用不存在的 `id`、`src`、`pkg`、controller、page、transition。
8. 新写 XML 优先使用 canonical 属性名，不优先写 alias。
9. 对象 `id` 形态必须跟项目样本一致；常见编辑器导出为 `n0`、`n1`，语义写在 `name`，不要擅自写 `n0_bg` 等自造格式。
10. 命中重复数据项或滚动区域时，使用 `list + item`。
11. 写组件 `displayList` 前必须先查同包或同项目样本确定图片实例口径：样本用 `<loader url="ui://..." fill="scaleFree"/>` 时，所有图片切图实例继续写 `loader`；只有样本明确在 `displayList` 中使用 `<image src="..."/>` 时才写 `image`。不要把 `package.xml` 里的 `<image>` 资源声明误当成组件显示层也应写 `<image>` 的依据。
12. 效果图需要的图片资源不存在时，只生成带语义 `name`、坐标和尺寸的空 `<loader fill="scaleFree"/>` 占位，并在交付说明列出缺失资源；不要用 `graph`、文本、色块或程序化形状自作主张替代。
13. 不要用 `transition` 代替基础状态显隐；状态优先用 controller + gear。
14. 不要把参考图当作运行时节点塞进 `displayList`。
15. 效果图结构判断、组件拆分和落地自检统一查看 P2 判断文档，不在 SKILL 本体展开细则。
16. 为效果图补 `package.xml` 时，图片九宫格、平铺、平滑等资源语义必须结合效果图和显示尺寸判断，不能只看文件名。

## 交付标准

只有满足下面条件，才可宣称 XML 已可交付：

1. XML 结构完整。
2. 标签合法。
3. 属性合法。
4. 本地引用闭合。
5. 没有明显重复 id。
6. 没有明显断裂的 controller/page/transition/resource 引用。
7. 用户明确给出，或效果图与显示尺寸已能判断的九宫格、平铺、平滑、白模染色信息已写入。
8. 如果效果图命中 list/item/state，已经实际落地组件拆分、`defaultItem`、controller/gear 和 package 声明。

## 索引

以下文档按任务需要读取。

### package.xml / component.xml 入口

- [Package XML](references/package-xml.md)：`packageDescription`、`resources`、`publish`、资源声明、图片资源属性、九宫格、平铺、平滑、白模染色、资源语义和包级协议来源。
- [Component XML](references/component-xml.md)：`<component>`、`displayList`、显示对象属性、标签变体、组件拆分、`designImage`、扩展节点、controller/gear 和组件级协议来源。

### P2 效果图 / 截图 -> FairyGUI 结构判断

- [Screenshot Judgment](judgment/screenshot-judgment.md)：页面类型、重复单元、滚动区域、按钮、loader、image、ProgressBar、controller、gear 和效果图反推九宫格。
- [Component Splitting](judgment/component-splitting.md)：页面根、面板、列表项、基础控件和公共组件拆分边界。

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
