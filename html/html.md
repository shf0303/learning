设置文字溢出省略号代替：

```css
父组件：{
	whitespace:nowrap;
    overflow:hidden;
    text-overflow:elliscips
}
```

注意：内联样式和外联css不一样，在内联样式

```
style='color:red;fontsize:16px;margin-left:10px'
```

可以注意到不同点：style是一个属性，所以它的值也必须是一种字符串，以字符串包裹起来，其他就和外联css样式差别不大了。

内联元素边距和宽高

内联元素不能设置宽高，在边距方面也只能设置左右内边距(`padding-right`/`padding-left`)和左右外边距(`margin-right`/`margin-left`)

float浮动

* 当浮动元素前面是一个块级元素时，浮动元素不能忽视前面的块级元素浮上去，独占一行的效果还在，但是如果前面的是内联元素，比如说文字等，则会呈现一种环绕的效果。请注意！









