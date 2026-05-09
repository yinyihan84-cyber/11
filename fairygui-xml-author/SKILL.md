---
name: fairygui-xml-author
description: 直接编写、修改、排查和校验 FairyGUI 工程 XML。用于根据界面需求手写 `component.xml`、`package.xml`、`package_branch.xml`，根据现有 FairyGUI 样本补全 `displayList`、扩展节点、控制器、过渡和资源引用，或在 XML 打不开、字段不合法、id/控制器/页面/资源引用断裂时进行诊断。适用于“不要进编辑器，直接让 agent 写 FairyGUI XML 拼界面”的场景。
---

# FairyGUI XML 写作

## 目标

让 agent 直接手写 FairyGUI XML，并尽量做到：

- 标签合法
- 属性合法
- 结构合法
- 引用闭合
- 输出可被 FairyGUI 工程读取

把 FairyGUI XML 当作“受约束的配置语言”，不要当作自由 HTML。

## 何时使用

在下面这些请求中使用本技能：

- “根据这个界面描述写 FairyGUI XML”
- “补一个 component.xml / package.xml”
- “直接拼 FairyGUI 界面，不走编辑器”
- “这个 FairyGUI XML 为什么打不开”
- “检查有没有不存在的 id / controller / page / src / pkg 引用”
- “把已有样本改成另一个界面”

## 工作方式

先基于项目上下文和参考文档建立约束，再写 XML。

默认顺序：

1. 识别目标文件类型
2. 收集上下文符号
3. 收集资源路径与切图语义
4. 选择合法节点
5. 写入合法属性
6. 检查结构和引用
7. 给出结果或诊断

## 已确认事实

以下事实来自对 OpenFairyGUI 源码和协议文档的核对，可视为当前可靠约束来源：

- 已存在显式 XML 协议表，覆盖大量 `package.xml`、`component.xml`、`displayList`、扩展节点和结构节点
- `displayList` 的变体映射已明确固定，尤其是 `loader3d`、`inputtext`、`treeView`、`jta`
- reader / writer 已经在按 canonical 名和 alias 做归一读写
- 当前项目已有一部分项目级校验，但还不是“完整编译器”

这意味着本技能可以把这些内容当作“已知协议”，但不能假装所有 FairyGUI XML 行为都已被完整验证。

## 文件类型判断

优先判断当前编辑对象属于哪一类：

- `package.xml`
- `package_branch.xml`
- 组件根 XML，根节点为 `<component>`

不要把包级资源声明写进组件 `displayList`，也不要把组件显示对象写进 `package.xml`。

## 组件化生成策略

当用户给的是一整页效果图，但项目真实口径是“父组件 + 子组件 + 列表项 + 基础控件”拆分时，不要默认把所有内容塞进一个 XML。

优先按下面顺序判断是否应该拆分组件：

1. 页面容器是否只是承载一个主面板组件。
2. 主面板中是否存在可复用列表项、按钮、进度条、标签条等子结构。
3. 同一视觉块是否会在列表中重复出现；若会，优先抽成独立 `component` 并通过 `list defaultItem` 或 `<component src="...">` 引用。
4. 某块 UI 是否天然对应 FairyGUI 扩展节点，例如 `ProgressBar`、`Button`。

推荐拆分口径：

- 页面根：只放总容器和整体定位关系。
- 面板组件：放背景、标题、列表、关闭按钮、局部动效。
- 列表项组件：放 controller、gear、奖励区、进度区、状态区。
- 基础子组件：例如进度条、图标按钮等。

如果用户给了原项目样本，优先复用样本的拆分粒度，而不是重新发明新的层级。

## 效果图结构预判

根据效果图写 XML 前，必须先判断它是“静态拼图”还是“数据驱动组件”。不要看到界面就直接把所有可见内容平铺进一个组件。

按下面顺序做结构判断：

1. 找重复：如果同一视觉单元重复出现 2 次以上，且只是文案、图标、进度、奖励、按钮状态不同，默认应抽成 item 组件。
2. 找滚动：如果重复单元位于一个裁切窗口内、底部有被截断的下一条、或数量显然可能超过当前可见区域，默认应使用 `list`。
3. 找状态：如果同一 item 存在互斥视觉结果，例如未完成、待领取、已完成、锁定、选中、禁用、不同奖励展示，默认应在 item 组件内建立 controller + gear，而不是复制多套节点。
4. 找子控件：如果 item 内有进度条、按钮、标签、奖励格、等级角标等可复用块，优先独立成子组件或复用已有组件。
5. 找数据位：标题、描述、数量、进度值、图标、奖励、等级等运行时变化内容，只在 XML 中放预览值；不要为每条数据写死一套结构。

如果命中 `list + item` 结构，默认生成或维护这组文件关系：

- 页面根：只挂主面板组件。
- 主面板：包含背景、标题、关闭按钮、装饰、`list`。
- Item 组件：包含状态 controller、gear、任务图标、文案、进度、奖励、状态按钮。
- 基础控件组件：例如进度条，必要时由 item 组件引用。

### 命中后的强制落地

只判断出“这里应该用 list / item / 状态机”不算完成。只要效果图命中这些结构，就必须在本次 XML 输出中真正落地对应文件和引用，除非用户明确要求“只分析不改文件”。

强制规则：

1. 命中 `list`：必须在面板组件中写 `<list>`，并设置 `defaultItem`。不得继续把重复项直接平铺在面板组件里。
2. 命中 item 组件：必须新建或维护一个独立 item XML，并在 `package.xml` 中声明对应 `<component>` 资源。
3. 命中 item 状态：必须在 item XML 中写 controller，并用 `gearDisplay` / `gearIcon` / `gearColor` 等表达互斥状态。不得只在分析文字中说明状态，或复制多套 item 节点假装状态。
4. 命中基础控件：如果进度条、按钮、奖励格等在多个 item 或状态中复用，必须新建/复用基础组件，或明确说明为什么保持在 item 内更合适。
5. 引用闭环必须同时写好：`package.xml` 组件声明、面板 `list defaultItem`、item 内 `<component src="...">`、相关 `fileName`、controller page 和 gear pages。
6. 如果目标工程已有同类 `GameTaskPanel.xml` / `TaskItem.xml` / `TaskProgress.xml` 等样本，优先沿用其拆分和命名风格；如果没有样本，则按语义生成中性文件名，如 `Panel.xml`、`ListItem.xml`、`Progress.xml`。
7. 如果缺少 item 必需切图，只能在 item XML 中留空 `loader` 占位，并在交付说明里列出缺图；不能因此退回到面板里写死多条静态项。

