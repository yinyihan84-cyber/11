# gear

## 用途

让同一节点随 controller/page 改变显示、图源、文本、颜色、位置、尺寸或透明度。

## 合法位置

gear 写在被控制的显示对象内部。

## 常见类型

- `gearDisplay`：按状态显隐。
- `gearDisplay2`：第二 controller 附加条件。
- `gearIcon`：换图源。
- `gearText`：换文案。
- `gearColor`：换颜色或描边色。
- `gearXY`、`gearSize`、`gearLook`：换位置、尺寸、透明度等。

## 基础片段

```xml
<loader id="n0_card" name="card" size="727,194" url="ui://pkgidcard_disabled" fill="scaleFree">
  <gearIcon controller="state" pages="0,1" values="ui://pkgidcard_disabled|ui://pkgidcard_available" default="ui://pkgidcard_completed"/>
</loader>
```

```xml
<text id="n1_status" name="status_label" size="123,55" text="状态">
  <gearDisplay controller="state" pages="0"/>
</text>
```

## 结构规则

- `controller` 必须存在于当前组件。
- `pages` 必须属于该 controller。
- `values` 数量和格式必须与 pages 口径匹配。
- `default` 是未命中页面时的回退值。

## 注意事项

- 换图优先 `gearIcon`，不要复制多份节点。
- 整块显隐优先 `gearDisplay`，不要为每个 page 写一套完整重复结构。
