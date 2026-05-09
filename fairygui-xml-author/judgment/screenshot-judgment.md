# 效果图判断

## 目的

根据效果图、截图、`res/效果图`、`切图/效果图`、`界面效果图` 等参考目录或参考图，在写 XML 前判断页面结构。这里做的是“该怎么拆、用什么节点、哪些状态需要 controller”，不是写完后的校验。

不要把效果图当作一张大图平铺。先把视觉内容判定为 FairyGUI 结构，再写 XML。

## 判断顺序

1. 判断页面类型：游戏 HUD、弹窗、列表页、加载页、结算页、状态 item、商店、排行榜、设置页。页面类型决定根组件、面板、列表、按钮和状态机拆分边界。
2. 找重复单元：同一视觉单元出现 2 次以上，且只是文案、图标、进度、奖励、按钮状态不同，默认拆成 item 组件。
3. 找滚动区域：重复单元在裁切窗口内、底部有被截断下一条、或数量明显可变，默认使用 `list + defaultItem`。
4. 找互斥状态：未完成、可领取、已领取、锁定、选中、禁用、空数据、加载中等，默认使用 controller + gear。
5. 找运行时数据位：头像、奖励、皮肤、广告图、动态图标、标题、数量、进度值等运行时变化内容，XML 中只放预览值。
6. 找可点击区域：按钮、页签、关闭按钮、领取按钮优先使用 Button 或现有按钮组件，尤其是 `IconButton`、`PayButton`、`TextButton` 或同包 `*Button.xml`。
7. 找进度区域：进度、血条、加载条、任务完成度优先使用 ProgressBar。
8. 找公共结构：弹窗、商店、设置、排行榜优先复用公共底框、内框、标题框、关闭按钮、等待弹窗。
9. 找可伸缩资源：底框、按钮底、卡片底、标签底、进度槽必须回到 `package.xml` 判断或补充九宫格语义。

## 静态拼图还是数据驱动组件

根据效果图写 XML 前，必须先判断它是“静态拼图”还是“数据驱动组件”。

- 找重复：同一视觉单元重复出现 2 次以上，且只是文案、图标、进度、奖励、按钮状态不同，默认抽成 item 组件。
- 找滚动：重复单元位于裁切窗口内、底部有被截断的下一条、或数量显然可能超过当前可见区域，默认使用 list。
- 找状态：同一 item 存在未完成、待领取、已完成、锁定、选中、禁用、不同奖励展示等互斥视觉结果，默认在 item 内建立 controller + gear。
- 找子控件：item 内有进度条、按钮、标签、奖励格、等级角标等可复用块，优先独立成子组件或复用已有组件。
- 找数据位：标题、描述、数量、进度值、图标、奖励、等级等运行时变化内容，只在 XML 中放预览值；不要为每条数据写死一套结构。

## 节点选择判断

- 固定底框、装饰、标题框：优先 `image`。
- 头像、奖励、皮肤、广告图、动态背景、广告位、动态图标：优先 `loader` 或复用组件。
- 可点击区域：优先 Button，不要只写静态 image。
- 进度、血条、加载条、任务完成度：优先 `extention="ProgressBar"`。
- 可滚动数据集合：优先 `list`。
- 少量固定装饰且无状态变化：可以留在单个组件内。

只有效果图明确是少量固定装饰、没有重复数据单元、没有滚动或状态变化时，才允许把内容直接写在一个组件里。

## 命中 list/item/state 后必须落地

只判断“应该用 list / item / 状态机”不算完成。命中后本次产物必须包含：

- 面板组件中的 `<list>`。
- `list defaultItem` 指向 item 组件。
- 独立 item XML。
- `package.xml` 中声明 item 组件。
- item 内 controller 和 gear 表达互斥状态，不要复制多套 item 节点假装状态。
- item 内引用的基础组件在 `package.xml` 声明。
- 相关 `fileName`、controller page、gear pages 全部闭合。

如果缺少必需切图，只能留空 `loader` 并说明缺图，不要退回到面板里写死多条静态项。

## 默认文件关系

命中 `list + item` 结构时，默认生成或维护这组关系：

- 页面根：只挂主面板组件。
- 主面板：包含背景、标题、关闭按钮、装饰、list。
- Item 组件：包含状态 controller、gear、任务图标、文案、进度、奖励、状态按钮。
- 基础控件组件：例如进度条，必要时由 item 组件引用。

如果目标工程已有同类 `GameTaskPanel.xml`、`TaskItem.xml`、`TaskProgress.xml` 等样本，优先沿用其拆分和命名风格；如果没有样本，则按语义生成中性文件名，例如 `Panel.xml`、`ListItem.xml`、`Progress.xml`。

## 强制落地自检

- 命中 list：面板组件中必须写 `<list>`，并设置 `defaultItem`。
- 命中 item 组件：必须新建或维护独立 item XML，并在 `package.xml` 中声明对应 `<component>` 资源。
- 命中 item 状态：item XML 必须写 controller，并用 `gearDisplay` / `gearIcon` / `gearColor` / `gearText` 等表达互斥状态。
- 命中基础控件：进度条、按钮、奖励格等在多个 item 或状态中复用时，必须新建/复用基础组件，或明确说明为什么保持在 item 内更合适。
- 引用闭环：`package.xml` 声明页面、面板、item、基础控件组件；主面板中的 `list defaultItem` 指向 item 组件的 `ui://包id组件id`；item 内 `<component src="...">` 指向基础控件组件；controller 名、page id、gear pages 全部在 item 组件内闭合。

## 结构判断输出

写 XML 前先输出极短判断：

- 页面类型
- 组件拆分
- 需要的 controller
- 需要的 package 资源声明
- 缺失资源或待确认语义

示例：

```text
结构判断：弹窗列表页；拆为 RootView + TaskPanel + TaskItem + TaskProgress；TaskItem 需要 state controller；列表使用 defaultItem；奖励图标使用 loader；进度使用 ProgressBar。
```