引用必须闭合：

- `package.xml` 声明页面、面板、item、基础控件组件。
- 主面板中的 `list defaultItem` 指向 item 组件的 `ui://包id组件id`。
- item 组件中的 `<component src="...">` 指向基础控件组件。
- controller 名、page id、gear pages 必须全部在 item 组件内闭合。

只有当效果图明确是少量固定装饰、没有重复数据单元、没有滚动或状态变化时，才允许把内容直接写在一个组件里。

## 效果图组件判定与自检

当用户给出效果图、截图，或包内存在 `res/效果图`、`切图/效果图`、`界面效果图` 等参考目录时，先把视觉内容判定为 FairyGUI 结构，再写 XML。不要把效果图当作一张大图平铺。

### 判定规则

1. 识别页面类型：游戏 HUD、弹窗、列表页、加载页、结算页、状态 item。页面类型决定根组件、面板、列表、按钮和状态机的拆分边界。
2. 发现 2 个以上重复数据单元，或明显可滚动的数据区域，必须判断为 `list + item`；页面只承载 `<list ... defaultItem="ui://...">`，item 独立成组件。
3. 发现可点击区域，必须判断为 Button；优先复用已有 `IconButton`、`PayButton`、`TextButton` 或同包 `*Button.xml`，不要只写静态 `image`。
4. 发现头像、奖励、皮肤、广告图、动态背景、运行时图标，优先用 `loader` 或复用组件；固定底框、装饰和标题框才用 `image`。
5. 发现进度、血条、加载条、任务完成度，优先判断为 `extention="ProgressBar"`；只有确认是纯静态装饰时才保留普通图片。
6. 发现空数据、加载中、可领取、已领取、锁定、选中、禁用等互斥状态，必须写 controller + gear；不要复制多套完整节点。
7. 发现弹窗、商店、设置、排行榜结构，优先复用公共底框、内框、标题框、关闭按钮、等待弹窗；不要在页面内重新拼一套通用组件。
8. 发现可伸缩底框、按钮底、卡片底、标签底、进度槽，必须回到 `package.xml` 判断或补充九宫格语义。

### 写作规则

写 XML 前先输出极短结构判断：页面类型、组件拆分、需要的 controller、需要的 package 资源声明、自检结论。

落地时按下面顺序写：

1. 页面根只放大结构、背景、容器关系和入口 controller。
2. 面板承载背景、标题、关闭按钮、列表或主要内容。
3. 列表使用 `list defaultItem`，item 内部负责自身状态。
4. 按钮实例写 `<Button .../>`，tab 或状态切换按钮可写 `<Button controller="tab" page="1"/>`。
5. 运行时资源位用 `loader`，静态切图用 `image`，复用 UI 用 `component`。
6. 进度条组件根写 `extention="ProgressBar"`，实例用 `<ProgressBar value="..." max="..."/>`。
7. 效果图、页面、面板、item、基础控件和切图都要在 `package.xml` 中声明并闭合引用。

### 状态机规则

状态机优先放在变化发生的最小组件内：页面状态放页面，item 状态放 item，按钮展示变体放按钮。

常用 controller 命名：

- `state`：normal、available、received、locked、selected、disabled 等流程状态。
- `loading`：loaded / loading。
- `empty`、`no_history`、`recorded`：空数据 / 有数据。
- `tab`、`region`、`category`：页签、地区、分类。
- `type`、`bg`、`icon_size`：组件内部展示变体。

Gear 选择：

- 换图用 `gearIcon`。
- 显隐用 `gearDisplay`，双条件显隐再加 `gearDisplay2`。
- 换文案用 `gearText`。
- 换颜色或描边色用 `gearColor`。
- 换位置、尺寸、透明度用 `gearXY`、`gearSize`、`gearLook`。
- 循环闪烁、呼吸、灯效用 `transition`；不要用 transition 代替基础状态显隐。

最小合法嵌入片段示例；这些片段必须放在组件 XML 的合法位置，不能单独当完整 XML：

```xml
<list id="n_list" name="list" defaultItem="ui://pkgiditem01">
  <gearDisplay controller="empty" pages="0"/>
  <item/>
</list>
<component id="n_btn_tab" name="btn_tab" src="btn01" fileName="TabButton.xml">
  <Button controller="tab" page="1"/>
</component>
<loader id="n_icon" name="icon" url="ui://pkgc">
  <gearIcon controller="state" pages="0,1" values="ui://pkga|ui://pkgb" default="ui://pkgc"/>
</loader>
```

### 自检清单

交付前逐项确认：

1. 重复数据是否已拆成 `list + item`，而不是平铺多套节点。
2. item 的互斥状态是否在 item 内用 controller + gear 表达。
3. 可点击区域是否使用 Button，而不是只有 `image`。
4. 头像、奖励、皮肤、广告图等运行时资源位是否使用 `loader`。
5. 进度、血条、加载条是否使用 ProgressBar；若没有，是否已说明它只是静态装饰。
6. 空数据和加载中是否由页面 controller 控制，而不是写进 item。
7. `list defaultItem` 是否能解析到 `package.xml` 中声明的 item 组件。
8. `<component src="...">` 的 `src`、`pkg`、`fileName` 是否能解析到真实组件。
9. 效果图资源是否声明，并通过组件根 `designImage` 使用或明确不需要。
10. 可伸缩底框、按钮底、卡片底、进度槽是否有 `scale="9grid"` / `scale9grid`，或明确待确认。
11. 跨包公共资源是否保留 `pkg`，没有被误写成本包资源。
12. 没有用 `graph` 代替缺失的真实视觉切图，除非用户明确允许占位。

