# 引用校验

## 目的

检查资源、组件、controller、page、transition 和路径引用是否闭合。

## 资源引用

- 不引用未确认存在的 `id`、`src`、`pkg` 或资源路径。
- `src` 能在当前包 `package.xml` 中找到。
- `pkg + src` 能在外部包中找到；跨包引用不要丢 `pkg`。
- `url="ui://pkgidresid"` 的包 id 必须等于当前 `packageDescription id`，资源 id 必须能在同一个 `package.xml` 中找到；校验时用当前包 id 作为前缀拆分，不要按固定长度或示例 id 猜测。
- `fileName` 与目标组件资源的 `name` 一致。
- `path + name` 能解析到真实磁盘文件。
- 同一 `path + name` 不应出现多份资源声明；已有资源必须复用原 id，除非能证明是不同资源。

## 组件引用

- 父组件 `<component src="...">` 指向已声明组件。
- `list defaultItem` 指向已声明 item 组件。
- item XML 文件真实存在。
- 基础控件组件如 ProgressBar、Button、RewardCell 已在 `package.xml` 中声明。
- 如果 `.objs/workspace.json` 存在，`doc.activeDoc` 和 `doc.openedDocs` 中的 `ui://包id组件id` 必须能解析到当前 `package.xml` 的组件声明；否则要补组件声明、改用已有 id，或同步 workspace。

## controller 和 page

- 不引用未确认存在的 controller 或 page。
- 每个 `gear controller` 都能在当前组件中找到。
- `gear pages` 都属于对应 controller。
- `pageController`、`selectionController` 都存在。
- Button 实例的 `controller` 和 `page` 引用闭合。
- controller `selected` 属于 `pages`。

## transition 和 relation

- 不引用未确认存在的 transition、transition target、action target 或 relation target。
- `transition item target` 指向存在对象。
- `action.controller` 存在。
- `action.targetPage` 属于对应 controller。
- `relation target` 指向存在对象；空 target 表示根时需符合样本口径。

## 诊断口径

拿不到可靠行列时，不要伪造 `line` / `column`。至少报告文件、标签、对象 id、引用符号和修复建议。
