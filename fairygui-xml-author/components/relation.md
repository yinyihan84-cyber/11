# relation / group / pivot / anchor

## 用途

用于表达布局关系、根对齐、组件跟随、组绑定和锚点。不要只用绝对坐标生成静态布局而忽略样本里的关系。

## 合法位置

`relation` 写在被约束的显示对象内部。`group`、`pivot`、`anchor` 按项目样本口径使用。

## 基础片段

```xml
<component id="n0_panel" name="panel" src="panel01" fileName="PanelView.xml" xy="25,367">
  <relation target="" sidePair="center-center,bottom-bottom"/>
</component>
```

## 结构规则

- 页面级容器常通过空 target 关联根。
- 标题装饰、按钮组、图标组可通过 group 绑定。
- 标题框、文本、logo 宽度跟随时检查 relation。
- relation target 必须能解析到对象 id；空 target 表示根时沿用样本口径。

## 注意事项

- 有样本时优先沿用样本的 `pivot`、`anchor`、`relation`、`group`。
- 不确定高级 group 结构时不要硬写。
