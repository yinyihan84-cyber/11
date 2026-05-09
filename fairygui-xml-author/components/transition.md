# transition

## 用途

用于循环闪烁、呼吸、灯效、刷新缩放等动画。不要用 transition 代替基础状态显隐；状态显隐优先 controller + gear。

## 合法位置

transition 写在组件根下，transition item 的 target 指向本组件显示对象。

## 核心属性

- `name`
- `autoPlay`
- `autoPlayRepeat`
- `time`
- `type`
- `target`
- `value`
- `startValue`、`endValue`
- `duration`

## 基础片段

```xml
<transition name="t0" autoPlay="true" autoPlayRepeat="-1">
  <item time="0" type="Visible" target="n0_a" value="true"/>
  <item time="0" type="Visible" target="n0_b" value="false"/>
  <item time="6" type="Visible" target="n0_a" value="false"/>
  <item time="6" type="Visible" target="n0_b" value="true"/>
</transition>
```

## 注意事项

- target 必须存在。
- 动画时长和类型沿用项目样本口径。
- 没有样本时先写最小动画，不要发明复杂结构。
