# package.xml 写法

## 用途

`package.xml` 只负责包级资源声明和发布信息。不要把组件显示对象、坐标布局或 `displayList` 内容写进这里。

## 现有工程优先

写入已有 FairyGUI 工程时，先把工程索引当作事实来源，而不是从示例生成新 id：

- 读取已有 `package.xml`，以 `<packageDescription id="...">` 作为当前包 id。
- 读取 `.objs/workspace.json`，记录 `doc.activeDoc`、`doc.openedDocs`、`libview.expandedNodes` 中出现的包 id 和组件 id；这些 id 往往是编辑器已经打开或缓存的真实文档。
- 扫描现有组件 XML，建立 `component id -> name/fileName`、资源 `path + name -> id` 的映射。
- 已有资源按 `path + name` 复用原 id，不要为同一 PNG/JPG/字体再声明一份短 id。
- 只有确认是全新包、没有 `package.xml`、没有 `.objs/workspace.json` 或 metas 线索时，才允许生成新的包 id 和资源 id；示例里的 `pkgid01`、`root01` 只代表形态，不能直接照抄。
- 如果编辑器已经自动补全了资源声明，保留它生成的资源 id，只在缺少组件声明或资源语义时做最小追加/修正。

## 基础结构

```xml
<?xml version="1.0" encoding="utf-8"?>
<packageDescription id="pkgid01">
  <resources>
    <component id="root01" name="RootView.xml" path="/" exported="true"/>
    <component id="panel01" name="PanelView.xml" path="/"/>
    <image id="bg01" name="panel_bg.png" path="/res/" scale="9grid" scale9grid="12,12,8,8"/>
  </resources>
  <publish name=""/>
</packageDescription>
```

## 结构规则

- 根节点使用 `<packageDescription id="...">`。
- 资源声明放在 `<resources>` 内。
- 组件资源使用 `<component>`，`name` 通常是组件 XML 文件名。
- 图片资源使用 `<image>`，`name` 是真实图片文件名，`path` 是相对包资源目录的路径。
- 组件先在 `package.xml` 声明，组件 XML 中的 `src` / `defaultItem` 才能引用。
- `exported="true"` 只用于需要被外部创建或导出的组件。

## 常用属性

- `id`：包内唯一资源 id。
- `name`：资源文件名或显示名称，组件通常写 XML 文件名。
- `path`：资源所在目录，保留项目已有口径。
- `fileName`：组件实例中用于说明目标组件文件名，不写在资源声明上。
- `scale="9grid"` + `scale9grid="x,y,w,h"`：九宫格切图。
- `scale="tile"`：平铺切图。
- `gridTile`：tile grid 索引；只有样本或资源语义明确时才写，不能单独当作平铺标记。
- `smoothing="false"`：禁用平滑。
- `width` / `height`：样本或资源语义需要显式记录时再写。

## 图片资源属性

图片是否九宫格、平铺、禁用平滑，在 `package.xml` 的 `<image>` 资源声明阶段先写初稿。任务来自效果图或截图时，必须先确定当前显示节点实际引用的图片资源，再用普通拉伸结果对照效果图判断资源语义；组件 XML 拼出后还要再次复查，发现拉伸、边角、纹理或缩放不对时回改 `package.xml`。

- 九宫格：`scale="9grid"` + `scale9grid="x,y,w,h"`。
- 平铺：写 `scale="tile"`。
- tile grid：按项目样本写 `gridTile`。
- 禁用平滑：按项目样本或资源语义写 `smoothing="false"`。
- 宽高：样本或资源面板明确记录时写 `width` / `height`。

## 图片资源与显示节点口径

- 固定静态切图不自动等于组件里的 `<image>` 显示节点；先看项目样本，样本显示层用 `loader url="ui://..."` 时继续用 `loader`。
- 头像、奖励、皮肤、广告图、动态背景、运行时图标优先用 `loader`。
- 用户项目规范要求文件夹资源图片在组件中写 `<loader url="ui://包id资源id" fill="scaleFree"/>`，`package.xml` 中仍用 `<image>` 声明资源本体。
- 缺图但需要保留结构时，组件中使用带语义 `name`、坐标和尺寸的空 `loader` 占位，不要用 `graph`、文本、色块或程序化形状脑补。

## 根据效果图判断如何拉伸

`package.xml` 不写布局，但它必须承接已选图片资源的拉伸语义。写 `<image>` 声明前，先比对当前节点实际引用的图片、切图原始尺寸、组件 XML 显示尺寸和效果图目标外观。

