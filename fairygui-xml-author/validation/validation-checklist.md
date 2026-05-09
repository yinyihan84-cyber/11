# FairyGUI XML 校验清单

## 目的

交付前用这份清单检查 XML 产物本身是否合法、完整、可解析。不要在这里重新做效果图结构判断；效果图判断属于 `judgment/`。

## XML 语法层

- 标签闭合。
- 属性引号完整。
- XML 层级无破损。
- 没有重复属性。

## 标签与属性层

- 当前标签属于已知 FairyGUI 节点集合。
- 当前标签下的属性都在协议中。
- 没有拼错字段名。
- 新写 XML 优先使用 canonical 属性名，不扩散 alias。
- `displayList` 标签变体符合 [../references/displaylist-variants.md](../references/displaylist-variants.md)。
- 显示对象标签和资源引用方式符合项目样本口径，例如样本用 loader 展示图片时，新文件不应改用 image。

## 结构层

- 根节点类型正确。
- `displayList` 只包含合法对象。
- 扩展节点只出现在合法位置。
- `relation`、`gear*`、`item` 没有出现在错误父节点下。
- 对象 `id` 在同一组件内唯一。
- 对象 `id` 形态符合项目样本；常见导出口径为 `n0`、`n1`，语义写在 `name`。
- `extention` 与根组件或扩展子节点一致。

## 引用层

- `src` 指向存在的资源。
- `pkg + src` 可以解析到存在目标。
- `fileName` 与资源匹配。
- `relation target` 指向现有对象 id。
- `gear controller` 存在。
- `gear pages` 属于对应 controller。
- `pageController` 和 `selectionController` 存在。
- `transition item target` 存在。
- `action.controller` / `action.targetPage` 合法。

## 资源层

- 每个引用图片的 `image` / `loader` 都能解析到 `package.xml` 资源和真实图片文件。
- `ui://包id资源id` 的资源 id 与 `package.xml` 完全一致，且组件 `src/fileName` 与声明匹配。
- 显示节点的 `size` 与图片原始宽高不一致时，已在资源语义中说明九宫格、平铺、等比缩放、裁切或待确认。
- 用户明确给出的九宫格、平铺、平滑、白模染色信息已经写入对应资源或显示节点。
- 不存在用 `graph`、文本块、颜色块替代缺失切图的情况，除非用户明确允许。

## 效果图落地层

如果任务来自效果图、截图或参考效果图目录，只检查判断结果是否已经落地，不在这里重新做结构判断：

- 命中重复或滚动数据区时，面板已使用 `<list>` 和 `defaultItem`，没有把重复项平铺成多套静态节点。
- 命中 item 时，独立 item XML 已存在，且在 `package.xml` 中声明。
- 命中互斥状态时，item 或面板内已有 controller + gear，controller page 与 gear pages 闭合。
- 命中进度、按钮、奖励格等基础控件复用时，已新建/复用组件，或说明保留在 item 内的理由。
- 缺失运行时图标、头像、奖励等切图时，使用 loader 占位并在交付说明列出缺图。

## 最小交付标准

只有满足下面条件，才可宣称“XML 已可交付”：

- 结构完整。
- 标签合法。
- 属性合法。
- 本地引用闭合。
- 没有明显重复 id。
- 没有明显断裂的 controller/page/resource/transition 引用。
- 如果效果图命中 list/item/state，已经实际落地组件拆分、`defaultItem`、controller/gear 和 package 声明。

## 保守策略

校验时遇到不确定项：

1. 先查项目样本。
2. 再查本技能 references 和 components。
3. 仍无法确认时，明确标记“不确定”。
4. 不要自行创造协议。
