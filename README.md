# FairyGUI XML Author Skill

一个专门帮你写 FairyGUI XML 的通用 Skill。

简单说：你给它 FairyGUI 工程、切图、效果图、截图，或者一堆已经写到一半但打不开的 XML，它会尽量帮你把 `package.xml` 和组件 XML 写到 FairyGUI 能看懂的样子。

它不是 FairyGUI 编辑器替代品，也不会假装自己打开编辑器点来点去。它做的是更朴素但很重要的活：看工程、看资源、看引用关系，然后把 XML 写规矩。

## 它是干嘛的

FairyGUI XML 有点挑食。

不是说你写个 `<component>`，里面摆几个节点，它就会乖乖打开。它还要看：

- `package.xml` 里有没有声明资源。
- 组件里的 `src`、`fileName`、`defaultItem` 能不能找到。
- `ui://包id资源id` 的包 id 是不是真的。
- 图片、按钮、列表项、进度条是不是引用了真实资源。
- controller、gear、page 有没有对上。
- 效果图里的列表和状态，是不是真的拆成了列表和状态机。

这个 Skill 就是为了处理这些细节。它会提醒作者别拍脑袋造 id，别把效果图当一整张大图糊上去，也别用看起来很聪明但编辑器完全不认的写法。

## 适合什么时候用

- 根据效果图或截图写 FairyGUI XML。
- 给已有 FairyGUI 工程补 `package.xml`。
- 写页面、弹窗、列表项、按钮、进度条这些组件。
- 把重复内容拆成 `list + item`。
- 把“未完成 / 可领取 / 已完成”这种状态拆成 `controller + gear`。
- 查为什么 FairyGUI 打不开、资源丢了、引用断了。
- 校验 `ui://`、组件 id、资源 id、`fileName` 这些容易翻车的地方。

## 它最在意什么

### 1. 先认清工程，再动手

已有工程里通常已经有自己的包 id、资源 id、组件 id。

所以不能一上来就写 `pkg001`、`root01`、`item01` 这种示例 id。那种写法看着很整齐，编辑器打开时会很冷漠。

正确做法是先读：

- `package.xml`
- `.objs/workspace.json`
- `.objs/metas/`
- 已有组件 XML

先把真实 id 摸清楚，再写新的 XML。

### 2. `package.xml` 和组件 XML 各干各的

`package.xml` 负责声明资源。

组件 XML 负责摆 UI、写列表、写 controller、写 gear、引用资源。

不要把显示节点塞进 `package.xml`，也不要指望组件 XML 自己凭空认识资源。

### 3. 图片切图用 loader

这个技能默认遵守当前项目口径：文件夹资源图片在组件里用 `loader` 引用。

```xml
<loader url="ui://包id资源id" fill="scaleFree"/>
```

不要因为 `package.xml` 里资源声明是 `<image>`，组件里就跟着写 `<image src="..."/>`。这俩不是一回事。

### 4. 效果图不是贴纸

看到效果图，不是把整张图塞进去完事。

如果里面有任务列表，就拆列表。  
如果有不同状态，就拆 controller。  
如果有进度条，就按进度条处理。  
如果缺图，就留空 loader 占位，别拿色块硬凑。

## 一般怎么用

流程大概是这样：

1. 先判断这次是写 `package.xml`、写组件，还是根据效果图还原页面。
2. 看工程里真实的包 id、资源 id、组件 id。
3. 如果有截图或效果图，先判断页面结构：是不是弹窗、列表页、任务项、状态按钮。
4. 按需要查对应文档，比如 package、component、list、loader、button、progressbar。
5. 写 XML。
6. 校验引用是不是全都闭合。
7. 交付时说明哪些已经确认，哪些资源缺失或还不确定。

## 文件都放哪了

```text
fairygui-xml-author/
├── SKILL.md
├── references/
├── judgment/
├── components/
└── validation/
```

各目录大概是这样分工：

- `SKILL.md`：入口说明，写着最核心的规则和流程。
- `references/`：FairyGUI XML 的基础规则，比如 package、component、属性、资源语义。
- `judgment/`：看效果图时用，判断要不要拆列表、拆 item、拆状态。
- `components/`：各种节点的写法，比如 Button、List、Loader、ProgressBar、Controller、Gear。
- `validation/`：写完后检查用，专门抓引用断裂、结构不合法、id 对不上这类问题。

## 写完怎么算靠谱

至少要做到这些：

- XML 本身能解析。
- 组件和资源都在 `package.xml` 里找得到。
- `ui://` 的包 id 等于真实 `packageDescription id`。
- `src`、`fileName`、`defaultItem` 都能对上。
- controller 和 gear 的 page 没乱飞。
- 列表真的用了 `list + item`，不是手写一堆假列表。
- 缺资源的地方用空 `loader` 占位，并且老老实实说出来。

## 一句话总结

这个 Skill 是 FairyGUI XML 的“写之前先看清楚，写完以后再查一遍”小助手。

它不追求花活，追求的是：少猜 id，少断引用，少让编辑器一脸问号。
