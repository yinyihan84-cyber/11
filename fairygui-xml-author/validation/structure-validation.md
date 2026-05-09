# 结构校验

## 目的

检查 XML 结构本身是否合法。这里不负责判断效果图应该如何拆，只检查已经写出的结构是否符合 FairyGUI XML 规则。

## component 根

- 根节点是 `<component>`。
- 根 `size`、`extention`、`designImage*` 等属性符合协议。
- 扩展根组件的 `extention` 与根下扩展子节点一致，例如 `extention="ProgressBar"` 配 `<ProgressBar/>`。

## displayList

- `displayList` 中只出现合法显示对象标签。
- `input="true"` 的文本应使用 `inputtext` 变体。
- 树结构应使用 `list treeView="true"`。
- `loader3d` 保持已确认的小写 tag。
- 不把包级资源声明放进 `displayList`。

## 子节点位置

- `relation` 写在被约束的显示对象内。
- `gear*` 写在被控制的显示对象内。
- `item` 写在 `list` 内。
- Button、ProgressBar 等扩展子节点写在对应组件实例或扩展根组件内。
- `transition item` 的 target 指向本组件内存在的对象。

## id 与层级

- 同一组件内对象 `id` 唯一。
- 不随意改已有 `id`。
- 复制结构后检查是否产生重复 id。
- group、relation、controller、transition 引用的对象在同一作用域内可见。
