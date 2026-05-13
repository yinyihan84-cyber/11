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
- 没有发明未确认存在的标签或属性。
- 没有拼错字段名。
- 新写 XML 优先使用 canonical 属性名，不扩散 alias。
- `displayList` 标签变体符合 [../references/displaylist-variants.md](../references/displaylist-variants.md)。
- 显示对象标签和资源引用方式符合用户项目规范：引用文件夹资源图片时必须是 `<loader url="ui://包id资源id" fill="scaleFree"/>`，不得改用 `<image src="..."/>`。

## 结构层

- 根节点类型正确。
- `displayList` 只包含合法对象。
- 组件 `displayList` 中没有包级资源声明。
- `package.xml` 中没有组件显示对象、坐标布局或 `displayList` 内容。
- 扩展节点只出现在合法位置。
- `relation`、`gear*`、`item` 没有出现在错误父节点下。
- 对象 `id` 在同一组件内唯一。
- 对象 `id` 形态符合项目样本；常见导出口径为 `n0`、`n1`，语义写在 `name`。
- `extention` 与根组件或扩展子节点一致。

## 引用层

- 没有引用不存在或未确认存在的 `id`、`src`、`pkg`、controller、page、transition。
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

- 每个引用文件夹资源图片的 `loader` 都能解析到 `package.xml` 资源和真实图片文件，且带有 `fill="scaleFree"`。
- 组件 `displayList` 中不得用 `<image src="..."/>` 引用文件夹资源图片；`package.xml` 中的 `<image>` 只是资源声明。
- `ui://包id资源id` 的资源 id 与 `package.xml` 完全一致，且组件 `src/fileName` 与声明匹配。
- `ui://` 的包 id 与当前 `packageDescription id` 完全一致；不要残留示例包 id、临时包 id 或旧包 id。
- `.objs/workspace.json` 中 `doc.activeDoc`、`doc.openedDocs` 指向的组件 id 能在当前 `package.xml` 中解析，或已同步更新。
- 同一 `path + name` 没有被重复声明为多个资源 id；组件引用复用已有资源 id。
- 显示节点的 `size` 与图片原始宽高不一致时，已对已选资源完成语义判断：普通拉伸、九宫格、平铺、裁切或待确认。
- 写入 `scale9grid` 的资源必须能说明普通拉伸为什么不能匹配效果图，以及九宫格中心来源：FairyGUI 编辑器/资源面板切线、同项目可信样本，或基于原图像素结构分析得到。
- 九宫格数值不得来自按经验平均切、按文件名猜、只看显示尺寸或固守 package 初稿。
- 用户明确给出的九宫格、平铺、平滑、可染色资源颜色信息已经写入对应资源或显示节点。
- 不存在用 `graph`、文本块、颜色块替代缺失切图的情况，除非用户明确允许。
- 不存在用相似图片替代效果图中缺失资源的情况；缺失资源必须是空 loader 占位。

## 效果图落地层

如果任务来自效果图、截图或参考效果图目录，只检查判断结果是否已经落地，不在这里重新做结构判断：

- 命中重复或滚动数据区时，面板已使用 `<list>` 和 `defaultItem`，没有把重复项平铺成多套静态节点。
- 命中 item 时，独立 item XML 已存在，且在 `package.xml` 中声明。
- 命中互斥状态时，item 或面板内已有 controller + gear，controller page 与 gear pages 闭合。
- 命中进度、按钮、奖励格等基础控件复用时，已新建/复用组件，或说明保留在 item 内的理由。
- 缺失运行时图标、头像、奖励等切图时，使用 loader 占位并在交付说明列出缺图。

## 视觉二次验收层

来自效果图的任务必须在写完第一版 package/component 后做二次验收：

- `package.xml` 的九宫格、平铺、平滑和资源声明不是第一版即正确；必须根据拼后视觉效果复查并回改。
- 组件 XML 的坐标、尺寸、缩放比例、颜色、透明度、层级、资源引用、状态 gear 和空 loader 占位必须逐项对照效果图。
- 可染色资源颜色不能按文件名枚举判断；必须检查源图主体是否白色或近灰白，并对照效果图中该实例区域。目标区域仍白时不写 `color`，目标区域非白时在显示节点或 gear 上写对应颜色。
- 已选资源的普通拉伸、九宫格、平铺、裁切或待确认判断必须以拉伸后是否匹配效果图为依据。
- 九宫格复判必须确认圆角、边框、阴影、高光、纹理、渐变、端帽和底边没有被错误拉伸、拉厚或错位。
- 列表区域、item 高度、lineGap、裁切窗口、按钮位置、进度条比例、奖励位和底部/顶部装饰必须参与视觉验收。
- 复查发现的偏差必须写回 XML；不能只在交付说明里描述“需要调整”。

## 最小交付标准

只有满足下面条件，才可宣称“XML 已可交付”：

- 结构完整。
- 标签合法。
- 属性合法。
- 本地引用闭合。
- 没有明显重复 id。
- 没有明显断裂的 controller/page/resource/transition 引用。
- 没有明显断裂的 package id、组件 id、workspace 打开文档 id 或 `ui://` 前缀引用。
- 如果效果图命中 list/item/state，已经实际落地组件拆分、`defaultItem`、controller/gear 和 package 声明。
- 如果任务来自效果图，已经完成拼后视觉二次验收，并回改 package/component 中可修正的偏差。

## 保守策略

校验时遇到不确定项：

1. 先查项目样本。
2. 再查本技能 references 和 components。
3. 仍无法确认时，明确标记“不确定”。
4. 不要自行创造协议。