## 写之前先做的上下文收集

在改写任何 XML 前，尽量从当前文件和邻近文件收集这些符号：

- package id
- 资源 id
- 资源 name
- 资源真实磁盘路径
- 资源目录相对 package.xml 的相对路径
- 对象 id
- controller 名
- controller page
- transition 名
- `src` / `pkg` 可引用目标
- list 的 `defaultItem`
- 跨包资源的 package id
- 扩展节点类型，例如 `Button` / `ProgressBar`
- gear 绑定关系
- 哪些节点只是占位容器，哪些节点是状态切换节点

如果用户已经明确“切好图”“选好图”“直接用这些资源”，把资源文件本身视为第一手输入，而不是只根据效果图猜 UI。

如果用户还提供了“原项目 XML 样本”，优先把样本视为比效果图更高优先级的结构事实来源，尤其用于：

- controller 名称和 page 含义
- gear 类型与取值格式
- 组件拆分边界
- 跨包资源引用模式
- 关系和 pivot / anchor 写法

## 资源路径与工程布局规则

当任务不只是“改单个 XML”，而是“生成可打开的 FairyGUI 工程”时，默认先确定目标布局，再写文件。

优先布局：

- `<project>.fairy`
- `settings/*.json`
- `assets/<包名>/package.xml`
- `assets/<包名>/<组件名>.xml`

资源路径策略：

1. 默认优先复用用户现有资源目录。
2. 如果 `res/`、`images/`、`ui/` 等目录已经存在，优先在 `package.xml` 中直接写可解析到这些目录的 `path`。
3. 不要为了“方便生成”就复制、搬运、镜像一遍资源。
4. 只有用户明确要求“复制到工程目录”或现有路径无法被工程解析时，才允许新建 `images/` 并复制资源。
5. 如果确实需要复制资源，必须在交付里明确说明原因和新旧路径关系。
6. 不允许为了补视觉效果而临时创建新的绘制资源、程序化图形资源或替代底图；界面视觉资源必须优先来自用户提供的切图文件夹。

换句话说：能直接用用户给的资源，就直接用；不要擅自创建第二份资源副本。

## 跨包引用规则

如果样本里已经存在其他公共 UI 包，不要假设当前包必须自给自足。

需要明确区分三种引用：

1. 当前包本地资源：
   - 常见写法：`src="localImg01"` 或 `url="ui://pkgid01localImg01"`
2. 当前包子组件：
   - 常见写法：`<component src="item01" fileName="ListItem.xml"/>`
3. 外部公共包资源或组件：
   - 必须同时写清 `pkg` 与 `src`，或使用完整 `ui://包id资源id`
   - 例如：`pkg="commonpkg" src="panelBg01"`

不要把跨包资源错误地改写成本地资源，也不要漏掉 `pkg`。

## 纯切图约束

当用户要求“参考效果图拼界面”且同时提供了切图目录时，默认进入“纯切图模式”：

1. 不允许自行创建 `graph` 来代替缺失的背景、按钮、描边、底板、阴影或装饰。
2. 不允许用文本、颜色块或程序化图形去“脑补”未提供的美术。
3. 可显示的视觉元素应优先使用已有切图资源，通过 `image`、`loader`、`component` 等合法节点引用。
3.1. 如果用户明确要求“不要把图片直接写进 XML”，则切图引用必须统一使用 `loader`，先用 `xy + size` 确定区域，再通过 `url="ui://pkgIdresId"` 引用资源。
3.2. 在该模式下，不要输出直接承载切图的 `<image src="...">` 节点。
4. 如果某个位置没有对应切图：
   - 优先向用户明确反馈缺少哪张图、当前无法还原哪一块；
   - 如果任务要求先产出结构，可只放一个空 `loader` 作为占位，不要擅自补 `graph`。
5. 只有用户明确允许“先用图形占位”时，才可以突破本约束。

## 切图语义规则

不要把 PNG 当成“只有文件名和宽高”的静态贴图。FairyGUI 资源往往还带有切图语义，至少包括：

- 普通图
- 九宫格
- 平铺
- 是否平滑
- 资源原始宽高
- 是否为白模 / 灰模，需要在显示节点上用 `color` 染色

当用户提供了编辑器截图、口头说明或明确数值时，必须把这些语义写进 `package.xml` 对应资源属性，而不是忽略它们。

重点字段：

- 九宫格：`scale="9grid"` + `scale9grid="x,y,w,h"`
- 平铺：`gridTile`
- 禁止平滑：`smoothing="false"`
- 必要时补 `width` / `height`
- 白模染色：在使用节点上写 `color="#RRGGBB"`，不要改资源文件本身

### 拉伸语义检查

生成或修改组件时，不要只看资源名判断九宫格；必须对照“资源原始尺寸、显示尺寸、视觉角色”判断拉伸策略。

对每个引用图片的 `image` / `loader`：

1. 找到对应 `package.xml` 资源和真实图片原始宽高。
2. 对比显示节点的 `size` 与原始宽高。
3. 如果宽高不一致，先判断它是在做等比缩放、局部裁切、重复平铺，还是用于铺满一个可变尺寸区域。
4. 如果切图包含需要保持形状的边缘、描边、圆角、阴影、内凹、端帽、边框、槽体、底板或任何装饰边，且显示尺寸与原图不一致，默认必须在 `package.xml` 为该资源声明九宫格。
5. 如果切图是纯图标、插画、照片、徽章主体、文字图或不可拆分装饰，并且只做整体等比缩放，通常不要补九宫格。
6. 如果切图被非等比缩放，但又无法确认能否九宫格，不能静默省略；应明确标记“拉伸语义待确认”，并说明是需要用户确认九宫格，还是建议改为等比显示。

不要依赖 `button`、`bg`、`框`、`底` 等命名作为唯一依据；这些只能作为提示。即使资源名完全抽象，只要它在效果图中承担可伸缩容器、承载文字/图标、包裹内容、形成槽体或边界的角色，就必须进入九宫格检查。

