# 组件拆分判断

## 目的

决定一张界面应该拆成哪些 FairyGUI 组件。优先复用原项目样本的拆分粒度；没有样本时按语义拆分。

## 默认拆分

- 页面根：只放总容器、整体定位和入口组件。
- 主面板：放背景、标题、列表、关闭按钮、局部动效。
- 列表项：放状态 controller、gear、任务图标、文案、进度、奖励、状态按钮。
- 基础控件：进度条、按钮、奖励格、标签条等可复用结构。

## 需要拆成子组件的信号

- 同一视觉块重复出现 2 次以上。
- 视觉块在列表、滚动容器或数据驱动区域中出现。
- 同一块存在互斥状态，例如 normal、available、received、locked、selected、disabled。
- 区块内存在可复用按钮、进度条、奖励格、标签条。
- 项目样本已有同类 `Panel.xml`、`Item.xml`、`Progress.xml`、`Button.xml`。

## 可以留在同一组件内的信号

- 只是固定背景、标题、装饰或少量静态文案。
- 没有运行时数据变化。
- 没有滚动、列表、状态切换。
- 不是公共控件，也不会被其他组件复用。

## 命名策略

- 有样本时沿用样本命名。
- 没有样本时使用中性语义名：`RootView.xml`、`PanelView.xml`、`ListItem.xml`、`ProgressView.xml`、`TextButton.xml`。
- 不要照搬技能示例里的业务词汇；按当前界面语义改名。

## 引用闭环

拆分后必须同时闭合：

- `package.xml` 声明所有组件。
- 父组件 `<component src="...">` 指向已声明组件。
- `list defaultItem="ui://pkgiditem01"` 指向 item 组件。
- 跨包公共组件保留 `pkg`。
- `fileName` 与目标组件文件名一致。
