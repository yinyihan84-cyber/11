# 从 OpenFairyGUI 源码确认的规则与边界

## 目的

这份文档不是要求依赖 OpenFairyGUI 运行，而是把从其源码中确认过的 FairyGUI XML 规则沉淀为本技能的参考事实。

## 已确认可参考的内容

### 1. 协议表是显式维护的

源码里存在一份集中协议表，覆盖：

- `package.xml`
- `component.xml`
- `displayList`
- 扩展节点
- 结构节点

因此，本技能可以把这些协议视为可靠约束来源。

### 2. `displayList` 变体规则已固定

已确认的重点口径：

- `GTextInput` 写回 `inputtext`
- `GTree` 写回 `list treeView="true"`
- `GLoader3D` 写回 `loader3d`
- `GMovieClip` 当前写回口径优先 `jta`

### 3. alias 是读兼容，不是优先生成口径

例如：

- `animationName` -> `animation`
- `skinName` -> `skin`
- `promptText` -> `prompt`
- `columnGap` -> `colGap`
- `lineCount` -> `lineItemCount`
- `autoResizeItem` -> `autoItemSize`
- `autoPlayTimes` -> `autoPlayRepeat`

新生成 XML 时，优先 canonical。

## 已确认的能力边界

### 1. 当前项目级 validate 不完整

当前已有的 validate 偏轻量，主要检查：

- 缺失 project / package / resource id
- package 内重复资源 id
- 空组件
- 空 controller
- `ui://` 断链

它还不足以覆盖本技能希望做到的全部 XML 校验。

### 2. 当前解析链路没有稳定的行列号

当前 XML 解析可以保顺序，但没有稳定提供：

- line
- column
- source span

因此如果没有外部解析器支持，本技能不应伪造源码定位能力。

### 3. 协议覆盖测试很强，但偏样本驱动

已有测试会检查：

- 样本 XML 用到的属性是否都在协议中
- `displayList` / 扩展节点 / group 高级结构的约束是否稳定

这能增强本技能的信心，但不等于全部 FairyGUI XML 规则都已穷尽。

## 对本技能的直接影响

1. 可以自信地约束标签、属性、变体和常见结构。
2. 对引用校验可以做强一些，但仍要保守。
3. 对行列号级诊断必须显式区分“真实定位”与“逻辑定位”。
4. 交付时应该同时给出：
   - 已验证部分
   - 未验证部分
   - 保守假设