- 先选资源：根据效果图位置、已有 `package.xml`、切图目录和同类样本，确定当前显示节点使用的具体图片。
- 再做普通拉伸验证：比较原图尺寸、组件显示尺寸和效果图目标外观，判断普通缩放是否能对得上效果图。
- 不因为显示尺寸与原图尺寸不一致就自动写九宫格；先验证普通拉伸是否能还原效果图。
- 普通拉伸会导致边缘、圆角、描边、阴影、高光、渐变、纹理、端帽或底边明显变形时，再判断 `scale="9grid"`、`scale="tile"`、裁切或待确认。
- 写入九宫格前必须能解释普通拉伸为什么不能匹配效果图，以及九宫格中心依据来自哪里。
- 纯图标、头像、插画、照片、文字图、徽章主体、不可拆分装饰不因尺寸变化自动写九宫格。
- 连续纹理、条纹、格子、噪声背景优先判断 `scale="tile"`，不要误判为九宫格。
- 九宫格数值优先级：FairyGUI 编辑器或资源面板明确切线最高；其次是同项目同类可信样本；再其次才是原图像素结构分析加效果图复判。
- 原图结构分析要先读取尺寸，再找不可拉伸区域：颜色/透明度突变、边缘轮廓、圆角、描边、高光、阴影、渐变、纹理和装饰都应保护。
- 在横向和纵向分别找颜色稳定、纹理变化少、连续且最小的区域作为可拉伸中心；`scale9grid="x,y,w,h"` 写的是这个中心矩形，不是四边保护区。
- 禁止用按高度/宽度平均切、按文件名猜、只看显示尺寸或沿用 package 初稿的方式生成 `scale9grid`。
- 能从普通拉伸验证判断“需要九宫格”，但缺少可验证切线或原图结构证据时，标记 `scale9grid` 待确认；不要编造数值。
- 写完后用效果图复判；如果边框变粗、圆角变形、高光/阴影/渐变被拉长、纹理错位或底边错位，回改 `scale9grid`。

示例：原图 `54x86` 的列表底图，左右保护边各 18，则中心 `x=18,w=18`；上部 67 像素包含圆角、描边、渐变和主体结构，底部 11 像素包含底边和收口，中心高度为 `86 - 67 - 11 = 8`，写 `scale9grid="18,67,18,8"`，不要经验估算成 `18,30,18,26`。

```xml
<image id="progress_bg" name="progress_bg.png" path="/res/" scale="9grid" scale9grid="11,12,5,4"/>
```

## 跨包引用

- 本包资源常用例如:  `src="res01"` 或 `url="ui://pkgidres01"`。
- 外部公共包资源必须保留 `pkg`，例如 `pkg="commonpkg" src="button01"`。
- 不要把跨包资源复制成本包资源，除非用户明确要求复制或当前工程无法解析原路径。
- 本包 `ui://` 前缀必须等于当前 `packageDescription id`。例如包 id 是 `x7y3gns3` 时，资源引用必须是 `ui://x7y3gns3资源id`，不能使用自造的 `ui://pkg001资源id`。

## 资源语义

- 已选图片资源被缩放使用时，必须按效果图验证普通拉伸、九宫格、平铺、裁切或待确认。
- 可重复纹理、连续背景、条纹、噪声、格子底等优先检查平铺。
- 图标、插画、照片、徽章主体、文字图通常不要补九宫格。
- 可染色资源在 package 阶段只判断资源语义；最终颜色写在组件显示节点 `color` 上。
- `package.xml` 第一版不是最终权威；拼后视觉验收可以推翻第一版九宫格、平铺、平滑和资源语义判断。
- 不要按资源名称判断是否染色。判断顺序是：先看源图主体是否为白色或近灰白、且不是不可染色图标/插画/文字图；再看效果图中该实例位置是否仍为白色。目标仍白则不写 `color`，目标非白才在组件 loader 节点或 gear 上写对应颜色。
- 资源语义不确定时标记“待确认”，不要猜 `scale9grid` 数值。

平铺写法：

```xml
<image id="tile_bg" name="tile_bg.png" path="/res/" scale="tile"/>
```

`gridTile` 不是平铺主标记，只在样本或资源面板明确给出 tile grid 索引时补写。

可染色显示节点写法：

```xml
<loader id="n1_bar" name="bar" size="271,24" url="ui://pkgidprogress_fill" fill="scaleFree" color="#fcd00b"/>
```

同一资源在不同状态或不同位置颜色不同，用 controller + `gearColor`，不要复制多份资源声明。

## 资源禁令

- 不要在未获要求时复制、搬运用户资源。
- 用户已指定资源目录时，`package.xml` 必须优先回源到该目录。
- 用户明确只允许引用切图时，不得用 `graph` 参与视觉还原。
- 缺失切图时，不要擅自补造 UI；反馈缺口，或只生成空 loader 占位。
- 用户项目规范要求切图必须走 loader：组件中使用 `<loader url="ui://pkgIdresId" fill="scaleFree"/>`，不要使用 `<image src="resId"/>`。
- 无法确认资源语义时写“不确定”，不要猜九宫格、平铺、平滑等属性。
- 写“底 / 框 / bg / frame + 内容”的组合时，底图或框图区域应大于或等于内部 icon / text 的视觉区域，内部内容内缩居中。不要让 `reward_icon`、`button_text`、`badge_icon` 等内容节点尺寸反而大于对应底框，除非参考图明确是溢出装饰。

## 协议来源

- 包级标签、属性和资源语义可参考 :
  - [attribute-reference.md](attribute-reference.md) 
  -  [openfairygui-derived-notes.md](openfairygui-derived-notes.md)。

- 这些参考文件只用于确认协议，不改变 `package.xml` 只声明资源的边界。

## 注意事项

- 不要为了方便生成而复制用户已有切图目录。
- 不要遗漏 item、ProgressBar、Button 等子组件资源声明。
- `id` 在同一包内必须唯一。
- `path + name` 应能解析到真实落盘文件。
