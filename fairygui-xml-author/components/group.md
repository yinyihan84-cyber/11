# Group

## 用途

`group` 是 `component.xml` 的 `displayList` 显示对象，用于把多个同级显示对象绑定为一个可整体移动、显隐、锁定或布局管理的分组。

它不是父容器，不承载子节点。组内元素仍然写在同一个 `displayList` 下，并通过元素自身的 `group="组id"` 绑定到对应 `<group id="...">`。

## 基础片段

```xml
<displayList>
  <loader id="n0" name="btn_bg" xy="100,200" size="180,64" url="ui://pkgidbtn_bg" fill="scaleFree" group="n3"/>
  <text id="n1" name="btn_title" xy="130,218" size="120,30" text="领取" group="n3"/>
  <loader id="n2" name="btn_icon" xy="112,214" size="32,32" url="ui://pkgidicon" fill="scaleFree" group="n3"/>
  <group id="n3" name="reward_button_group" xy="100,200" size="180,64"/>
</displayList>
```

## 结构规则

- `<group>` 写在 `displayList` 内，与被分组的 `loader`、`text`、`component`、`list` 等显示对象同级。
- 成员对象通过 `group="n3"` 指向同组件内存在的 `<group id="n3">`。
- 成员对象继续使用组件内绝对坐标，不改成相对 `<group>` 的局部坐标。
- `group` 的 `xy` 和 `size` 由成员对象外接矩形计算得出。
- 每个语义 `group` 应有一个基准元素，例如底图、头像框、按钮底图、进度条底板。
- `group` 的 `id` 在当前组件内唯一，成员不能引用跨组件的 group。

## 适用场景

- 按钮底图、图标、文本组成的按钮区域。
- 标题框、标题文字、左右装饰组成的标题区域。
- 头像框、头像、等级角标组成的头像区域。
- 奖励底框、奖励图标、数量文本组成的奖励格。
- 进度条底、进度填充、进度文字组成的进度条区域。

## 不适用场景

- 重复卡片、列表行、奖励格矩阵等数据重复区域；这类结构应拆成 `list + item`。
- 需要跨界面复用的完整控件；这类结构应拆成独立 component。
- 互斥状态差异；状态差异优先使用 controller + gear 管理。

## 禁令

- 不要把成员对象嵌套进 `<group>`。
- 不要写 `<group><loader/></group>`、`<group><text/></group>` 这类结构。
- 不要用 `group` 平铺大量重复项来替代 item 组件。
- 不要让成员对象引用不存在的 group id。

错误示例：

```xml
<displayList>
  <group id="n3" name="wrong_group" xy="100,200" size="180,64">
    <loader id="n0" name="btn_bg" xy="100,200" size="180,64"/>
    <text id="n1" name="btn_title" xy="130,218" size="120,30" text="领取"/>
  </group>
</displayList>
```

## 校验点

- `displayList` 中存在被引用的 `<group id="...">`。
- 所有 `group="..."` 引用都指向当前组件内的 group id。
- 同一组件内没有重复 group id。
- `group` 的 `xy` / `size` 覆盖所有成员对象的外接矩形。
- 如果同类结构重复超过 2 个，先判断是否应拆成 item/list，而不是继续增加 group。

