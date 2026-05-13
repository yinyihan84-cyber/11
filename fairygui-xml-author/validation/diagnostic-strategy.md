# FairyGUI XML 诊断策略

## 目的

规范本技能输出错误时的口径，避免把“逻辑校验”说成“源码编译器定位”。

## 诊断类型

### 1. 协议诊断

用于：

- 未知标签
- 未知属性
- 非法 alias 用法
- 非法 `displayList` 变体
- 非法子节点位置

推荐错误码范围：

- `FGX001` - 未知属性
- `FGX002` - 未知标签
- `FGX003` - 非法子节点
- `FGX004` - 非法扩展节点
- `FGX005` - 非法 `displayList` 变体

### 2. 结构诊断

用于：

- 根节点结构不完整
- `extention` 与扩展子节点不一致
- `group` 高级结构不合法
- 必要区段缺失

推荐错误码范围：

- `FGX101` - 根组件结构错误
- `FGX102` - `extention` 与扩展节点不一致
- `FGX103` - `group` 结构错误

### 3. 引用诊断

用于：

- 重复 `id`
- `src` / `pkg` / `fileName` 不匹配
- controller 不存在
- page 不存在
- relation target 不存在
- transition target 不存在

推荐错误码范围：

- `FGX201` - 重复对象 id
- `FGX202` - 资源引用不存在
- `FGX203` - controller 不存在
- `FGX204` - controller page 不存在
- `FGX205` - relation target 不存在
- `FGX206` - transition target 不存在

### 4. 视觉复判诊断

用于来自效果图的任务，在 package/component 初稿写完后对照效果图发现偏差：

- 九宫格、平铺、平滑等资源语义不符合拼后效果
- 可染色资源颜色未按源图与效果图目标区域判断
- 组件、loader、列表、item、按钮、进度条、奖励位坐标或尺寸偏差
- 缺失资源被相似图、graph、色块或文本替代
- 层级、状态 gear 或引用图与效果图不一致

推荐错误码范围：

- `FGX301` - 资源语义视觉偏差
- `FGX302` - 可染色资源取色偏差
- `FGX303` - 位置尺寸缩放偏差
- `FGX304` - 缺图占位策略错误
- `FGX305` - 层级或状态视觉偏差

## 输出级别

- `ERROR`：高概率无法正常读入或行为错误
- `WARNING`：可能能读入，但含义可疑或依赖样本口径
- `INFO`：不是错误，但值得说明

## 两种定位方式

### 1. 源码定位

只有在外部工具、解析器或校验器明确返回 `line/column` 时使用。

格式：

```text
ERROR FGX001 未知属性 "animatonName"
file: assets/Basics/Main.xml
line: 18
column: 42
node: <loader3d id="n7">
fix: 改为 "animation"
```

### 2. 逻辑定位

当没有源码 span 时，退化到逻辑定位。

格式：

```text
ERROR FGX203 controller 不存在
file: assets/Basics/Main.xml
node: <component id="n7">
symbol: pageController="state"
fix: 当前组件中不存在 controller "state"
```

## 保守原则

1. 没有真实位置就不要写行列号。
2. 没有完整符号表就不要断言跨包引用一定正确。
3. 当结论依赖样本口径时，明确说明“依据现有样本推断”。
