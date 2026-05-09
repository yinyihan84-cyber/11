# 效果图判断

## 目的

根据效果图、截图、`res/效果图`、`切图/效果图`、`界面效果图` 等参考目录或参考图，在写 XML 前判断页面结构。这里做的是“该怎么拆、用什么节点、哪些状态需要 controller”，不是写完后的校验。

不要把效果图当作一张大图平铺。先把视觉内容判定为 FairyGUI 结构，再写 XML。

## P2 判定规则

1. 判断页面类型：游戏 HUD、弹窗、列表页、加载页、结算页、状态 item、商店、排行榜、设置页。页面类型决定根组件、面板、列表、按钮和状态机拆分边界。
2. 找重复单元：同一视觉单元出现 2 次以上，且只是文案、图标、进度、奖励、按钮状态不同，默认拆成 item 组件。
3. 找滚动区域：重复单元在裁切窗口内、底部有被截断下一条、或数量明显可变，默认使用 `list + defaultItem`。
4. 找互斥状态：未完成、可领取、已领取、锁定、选中、禁用、空数据、加载中等，默认使用 controller + gear。
5. 找运行时数据位：头像、奖励、皮肤、广告图、动态图标、标题、数量、进度值等运行时变化内容，XML 中只放预览值。
6. 找可点击区域：按钮、页签、关闭按钮、领取按钮优先使用 Button 或现有按钮组件，尤其是 `IconButton`、`PayButton`、`TextButton` 或同包 `*Button.xml`。
7. 找进度区域：进度、血条、加载条、任务完成度优先使用 ProgressBar。
8. 找公共结构：弹窗、商店、设置、排行榜优先复用公共底框、内框、标题框、关闭按钮、等待弹窗。
9. 找可伸缩资源：底框、按钮底、卡片底、标签底、进度槽必须结合效果图视觉角色、切图原始尺寸和 XML 显示尺寸，回到 `package.xml` 判断或补充九宫格语义。

## 静态拼图还是数据驱动组件

根据效果图写 XML 前，必须先判断它是“静态拼图”还是“数据驱动组件”。

1. 重复：同一视觉单元出现 2 次以上，默认抽成 item。
2. 滚动：重复单元在裁切窗口内、底部截断或数量可变，默认使用 list。
3. 状态：同一 item 存在互斥视觉结果，默认写 controller + gear。
4. 子控件：进度条、按钮、标签、奖励格、等级角标优先复用或拆成子组件。
5. 数据位：标题、描述、数量、进度、图标、奖励、等级只放预览值。

## 节点选择判断

- 固定底框、装饰、标题框、按钮底、进度条、图标等凡引用文件夹资源图片：强制使用 `<loader url="ui://包id资源id" fill="scaleFree"/>`。
- 头像、奖励、皮肤、广告图、动态背景、广告位、动态图标：同样使用 `loader` 或复用组件；缺失资源时保留空 `loader` 占位。
- 可点击区域：优先 Button，不要只写静态 image。
- 进度、血条、加载条、任务完成度：优先 `extention="ProgressBar"`。
- 可滚动数据集合：优先 `list`。
- 少量固定装饰且无状态变化：可以留在单个组件内。

只有效果图明确是少量固定装饰、没有重复数据单元、没有滚动或状态变化时，才允许把内容直接写在一个组件里。

## Package XML 九宫格判断

根据效果图写或补 `package.xml` 时，不能只按文件名声明图片；需要先判断图片在界面里的伸缩语义。

1. 效果图中作为面板底、按钮底、卡片底、标签底、进度槽、边框、端帽、圆角底框的图片，如果组件显示尺寸与切图原始尺寸不一致，优先在 `package.xml` 的 `<image>` 上写 `scale="9grid"` 和 `scale9grid`。
2. 效果图中图标、插画、头像、照片、文字图、徽章主体、不可拆分装饰，即使被放大缩小，也不要因为尺寸变化直接补九宫格；优先按等比缩放、裁切或 loader 语义处理。
3. 如果效果图表现为连续纹理、条纹、格子、噪声背景，需要铺满区域时，优先判断 `scale="tile"`，不要误写成九宫格。
4. 无法从效果图和样本确定九宫格数值时，必须标记待确认；可以判断“需要九宫格”，但不能编造 `scale9grid` 数值。
5. 九宫格语义写在 `package.xml` 图片资源声明，不写在组件 XML 的 `image` / `loader` 显示节点上。

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

有 `GameTaskPanel.xml`、`TaskItem.xml`、`TaskProgress.xml` 等样本时沿用样本；没有样本时用 `Panel.xml`、`ListItem.xml`、`Progress.xml` 等中性命名。

## 强制落地自检

1. 命中 list：面板组件必须写 `<list>` 和 `defaultItem`。
2. 命中 item：必须有独立 item XML，并在 `package.xml` 声明。
3. 命中状态：item XML 必须写 controller 和 `gearDisplay` / `gearIcon` / `gearColor` / `gearText`。
4. 命中基础控件复用：必须新建/复用基础组件，或说明保留在 item 内的理由。
5. 引用闭环：`package.xml`、`defaultItem`、`component src`、`fileName`、controller page、gear pages 必须全部闭合。

## 结构判断输出

写 XML 前先输出极短判断：

- 页面类型
- 组件拆分
- 需要的 controller
- 需要的 package 资源声明，包含可从效果图判断的九宫格或平铺资源
- 缺失资源或待确认语义

示例：

```text
结构判断：弹窗列表页；拆为 RootView + TaskPanel + TaskItem + TaskProgress；TaskItem 需要 state controller；列表使用 defaultItem；奖励图标使用 loader；进度使用 ProgressBar。
```
