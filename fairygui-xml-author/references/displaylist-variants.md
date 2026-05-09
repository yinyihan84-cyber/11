# FairyGUI DisplayList 变体规则

## 目的

用这份文档约束 `component.xml` 中 `displayList` 的标签写法，避免 agent 把“原始 XML tag”“语义变体名”“内部归一名”混写。

## 三层概念

- 原始 XML tag：文件里实际写出来的标签
- 语义变体：用于区分对象含义的变体名
- 写回口径：最终推荐生成到 XML 的形式

## 固定映射

下面是协议层映射，不是强制项目风格。用户项目规范下，凡引用文件夹资源图片，组件显示层强制写 `loader url="ui://..." fill="scaleFree"`；不要按表格把这类资源改成 image。

| 对象语义 | 原始 XML tag | 语义变体 | 推荐写回 |
|---|---|---|---|
| 图片 | `image` | `image` | `image` |
| 普通文本 | `text` | `text` | `text` |
| 输入文本 | `text input="true"` 或 `inputtext` | `inputtext` | `inputtext` |
| 富文本 | `richtext` | `richtext` | `richtext` |
| 图形 | `graph` | `graph` | `graph` |
| 分组 | `group` | `group` | `group` |
| Loader | `loader` | `loader` | `loader` |
| Loader3D | `loader3d` | `loader3D` | `loader3d` |
| MovieClip / JTA | `movieclip` 或 `jta` | `jta` | `jta` |
| 子组件实例 | `component` | `component` | `component` |
| 列表 | `list` | `list` | `list` |
| 树 | `list treeView="true"` 或 `tree` | `tree` | `list treeView="true"` |

## 条件化规则

### 输入文本

当文本对象表示输入文本时：

- 可读入形式：`text input="true"` 或 `inputtext`
- 推荐写回：`inputtext`

### 树

当列表对象表示树时：

- 可读入形式：`list treeView="true"` 或 `tree`
- 推荐写回：`list`，并带 `treeView="true"`

### Loader3D

- 原始 XML 标签写 `loader3d`
- 内部语义变体可视为 `loader3D`
- 不要把 CamelCase 直接写进 XML tag

## 写作禁令

1. 不要把 `loader3D` 直接当 XML tag 写入。
2. 不要把 `tree` 当成默认最终写回形式，除非项目样本明确要求。
3. 不要随意在 `text` 和 `inputtext` 之间混写。
4. 不要在无依据时保留 `movieclip`，优先按当前写回口径写 `jta`。
5. 不要只因资源是包内图片就强制写 `image`；项目样本使用 loader 时，loader 是该项目的优先写回口径。
6. 在用户项目中，文件夹资源图片必须写 `loader` 并带 `fill="scaleFree"`；`image` 只保留为协议兼容或特殊样本场景。
