# FairyGUI XML Author Skill

这是一个用于直接编写、修改、排查和校验 FairyGUI 工程 XML 的 Codex 技能包。

它的目标是让 Codex 在没有打开 FairyGUI 编辑器操作界面的情况下，也能根据现有工程、切图资源、效果图或截图，正确生成 `package.xml` 和组件 XML，并尽量保证这些 XML 能被 FairyGUI 工程读取。

## 这个技能解决什么问题

FairyGUI XML 不是普通的 HTML 或随意拼接的 UI 描述文件。一个可用的 FairyGUI 工程需要同时满足：

- `package.xml` 中资源声明完整。
- 组件 XML 的 `<component>`、`<displayList>`、controller、gear、transition 结构合法。
- `src`、`fileName`、`defaultItem`、`ui://包id资源id` 等引用全部闭合。
- 图片、字体、组件、列表项、按钮、进度条等资源能在当前包里解析。
- 效果图里的重复项、滚动区和状态变化不能被简单写死成一堆静态节点。

这个技能把这些规则拆成一套可复用的写作流程和参考文档，帮助 Codex 少猜、少造、少写出编辑器打不开的 XML。

## 适用场景

- 根据效果图或截图生成 FairyGUI XML。
- 给已有 FairyGUI 工程补 `package.xml` 资源声明。
- 新建或修改页面组件、弹窗组件、列表项组件、按钮组件、进度条组件。
- 把重复 UI 拆成 `list + item`，把互斥状态拆成 `controller + gear`。
- 排查 FairyGUI XML 中的资源引用、组件引用、controller/page、transition 引用问题。
- 校验 `ui://` 包 id、资源 id、组件 id 是否和工程真实索引一致。

## 核心原则

1. 已有工程优先  
   写入已有 FairyGUI 工程前，必须先读取现有 `package.xml`、`.objs/workspace.json`、`.objs/metas/` 和组件 XML。包 id、资源 id、组件 id 以工程已有值为准，不能自造 `pkg001`、`root01` 之类示例 id。

2. 包与组件分离  
   `package.xml` 只声明资源和发布信息；组件 XML 只写显示结构、控制器、扩展节点和引用关系。

3. 引用必须闭合  
   所有 `src`、`fileName`、`defaultItem`、`ui://包id资源id`、controller、page、transition target 都必须能解析。

4. 效果图不能当大图平铺  
   如果效果图中出现重复列表、滚动区域、未完成/可领取/已完成等互斥状态，应拆成组件、列表项和状态机。

5. 文件夹资源图片走 loader  
   本项目规则要求组件 XML 中引用文件夹切图时使用：

   ```xml
   <loader url="ui://包id资源id" fill="scaleFree"/>
   ```

   不要用 `<image src="资源id"/>` 承载这类切图。

6. 缺资源只占位，不造假  
   效果图上有但工程里没有对应切图时，保留语义化空 `loader` 占位，并在交付说明里列出缺失资源；不要用 `graph`、文本色块或自造图片替代。

## 标准工作流程

1. 判断任务类型：写 `package.xml`、写组件 XML、根据效果图生成结构，还是排查已有 XML。
2. 收集工程真实索引：包 id、资源 id、组件 id、路径、文件名、已打开文档 id。
3. 如果输入来自效果图或截图，先判断页面类型、重复单元、滚动区、状态机和可复用控件。
4. 根据任务读取对应参考文档：包、组件、列表、按钮、进度条、controller、gear、校验规则等。
5. 编写或修改 XML，优先复用项目已有命名、id 形态、组件拆分和资源引用方式。
6. 校验 XML 结构和引用闭环，特别检查 `ui://` 前缀是否等于真实 `packageDescription id`。
7. 交付时说明已验证内容和仍缺失的资源或不确定语义。

## 目录结构

```text
fairygui-xml-author/
├── SKILL.md
├── references/
│   ├── package-xml.md
│   ├── component-xml.md
│   ├── attribute-reference.md
│   ├── displaylist-variants.md
│   ├── resource-semantics.md
│   └── openfairygui-derived-notes.md
├── judgment/
│   ├── screenshot-judgment.md
│   └── component-splitting.md
├── components/
│   ├── button.md
│   ├── controller.md
│   ├── gear.md
│   ├── image.md
│   ├── list.md
│   ├── loader.md
│   ├── progressbar.md
│   ├── relation.md
│   ├── text.md
│   └── transition.md
└── validation/
    ├── validation-checklist.md
    ├── structure-validation.md
    ├── reference-validation.md
    └── diagnostic-strategy.md
```

## 文档分工

- `SKILL.md`：技能入口，定义总流程、硬性规则和索引。
- `references/package-xml.md`：说明 `package.xml` 的资源声明、包 id、图片语义和跨包引用。
- `references/component-xml.md`：说明组件 XML 的结构、displayList、扩展节点、designImage 和组件引用。
- `judgment/`：用于从效果图或截图判断页面结构、组件拆分和数据驱动方式。
- `components/`：按 FairyGUI 节点类型拆分的写法说明，例如 Button、List、Loader、ProgressBar、Controller、Gear。
- `validation/`：写完后用于检查结构、引用、资源、状态机和诊断口径。

## 交付标准

只有满足以下条件，才能认为 XML 可以交付：

- XML 能正常解析。
- 标签和属性符合 FairyGUI XML 口径。
- 本地资源、组件、列表项、按钮、进度条等声明完整。
- `ui://` 包 id 与当前 `packageDescription id` 一致。
- `src`、`fileName`、`defaultItem`、controller/page、transition target 引用闭合。
- 命中列表、列表项或状态机的效果图，已经落地为 `list + item + controller + gear`。
- 缺失资源已用空 `loader` 占位，并在说明中列出。

## 使用提示

把这个目录作为 Codex skill 安装或同步到技能目录后，当任务涉及 FairyGUI XML、`package.xml`、组件 XML、效果图转 UI、XML 排查或资源引用校验时，应触发 `fairygui-xml-author`。

如果只是读这个仓库，优先从 `fairygui-xml-author/SKILL.md` 开始；如果要根据效果图写 XML，先读 `judgment/screenshot-judgment.md` 和 `judgment/component-splitting.md`；如果要排查打不开的问题，重点读 `validation/reference-validation.md`。