交付前必须列出或内部确认所有“显示尺寸不同于原图尺寸”的图片资源，并逐个给出结论：已声明九宫格、等比缩放无需九宫格、平铺处理、裁切/填充处理，或语义待确认。

如果用户说“这个按钮是九宫格”“这个底图是平铺”“这个图不能平滑”，但没有把这些信息反映到资源声明中，视为交付不完整。

如果参考图或原项目样本显示某个白模资源需要染色，必须把颜色写到对应显示节点上。典型例子：

```xml
<loader id="n1_bar" name="bar" xy="2,2" size="271,24" url="ui://pkgidprogress_fill" fill="scaleFree" color="#fcd00b"/>
```

不要因为资源文件本身是白色，就按白色输出；以参考图和样本 XML 的最终视觉色为准。

对于重复列表项，要逐个 item 按状态检查白模 / 灰模染色，不要只改第一项。常见需要单独判断的节点包括：列表项内图标底、奖励底、按钮底、进度条填充、标题描边、状态标识底和面板内框。

写图标、奖励、按钮等“底 / 框 / bg / frame + 内容”的组合时，必须先判断包裹关系：底图或框图的显示区域应大于或等于内部 icon / text 的视觉区域，内部内容需要内缩居中。不要出现 `reward_icon`、`button_text`、`badge_icon` 等内容节点尺寸反而大于对应 `reward_bg`、`button_bg`、`badge_frame` 的情况，除非参考图明确是溢出装饰。

如果切图语义未知：

1. 先查用户是否提供了编辑器截图或参数。
2. 再查项目样本里同类资源的写法。
3. 仍无法确认时，明确标记“切图语义未知”，不要擅自猜九宫格数值。

如果项目里已经有 FairyGUI XML 样本，优先沿用其命名风格、布尔写法、默认值策略和写回口径。

## 不可违反的规则

1. 不要发明未确认存在的标签。
2. 不要发明未确认存在的属性。
3. 不要引用不存在的 `id`、`src`、`pkg`、controller、page、transition。
4. 新写 XML 时优先使用 canonical 属性名，不优先写 alias。
5. 不要随意改现有 `id`，除非任务本身要求重命名。
6. 无法确认时明确说明“不确定”，不要伪造协议。
7. 如果某条判断依赖项目样本而不是正式协议，要在结论里说明依据来自样本口径。
8. 如果没有真实解析器返回的位置信息，不要伪造“第几行第几列”。
9. 不要在未获要求时复制、搬运用户资源。
10. 用户已指定资源目录时，`package.xml` 必须优先回源到该目录。
11. 用户已明确要求只使用切图时，不允许自行添加 `graph` 作为视觉替代。
12. 缺失切图时，不要擅自补造 UI；必须反馈缺口，或只生成空 `loader` 占位。
13. 如果用户要求切图必须通过 `loader` 引用，则不要使用 `<image src="...">` 直接写资源。

## `displayList` 写作规则

严格按下列口径写：

- 图片写 `image`
- 普通文本写 `text`
- 输入文本优先写 `inputtext`
- 富文本写 `richtext`
- 图形写 `graph`
- 分组写 `group`
- 普通 Loader 写 `loader`
- 3D Loader 写 `loader3d`
- 动画资源实例优先写 `jta`
- 子组件实例写 `component`
- 普通列表写 `list`
- 树写 `list`，并额外写 `treeView="true"`

注意：

- `loader3D` 可能只在内部变体名里出现，原始 XML 标签仍写 `loader3d`
- `tree` 是语义变体，不是常规最终写回标签
- `inputtext` 是输入文本的优先写回口径

额外约束：

- 当用户指定“只允许引用切图”时，虽然协议上允许 `graph`，但实际生成时不得使用 `graph` 参与视觉还原。
- 缺图占位优先用空 `loader`，并在交付中明确说明它对应的缺失资源。
- 当用户指定“切图必须走 loader”时，已知资源也必须写成 `<loader ... url="ui://pkgIdresId" fill="scaleFree"/>`，而不是 `<image src="resId"/>`。

需要详细映射时，读取：

- [displaylist-variants.md](C:\Users\Tinyfun\.agents\skills\fairygui-xml-author\references\displaylist-variants.md)

## 列表与子组件规则

对于 `list`：

1. 如果样本里使用 `defaultItem`，生成时优先沿用该模式。
2. 如果只是占位演示，可保留若干 `<item/>` 作为编辑器内预览数据。
3. 不要把列表项内容直接展开写进 `list` 下，除非样本明确这么做。
4. 如果效果图中的列表项有多个状态，`list` 只负责承载 item；状态逻辑必须放在 item 组件的 controller / gear 中。
5. `defaultItem` 必须使用可解析的 `ui://包id组件id`，并且该组件必须在 `package.xml` 中声明。
6. 当你已经判断应该使用 `list` 时，本次生成结果中必须出现 `<list>`、独立 item XML、`package.xml` item 组件声明和 `defaultItem` 引用，不能只写判断结论。

对于 `<component>` 子节点：

1. `src` 指向组件资源 id。
2. `fileName` 尽量与组件文件名一致。
3. 如果样本存在扩展子节点，例如 `<Button>` / `<ProgressBar>`，优先按样本写，而不是用纯静态节点伪装。

## canonical 属性名规则

新生成 XML 时，优先写 canonical 名：

- 写 `animation`，不要优先写 `animationName`
- 写 `skin`，不要优先写 `skinName`
- 写 `prompt`，不要优先写 `promptText`
- 写 `colGap`，不要优先写 `columnGap`
- 写 `lineItemCount`，不要优先写 `lineCount`
- 写 `autoItemSize`，不要优先写 `autoResizeItem`
- 写 `autoPlayRepeat`，不要优先写 `autoPlayTimes`

如果是编辑现有老 XML，可以读 alias，但重写时尽量正规化。

完整属性表见：

- [attribute-reference.md](C:\Users\Tinyfun\.agents\skills\fairygui-xml-author\references\attribute-reference.md)

