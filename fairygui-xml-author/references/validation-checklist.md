# FairyGUI XML 校验清单

## 目的

交付前用这份清单做自检。目标不是“看起来像”，而是“尽量能被工程读入且引用闭合”。

## 一、XML 语法层

至少确认：

- 标签闭合
- 属性引号完整
- XML 层级无破损
- 没有重复属性

## 二、标签与属性层

至少确认：

- 当前标签属于已知 FairyGUI 节点集合
- 当前标签下的属性都在协议中
- 没有拼错字段名
- 没有把 alias 当唯一标准继续扩散

典型错误：

- `animationName` 被误写成 `animatonName`
- `columnGap` 被误写成 `columGap`
- `tree` 被误当成最终写回标签

## 三、结构层

至少确认：

- 根节点类型正确
- `displayList` 只包含合法对象
- 扩展节点只出现在合法位置
- `relation` / `gear*` / `item` 没有出现在错误父节点下
- 对象 `id` 在同一组件内唯一

## 四、列表与状态机结构层

至少确认：

- 效果图中重复出现的数据单元没有被直接平铺写死在面板组件里
- 可滚动或数量可变的重复单元使用 `list`
- `list defaultItem` 指向已声明的 item 组件
- item 组件 XML 已真实存在，而不是只在分析或计划中提到
- item 组件内有明确 controller 表达互斥状态，而不是复制多套完整节点
- item 的 controller page 和所有 gear pages 闭合
- 任务标题、描述、图标、数量、进度、奖励等运行时数据只作为预览值存在
- item 内引用的基础控件组件，例如进度条，已在 `package.xml` 声明并可解析
- 如果判断需要 list/item/state，本次产物已同时包含面板 list、item XML、package component 声明和 defaultItem 引用

## 五、引用层

至少确认：

- `src` 指向存在的资源
- `pkg + src` 可以解析到存在目标
- `fileName` 与资源匹配
- `relation target` 指向现有对象 id
- `gear controller` 存在
- `gear pages` 属于对应 controller
- `pageController` 和 `selectionController` 存在
- `transition item target` 存在
- `action.controller` / `action.targetPage` 合法

## 六、资源拉伸语义层

至少确认：

- 每个引用图片的 `image` / `loader` 都能解析到 `package.xml` 资源和真实图片文件
- 显示节点的 `size` 与图片原始宽高不一致时，已判断拉伸目的
- 需要保持边缘、描边、圆角、阴影、端帽、槽体、边框、底板或包裹区域形状的切图，已在 `package.xml` 声明 `scale="9grid"` 和 `scale9grid`
- 纯图标、插画、照片、文字图或不可拆分装饰只做等比缩放时，不强行补九宫格
- 非等比缩放但语义无法确认时，已标记待确认，而不是静默省略

不要把资源名当作唯一判断依据。命名只能提供线索，最终以效果图中的视觉角色、显示尺寸和原图结构判断。

## 七、变体层

至少确认：

- 输入文本优先写 `inputtext`
- 树优先写 `list treeView="true"`
- `loader3d` 保持小写 tag 形式
- 动画实例优先沿用当前写回口径 `jta`

## 八、最小交付标准

只有满足下面条件，才可宣称“XML 已可交付”：

- 结构完整
- 标签合法
- 属性合法
- 本地引用闭合
- 没有明显重复 id
- 没有明显断裂的 controller/page/resource/transition 引用

## 九、诊断输出模板

推荐按下面格式报错：

```text
ERROR FGX001 未知属性 "animatonName"
file: <path>
line: <line if known>
column: <column if known>
node: <tag or logical node path>
fix: 改为 canonical 属性 "animation"
```

如果拿不到可靠的行号：

- 不要伪造行列
- 至少报告文件、标签、对象 id、最近唯一标识和修复建议

## 十、保守策略

校验时遇到不确定项：

1. 先查项目样本
2. 再查本技能 references
3. 仍无法确认时，明确标记“不确定”
4. 不要自行创造协议
