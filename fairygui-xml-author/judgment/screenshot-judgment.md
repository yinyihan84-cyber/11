# 效果图判断

## 目的

根据效果图、截图、切图目录或参考图，在写 XML 前判断页面结构。这里做的是“该怎么拆、用什么节点、哪些状态需要 controller”，不是写完后的校验。

## 判断顺序

1. 判断页面类型：HUD、弹窗、列表页、加载页、结算页、状态 item、商店、排行榜、设置页。
2. 找重复单元：同一视觉单元出现 2 次以上，且只是文案、图标、进度、奖励、按钮状态不同，默认拆成 item 组件。
3. 找滚动区域：重复单元在裁切窗口内、底部有被截断下一条、或数量明显可变，默认使用 `list + defaultItem`。
4. 找互斥状态：未完成、可领取、已领取、锁定、选中、禁用、空数据、加载中等，默认使用 controller + gear。
5. 找运行时数据位：头像、奖励、皮肤、广告图、动态图标、标题、数量、进度值等运行时变化内容，XML 中只放预览值。
6. 找可点击区域：按钮、页签、关闭按钮、领取按钮优先使用 Button 或现有按钮组件。
7. 找进度区域：进度、血条、加载条、任务完成度优先使用 ProgressBar。

## 节点选择判断

- 固定底框、装饰、标题框：优先 `image`。
- 头像、奖励、皮肤、广告位、动态图标：优先 `loader`。
- 可点击区域：优先 Button，不要只写静态 image。
- 进度、血条、加载条：优先 ProgressBar。
- 可滚动数据集合：优先 `list`。
- 少量固定装饰且无状态变化：可以留在单个组件内。

## 命中 list/item/state 后必须落地

只判断“应该用 list / item / 状态机”不算完成。命中后本次产物必须包含：

- 面板组件中的 `<list>`。
- `list defaultItem` 指向 item 组件。
- 独立 item XML。
- `package.xml` 中声明 item 组件。
- item 内 controller 和 gear 表达互斥状态。
- item 内引用的基础组件在 `package.xml` 声明。

如果缺少必需切图，只能留空 `loader` 并说明缺图，不要退回到面板里写死多条静态项。

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