## 节点选择规则

根据界面意图选节点，不要混用：

- 资源显示优先用 `image`、`jta`、`loader`、`loader3d`
- 纯文字展示用 `text`
- 输入框用 `inputtext`
- 富文本展示用 `richtext`
- 容器/子组件实例用 `component`
- 组合布局用 `group`
- 列表/树用 `list`

只有在项目样本明确显示某种历史写法时，才保留兼容形式。

## 扩展子节点规则

只在语义匹配时写扩展子节点：

- `<Button>`
- `<Label>`
- `<ComboBox>`
- `<ProgressBar>`
- `<Slider>`
- `<ScrollBar>`

如果组件根用了扩展子节点，保持根节点 `extention` 与扩展子节点类型一致。

不要在一个组件根下随意混写多个互相冲突的扩展节点。

如果原项目样本已经证明某组件是扩展控件：

- 进度条优先写成 `extention="ProgressBar"` + `<ProgressBar .../>`
- 按钮优先写成带 `<Button .../>` 的子组件实例

不要把这些控件退化成普通图片拼装，除非用户明确要求只做静态视觉稿。

## 控制器理解规则

controller 不是“可有可无的状态标签”，而是组件行为和视觉切换的核心结构。生成 controller 前，先回答下面 4 个问题：

1. 这个 controller 控制的是什么语义状态。
2. 它有哪些 page。
3. 每个 page 下哪些节点显示、隐藏、换色、换图、换文案。
4. 它是否和其他 controller 组合使用。

默认先建立一份“控制器语义表”，再写 XML。

推荐输出脑内模型：

- controller 名
- page id / page 名
- 默认页
- 受影响节点
- gear 类型
- 每个 gear 的 pages / values / default

### controller 设计模式示例

下面只是结构模式，不代表业务默认值。实际生成时必须从用户给的界面描述、资源命名、数据字段或样本 XML 中推导 controller 名和 page 名。

- `state`
  - 示例 page：`0,disabled,1,available,2,completed`
  - 用途：表达流程状态、可交互状态或完成状态
  - 常控制：底图、按钮显隐、状态标识、颜色
- `category`
  - 示例 page：`0,normal,1,special`
  - 用途：表达条目类别、页签类别或内容类型
  - 常控制：分类标识、特殊装饰、额外字段显示
- `variant`
  - 示例 page：`0,default,1,hidden`
  - 用途：表达二级条件或展示变体
  - 常配合：`gearDisplay2`
- `rewardKind`
  - 示例 page：`0,icon,1,text`
  - 用途：表达奖励、标签、说明等区域的展示形态
  - 常控制：图标奖励区与文本奖励区互斥显示

如果用户要求“理解控制器”，至少要输出到“每个 controller 的语义、page、受影响节点、gear 类型”这个粒度，而不是只识别出 `<controller>` 标签存在。

## gear 写作规则

gear 不是孤立属性，它依赖 controller 语义。

生成 gear 时至少检查：

1. `controller` 指向存在的 controller。
2. `pages` 中的页 id 必须属于该 controller。
3. `values` 的数量和格式与 page 数一致。
4. `default` 合理，且能作为未命中页面时的回退值。
5. 多个 gear 叠加时，是否存在组合条件，例如 `gearDisplay` + `gearDisplay2`。

常见 gear 用法：

- `gearDisplay`
  - 某些页显示，某些页隐藏
- `gearDisplay2`
  - 第二控制器附加条件
- `gearColor`
  - 同一节点跨状态切换颜色或描边色
- `gearIcon`
  - 同一 loader / image 跨状态切换图源

如果某节点在不同状态只是“换图”，优先考虑 `gearIcon`，不要复制多份节点。
如果某节点在不同状态是“整块显示/隐藏”，优先考虑 `gearDisplay`，不要为每个 page 写一套完整重复结构。

## 关系与布局口径

如果样本里已经有 `pivot`、`anchor`、`relation`、`group`，生成时不要忽略它们。

尤其要注意：

- 页面级容器常通过 `relation target="" sidePair="center-center,bottom-bottom"` 挂到根上
- 标题装饰、按钮组、图标组常通过 `group` 绑定
- 某些标题框宽度跟随文本或 logo，需要 `relation`

不要只凭绝对坐标生成静态布局而忽略这些关系，否则结果只是“看起来像”，不是原项目口径。

## designImage 与参考图规则

如果原项目样本已经使用 `designImage`、`designImageOffsetX`、`designImageOffsetY`、`designImageAlpha`、`designImageLayer` 来挂参考图：

1. 应识别它们是编辑器内参考，不是运行时节点。
2. 不要把 `designImage` 误写进 `displayList`。
3. 当用户要“按效果图复刻”且目录里正好有参考图时，优先考虑挂为 `designImage`，辅助后续继续人工调整。

这类字段属于“编辑辅助语义”，skill 需要明确认识，而不是忽略。

## 结构检查规则

至少检查：

- 组件根 `<component>` 结构是否合理
- `displayList` 中是否只出现合法对象标签
- 对象 `id` 是否唯一
- `tree` 是否按 `list treeView="true"` 写出
- `relation`、`gear*`、`item`、扩展子节点是否出现在合法父节点下
- `group` 的高级结构不要随意生成
- 根组件扩展节点与 `extention` 是否一致
- `displayList` 条件变体是否和属性联动一致，例如 `input="true"` / `treeView="true"`

## 引用检查规则

完成 XML 后，至少检查：

- `src` 是否存在
- `pkg + src` 是否能解析到真实资源
- `package.xml` 的 `path + name` 是否能解析到真实磁盘文件
- `fileName` 是否与目标资源一致
- `relation target` 是否指向已有对象 id
- `gear controller` 是否存在
- `gear pages` 是否属于该 controller
- `pageController` / `selectionController` 是否存在
- `transition item target` 是否存在
- `action.controller` / `action.targetPage` 是否有效
- 同一组件内对象 `id` 是否重复
- 同一 package 内资源 `id` 是否重复

## 验证分层

执行时按下面 4 层理解“我到底验证到了什么”：

