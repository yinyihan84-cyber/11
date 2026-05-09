# list

## 用途

用于可滚动或数量可变的数据集合。效果图中重复数据单元命中列表判断时，使用 `list + item`，不要在面板中平铺多套静态节点。

## 合法位置

`list` 是 `displayList` 显示对象，`item` 是 `list` 的子节点。

## 核心属性

- `defaultItem`
- `overflow`
- `autoClearItems`
- `lineGap`、`columnGap`
- `layout`
- `treeView`

## 基础片段

```xml
<list id="n0_list" name="list" xy="32,233" size="732,875" overflow="scroll" defaultItem="ui://pkgiditem01" autoClearItems="true">
  <item/>
  <item/>
  <item/>
</list>
```

## 结构规则

- `defaultItem` 必须指向 `package.xml` 中声明的 item 组件。
- item 组件 XML 必须真实存在。
- item 内部负责自身状态 controller 和 gear。
- 树结构使用 `list treeView="true"`。

## 注意事项

- 空数据和加载中状态通常放在页面或面板 controller，不放在 item。
- 不要只写 `<list>` 却漏掉 `defaultItem`、item XML 或 package 声明。
