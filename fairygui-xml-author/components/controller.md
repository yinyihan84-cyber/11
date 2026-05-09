# controller

## 用途

表达组件内部互斥页面或状态，例如 `state`、`tab`、`loading`、`category`、`selected`。

## 合法位置

controller 写在组件根下，通常位于 `displayList` 前。

## 核心属性

- `name`
- `pages`
- `selected`

## 基础片段

```xml
<controller name="state" pages="0,disabled,1,available,2,completed" selected="0"/>
```

## 命名规则

- `state`：流程状态或交互状态。
- `loading`：loaded / loading。
- `empty`：空数据 / 有数据。
- `tab`、`category`、`region`：页签、分类、地区。
- `type`、`variant`、`rewardKind`：组件内部展示变体。

## 结构规则

- 状态机放在变化发生的最小组件内。
- item 状态放 item，不要放到页面统一控制。
- controller `selected` 必须属于 `pages`。
- gear 的 `pages` 必须属于对应 controller。

## 注意事项

- 不要让一个 controller 同时承担太多无关语义。
- 没有业务语义时先用中性 page，占位后再替换成项目词汇。