### 第 1 层：协议层

能确认：

- 标签是否合法
- 属性名是否合法
- alias 是否应正规化
- 子节点位置是否合法
- `displayList` 变体是否合法

### 第 2 层：结构层

能确认：

- 根节点结构是否合理
- 扩展节点与 `extention` 是否一致
- `group` / `relation` / `gear*` / `item` 是否放在正确位置
- 基本 `displayList` 结构是否闭合

### 第 3 层：引用层

能确认：

- `id` / `src` / `pkg` / controller / page / transition 等引用是否闭合
- 常见跨包资源引用是否能解析

### 第 4 层：源码定位层

只有在真实解析器或工具给出位置信息时，才能确认：

- `line`
- `column`
- 精确 source span

如果没有位置信息，只能报告文件、标签、对象 id、controller 名、resource 名等逻辑定位。

## 值格式规则

写值时保持 FairyGUI 常见格式，不要自由发挥：

- 二元元组用 `x,y`
- 尺寸用 `w,h`
- 四元数据按样本口径写
- 布尔值沿用项目已有风格
- 颜色值沿用项目已有风格

如项目样本依赖显式默认值，不要擅自删掉。

## 最小生成策略

从零新建界面时，按最小闭环生成：

1. 先写合法根 `<component>`
2. 先确定 `.fairy / settings / assets/<包名>` 是否也需要同时生成
3. 只加必要的 `controller` / `transition`
4. 先建最小 `displayList`
5. 只用确认过的节点和属性
6. 先让引用全部闭合
7. 再逐步扩展视觉细节

如果用户已经指定“直接生成到某个目录”，默认要一起考虑：

- `.fairy` 工程入口
- `settings` 目录
- `assets/<包名>` 目录

不要只生成 XML 而忽略工程入口，除非用户明确只要单文件 XML。

## 诊断输出规则

发现问题时，优先输出：

- 错误级别
- 文件路径
- 标签或节点路径
- 对象 id / controller / 资源名
- 明确修复建议

推荐格式：

```text
ERROR FGX001 未知属性 "animatonName"
file: assets/Basics/Main.xml
line: 18
column: 42
node: <loader3d id="n7">
fix: 改为 canonical 属性 "animation"
```

如果没有可靠的行列来源，不要伪造行号。此时至少报告：

- 文件
- 标签
- `id`
- 最近的唯一标识

如果只是逻辑诊断而不是源码诊断，优先写成：

```text
ERROR FGX014 引用断裂
file: assets/Basics/Main.xml
node: <component id="n7">
symbol: pageController="state"
fix: 当前组件中不存在 controller "state"
```

## 参考资料使用方式

先读主文档，再按需展开引用文档：

- 属性表：用于确认某个节点能写哪些字段
- `displayList` 变体表：用于确认标签名和语义变体
- 校验规则：用于交付前自检和报错

参考文件：

- [attribute-reference.md](C:\Users\Tinyfun\.agents\skills\fairygui-xml-author\references\attribute-reference.md)
- [displaylist-variants.md](C:\Users\Tinyfun\.agents\skills\fairygui-xml-author\references\displaylist-variants.md)
- [validation-checklist.md](C:\Users\Tinyfun\.agents\skills\fairygui-xml-author\references\validation-checklist.md)
- [openfairygui-derived-notes.md](C:\Users\Tinyfun\.agents\skills\fairygui-xml-author\references\openfairygui-derived-notes.md)
- [diagnostic-strategy.md](C:\Users\Tinyfun\.agents\skills\fairygui-xml-author\references\diagnostic-strategy.md)

## 失败时的行为

遇到下面情况，不要硬写：

- 请求使用了未确认存在的标签
- 请求依赖未提供的跨包资源
- 请求要求未知扩展结构
- 请求要求的字段不在已知协议中
- 请求想还原的视觉元素没有对应切图，但用户又限制不能自建图形

此时：

1. 明确指出缺口
2. 给出最接近的合法写法
3. 如果用户允许先保留结构，占位只能使用空 `loader`，不能改用 `graph`
4. 只有在 correctness 被阻塞时，才要求用户提供现有样本

## 生成后的默认动作

当任务是“生成或修改 FairyGUI XML”时，默认在交付前做下面几件事：

1. 对照属性表检查字段是否合法
2. 对照 `displayList` 规则检查标签和变体
3. 扫描局部 `id`、controller、page、transition、resource 引用
4. 扫描 `package.xml` 中每个资源 `path + name` 是否真实落盘
5. 检查用户明确给出的九宫格 / 平铺 / 平滑信息是否已写入
6. 对所有显示尺寸不同于原图尺寸的图片资源做拉伸语义检查，确认九宫格 / 平铺 / 等比缩放 / 待确认结论
7. 如果效果图命中 list/item/state 结构，检查是否真的生成或维护了面板 list、item XML、controller/gear、package 声明和 defaultItem 引用
8. 输出“已验证部分”和“未验证部分”

不要只给 XML，不给自检结果。

## 交付标准

只有在下面条件满足时，才可以说“XML 已可交付”：

- XML 结构完整
- 标签合法
- 属性合法
- 本地引用闭合
- 没有明显重复 id
- 没有明显断裂的 controller/page/transition/resource 引用

如果任何一步没法验证，要在结论里显式说清楚。

## 示例使用边界

本文中的 XML 示例只表达结构模式，不代表任何业务的默认命名或默认页面状态。

生成前必须先产出一份“业务语义表”，再把示例模板替换成当前项目自己的名称：

- 组件：根组件、面板组件、列表项组件、基础控件组件
- 资源：本包资源、跨包资源、参考图、缺失切图
- controller：名称、page id、page 名、默认页
- gear：受控节点、gear 类型、pages、values、default
- 缺图 loader：占位区域、尺寸、后续需要补的资源

不要照搬示例里的业务词汇。示例里的 `state`、`category`、`variant`、`rewardKind` 只是常见控制器类型，实际项目可以替换成 `tab`、`mode`、`quality`、`lockedState`、`selection` 等更准确的名字。

