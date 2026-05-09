# Button

## 用途

用于可点击区域、关闭按钮、领取按钮、页签按钮、状态按钮。不要用静态 `image` 伪装按钮。

## 合法位置

Button 可以是扩展根组件，也可以是组件实例内部的扩展子节点。

## 核心属性

- `title`
- `icon`
- `controller`
- `page`
- `titleColor`
- `titleFontSize`
- `selectedTitle`

## 基础片段

```xml
<component id="n0_btn" name="btn_reward" src="button01" fileName="TextButton.xml" pkg="commonpkg" xy="565,108" size="149,72">
  <Button title="领取" titleColor="#dd532c" titleFontSize="33"/>
</component>
```

## 结构规则

- `src` / `pkg` 指向真实按钮组件资源。
- 页签或状态按钮可写 `<Button controller="tab" page="1"/>`。
- 按钮状态显示隐藏用 `gearDisplay` 控制实例，不复制多套按钮。

## 注意事项

- 优先复用已有公共按钮组件。
- 如果只能先占位，明确说明缺按钮组件或切图。