## 无样本时的默认生成流程

当项目里没有现成 `.xml` 样本，但用户希望直接从效果图和切图开始生成 FairyGUI XML 时，按下面顺序执行，不要跳步：

1. 先做效果图结构预判
   - 是否存在重复数据单元
   - 是否需要 `list`
   - 是否需要新建或维护 item XML
   - item 是否存在状态机，状态 controller 应有哪些 page
   - 是否需要基础控件组件，例如进度条、按钮、奖励格
2. 再列出资源清单
   - 哪些图承担可伸缩容器角色
   - 哪些图承担静态图标、插画、文字图或装饰角色
   - 哪些图承担边框、槽体、底板、端帽、遮罩或包裹内容的角色
   - 哪些图会被重复使用并出现不同显示尺寸
   - 哪些图只是参考图，不应进入 `displayList`
3. 再做组件拆分
   - 页面根组件
   - 主面板组件
   - 列表项组件
   - 基础控件组件，例如 `ProgressBar`
4. 再定义 controller 语义
   - 控制什么
   - 有哪些 page
   - 默认页是什么
   - 哪些节点受影响
5. 再决定 gear
   - 换图用 `gearIcon`
   - 显隐用 `gearDisplay`
   - 颜色切换用 `gearColor`
   - 组合条件再加 `gearDisplay2`
6. 最后才写 XML

如果用户没有提供足够切图，不要试图“猜出一切”；应明确留下空 `loader` 或向用户反馈缺图。

## 无样本时的组件拆分模板

这是推荐默认结构。文件名是中性示例，实际生成时要按业务替换。

### 通用场景：单页面板模板

适合没有列表、状态项较少的界面：

```xml
<?xml version="1.0" encoding="utf-8"?>
<component size="828,1656">
  <displayList>
    <component id="n0_panel" name="panel" src="panel01" fileName="PanelView.xml" xy="25,367">
      <relation target="" sidePair="center-center,bottom-bottom"/>
    </component>
  </displayList>
</component>
```

### 通用场景：弹窗 + 列表模板

### 第 1 层：页面根

只负责挂一个主面板组件，尽量不要把复杂内容都堆在根里。

```xml
<?xml version="1.0" encoding="utf-8"?>
<component size="828,1656">
  <displayList>
    <component id="n0_page" name="panel" src="panel01" fileName="PanelView.xml" xy="25,367">
      <relation target="" sidePair="center-center,bottom-bottom"/>
    </component>
  </displayList>
</component>
```

### 第 2 层：主面板组件

负责：

- 面板背景
- 标题装饰
- 标题文本
- 列表
- 关闭按钮
- 局部 transition

```xml
<?xml version="1.0" encoding="utf-8"?>
<component size="777,1289">
  <displayList>
    <loader id="n0_bg" name="bg" xy="8,148" size="778,1056" url="ui://pkgidbg01" fill="scaleFree"/>
    <loader id="n1_title_left" name="title_left" xy="493,67" size="94,96" url="ui://pkgidtitle_deco_a" fill="scaleFree"/>
    <loader id="n2_title_right" name="title_right" xy="198,72" size="86,84" url="ui://pkgidtitle_deco_b" fill="scaleFree"/>
    <text id="n3_title" name="title" xy="331,91" pivot="0.5,0.5" size="115,66" fontSize="50" color="#fff152" align="center" vAlign="middle" text="标题"/>
    <list id="n4_list" name="list" xy="32,233" size="732,875" overflow="scroll" defaultItem="ui://pkgiditem01" autoClearItems="true">
      <item/>
      <item/>
      <item/>
    </list>
  </displayList>
</component>
```

### 通用场景：状态列表项模板

负责：

- 状态 controller
- 分类 controller
- 展示变体 controller
- 图标、进度、按钮、奖励展示

优先把变化集中在 controller + gear，而不是复制三套节点。

### 第 4 层：基础控件组件

例如进度条，优先独立成 `extention="ProgressBar"` 组件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<component size="275,28" extention="ProgressBar">
  <displayList>
    <loader id="n0_bg" name="bg" xy="0,0" size="275,28" url="ui://pkgidprogress_bg" fill="scaleFree">
      <relation target="" sidePair="width-width,height-height"/>
    </loader>
    <loader id="n1_bar" name="bar" xy="2,2" size="271,24" url="ui://pkgidprogress_fill" fill="scaleFree">
      <relation target="" sidePair="width-width,height-height"/>
    </loader>
    <text id="n2_value" name="value" xy="0,-3" pivot="0.5,0.5" size="275,35" fontSize="25" color="#ffffff" align="center" vAlign="middle" text="5/20">
      <relation target="" sidePair="width-width,height-height"/>
    </text>
  </displayList>
  <ProgressBar titleType="valueAndmax"/>
</component>
```

## 无样本时的 package.xml 模板

如果是从零新建一个包，最少要先把资源声明清楚：

```xml
<?xml version="1.0" encoding="utf-8"?>
<packageDescription id="pkgid01">
  <resources>
    <component id="root01" name="RootView.xml" path="/" exported="true"/>
    <component id="panel01" name="PanelView.xml" path="/"/>
    <component id="item01" name="ListItem.xml" path="/"/>
    <component id="progress01" name="ProgressView.xml" path="/"/>
    <image id="title_deco_a" name="title_deco_a.png" path="/res/"/>
    <image id="title_deco_b" name="title_deco_b.png" path="/res/"/>
    <image id="progress_bg" name="progress_bg.png" path="/res/" scale="9grid" scale9grid="11,12,5,4"/>
    <image id="progress_fill" name="progress_fill.png" path="/res/" scale="9grid" scale9grid="10,10,3,3"/>
  </resources>
  <publish name=""/>
</packageDescription>
```

注意：

- 组件要先在 `package.xml` 里声明，后面 `src` 才能引用。
- 任何需要保护边缘形状且显示尺寸不同于原图的切图，优先在 `package.xml` 写九宫格；不要只靠资源名判断。

## 无样本时的 controller 模板

如果你知道一个列表项至少有三种流程状态，可以先用中性 page 占位，再按业务替换：

```xml
<controller name="state" pages="0,disabled,1,available,2,completed" selected="0"/>
```

如果还需要区分内容分类：

```xml
<controller name="category" pages="0,normal,1,special" selected="0"/>
```

如果还要区分展示变体：

```xml
<controller name="variant" pages="0,default,1,hidden" selected="0"/>
```

如果还要区分某个区域的展示形式：

```xml
<controller name="rewardKind" pages="0,icon,1,text" selected="0"/>
```

### controller 设计建议

1. `state` 用来表达流程状态或交互状态
2. `category` 用来表达内容类别
3. `variant` 用来表达二级条件或隐藏规则
4. `rewardKind` 只是“区域展示形式”的示例名，可替换成更贴近业务的名字
5. 不要让一个 controller 同时承担太多无关语义

## 无样本时的 gear 模板

### 1. 同一底图跨状态换图

```xml
<loader id="n0_card" name="card" xy="0,0" size="727,194" url="ui://pkgidcard_disabled" fill="scaleFree">
  <gearIcon controller="state" pages="0,1" values="ui://pkgidcard_disabled|ui://pkgidcard_available" default="ui://pkgidcard_completed"/>
</loader>
```

适合：

- 列表项底板
- 按钮底板
- 某个状态图标

### 2. 某节点只在某个状态显示

```xml
<text id="n1_status" name="status_label" xy="578,124" size="123,55" text="状态">
  <gearDisplay controller="state" pages="0"/>
</text>
```

### 3. 奖励区跨状态切换颜色

```xml
<text id="n2_label" name="label" xy="14,10" size="142,48" color="#ffffff" text="条目标题">
  <gearColor controller="state" pages="0,1,2" values="#ffffff,#318fb4|#ffffff,#c94f5a|#ffffff,#7079bc"/>
</text>
```

### 4. 两个 controller 联合控制显示

```xml
<loader id="n3_badge" name="badge" xy="93,146" size="26,26" url="ui://pkgidbadge_special" fill="scaleFree">
  <gearDisplay controller="category" pages="1"/>
  <gearDisplay2 controller="variant" pages="0" condition="0"/>
</loader>
```

适合：

- 特殊分类才显示额外标识
- 且在某个展示变体下隐藏

## 无样本时的 Button / ProgressBar 模板

### Button 组件实例

如果按钮来自公共包组件，不要只拼贴图，优先写：

```xml
<component id="n0_btn" name="btn_reward" src="button01" fileName="IconButton.xml" pkg="commonpkg" xy="565,108" size="149,72">
  <gearDisplay controller="state" pages="1"/>
  <Button title="操作" titleColor="#dd532c" titleFontSize="33"/>
</component>
```

### ProgressBar 组件实例

```xml
<component id="n1_progress" name="progress" src="progress01" fileName="ProgressView.xml" xy="138,145" size="274,28">
  <ProgressBar value="24" max="100"/>
</component>
```

## 无样本时的 transition 模板

如果效果图里有一闪一闪、交替亮灯、刷新动效等，不要一上来做复杂骨骼动画，可先用最小 transition：

### 可见性切换

```xml
<transition name="t0" autoPlay="true" autoPlayRepeat="-1">
  <item time="0" type="Visible" target="n0_a" value="true"/>
  <item time="0" type="Visible" target="n0_b" value="false"/>
  <item time="6" type="Visible" target="n0_a" value="false"/>
  <item time="6" type="Visible" target="n0_b" value="true"/>
</transition>
```

### 刷新缩放

```xml
<transition name="refresh_anim">
  <item time="0" type="Scale" tween="true" startValue="1,1" endValue="0,1" duration="3"/>
  <item time="3" type="Scale" tween="true" startValue="0,1" endValue="1,1" duration="6"/>
</transition>
```

## 无样本时的纯切图 + 占位策略示例

如果资源不完整，但用户又要求先生成可编辑骨架：

```xml
<component size="727,194">
  <controller name="state" pages="0,disabled,1,available,2,completed" selected="0"/>
  <displayList>
    <loader id="n0_card" name="card" xy="0,0" size="727,194" url="ui://pkgidcard_disabled" fill="scaleFree"/>
    <loader id="n1_icon_missing" name="icon_missing" xy="12,64" size="117,118" fill="scaleFree"/>
    <loader id="n2_value_frame" name="value_frame" xy="668,10" size="46,46" url="ui://pkgidvalue_frame" fill="scaleFree"/>
    <loader id="n3_value_text_missing" name="value_text_missing" xy="657,28" size="58,35" fill="scaleFree"/>
  </displayList>
</component>
```

这里的原则是：

- 有切图的部分正常引用
- 缺切图的部分留空 `loader`
- 用命名明确表达“这里缺什么”

## 无样本时绝对不要做的事

1. 不要因为没有样本就把整页内容全塞进一个巨大 XML。
2. 不要看到有 3 个状态，就复制 3 套完整节点；应优先用 item 组件的 controller + gear 表达状态。
3. 不要忽略 controller 和 gear，只做静态视觉。
4. 不要把可滚动或数据驱动的重复项直接展开写在面板组件里；应使用 `list defaultItem` 引用 item 组件。
5. 不要只判断“应该用 list / item / 状态机”却不新建或维护对应 XML 文件。
6. 不要只在面板中写 `<list>`，却漏掉 `defaultItem`、item 组件 XML 或 `package.xml` 组件声明。
7. 不要把参考图误塞进 `displayList` 当正式资源。
8. 不要漏掉 `package.xml` 中的组件资源声明。
9. 不要在需要保护边缘形状的可伸缩切图上省略 `scale9grid`，无论它在项目里叫什么名字。
10. 不要照搬示例里的业务词汇；示例只提供结构模式。

## 无样本时的最小交付要求

即使没有样本，也至少要做到：

- `package.xml` 可解析
- 组件层级清楚
- controller 命名有语义
- gear 引用闭合
- 缺图位置明确为占位
- 用户能看出后续该补哪些资源
