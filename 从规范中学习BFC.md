**块格式化上下文（block formatting context）**
是Web页面的可视CSS渲染的一部分。它是块盒子的布局发生及浮动体彼此交互的区域。
问题来了，什么是BFC呢？先来看看W3C中的解释
>Floats, absolutely positioned elements, block containers (such as inline-blocks, table-cells, and table-captions) that are not block boxes, and block boxes with 'overflow' other than 'visible' (except when that value has been propagated to the viewport) establish new block formatting contexts for their contents.
【译】浮动元素和绝对定位元素，非块级盒子的块级容器（例如 inline-blocks, table-cells, 和 table-captions），以及overflow值不为“visiable”的块级盒子，都会为他们的内容创建新的块级格式化上下文。

**块格式化上下文由以下之一创建：**
- 根元素或其它包含它的元素
- 浮动 (元素的[float](https://developer.mozilla.org/zh-CN/docs/Web/CSS/float)不是none)
- 绝对定位的元素 (元素具有[position](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position)为absolute或fixed)
- display属性为下列之一:table-cell,table-caption,inline-block,flex,or inline-flex.
- 块元素具有[overflow](https://developer.mozilla.org/zh-CN/docs/Web/CSS/overflow)，且值不是visible
在这些条件里面，用**overflow:hidden**最合适。
>In a block formatting context, boxes are laid out one after the other, vertically, beginning at the top of a containing block. The vertical distance between two sibling boxes is determined by the['margin'](https://www.w3.org/TR/CSS2/box.html#propdef-margin)properties. Vertical margins between adjacent block-level boxes in a block formatting context [collapse](https://www.w3.org/TR/CSS2/box.html#collapsing-margins).

【译】在一个块级格式化上下文中，盒子会从包含块的顶端在垂直方向上一个接一个地排列。两个兄弟盒子之间的垂直距离由margin属性决定，两个相邻的块级盒子的垂直外边距会发生叠加。

这里可以得出BFC的第一个作用：
**当两个相邻的块级盒子在同一个bfc中时。他们垂直方向上的外边距会发生重叠。解决办法是为其中一个盒子再次触发BFC，使两个盒子分属两个BFC。**
>In a block formatting context, each box's left outer edge touches the left edge of the containing block (for right-to-left formatting, right edges touch). This is true even in the presence of floats (although a box's *line boxes *may shrink due to the floats), unless the box establishes a new block formatting context (in which case the box itself [*may *become narrower](https://www.w3.org/TR/CSS2/visuren.html#bfc-next-to-float) due to the floats).

【译】在一个块级格式化上下文中，每个盒子的左外边缘（margin-left）会紧贴着包含盒子的左边缘（对于从右到左的格式来说，则触碰到右边缘），即使存在浮动也是如此（浮动会使父盒子会塌陷），除非这个盒子创建一个新的块级格式化上下文（在这种情况下，盒本身可能由于浮动而变窄）

这里可以得出BFC的第二个作用：
**利用BFC阻止文本换行，即破坏文字环绕效果**
****
![](http://upload-images.jianshu.io/upload_images/3801316-2249353f31c56673.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

来看一个例子：
![](http://upload-images.jianshu.io/upload_images/3801316-f3d4db14d6086af5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<pre> 
img{
    float:left;
    margin-left:10px;
    width:200px;
}
p{
    border:5px solid red;
    background-color: pink;
} 
</pre>

给图片加上浮动以后，效果变成了</br>
![](http://upload-images.jianshu.io/upload_images/3801316-4b1c9b04f8dea6e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里还可以实现一个小小的作用：两栏布局，宽度自适应
因为默认BFC和浮动元素在一行中不会重叠，两者相加刚好为屏幕宽度。
举个例子

![](http://upload-images.jianshu.io/upload_images/3801316-71abc89909f1c2b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<pre> 
.main{
    float:left;
    width:200px;
    height:20px;
    margin-left:10px;
    border:1px solid black;
    background-color: pink;
}
.bfc{
    overflow:hidden;
    height:20px;
    border:1px solid black;
    background-color: lightblue;
}
</pre>
>In addition, if the element has any floating descendants whose bottom margin edge is below the element's bottom content edge, then the height is increased to include those edges. Only floats that participate in this block formatting context are taken into account, e.g., floats inside absolutely positioned descendants or other floats are not.

【译】此外，如果BFC具有任何浮动子元素，其底边缘低于BFC的底部内容边缘，则高度增加以包括那些浮动元素。 仅考虑BFC的子元素浮动，例如，浮动在绝对定位或其他中则不会。

得出第三个作用：
**在w3c官方的定义中，BFC仍属于普通流，创建BFC即创建一个独立的盒子，BFC中的浮动元素虽然脱离了文本流但还在此空间内，浮动元素则会撑起父级BFC的高度。**
这里就迎来了BFC最最最最最最常用的一个作用：**清除浮动（应该说清除浮动的影响或者闭合浮动比较正确比较正确）**
首先明确下，BFC是一个完全独立的盒子，不会影响外部的元素的布局。</br>
BFC是一个机制，有之前的一系列条件可以出发这个机制产生作用。拿overflow:hidden来说（前提条件父元素不设置宽高）：   

overflow：hidden本意是用来隐藏溢出,子元素超过父级元素则会隐藏，浮动元素中父级崩塌，height：auto,height，没了内容，height：0，没了高度，应用了overflow:hidden本来应该,把浮动元素裁剪，这会使布局出现问题。所以这时候就会默认出发BFC这个机制，而**BFC要求不影响外部元素，所以会把BFC内部的元素全部包裹起来。这时候BFC就要计算其内部全部子元素的高度**，就会把浮动这个子元素计算入内。父元素原本没了高度，现在因为触发了BFC得到了一个高度，这时候overflow:hidden就起作用，但是父元素的高度是撑开子浮动元素的，overflow：hidden就显示不出作用，顺带还清除了浮动的影响

个人理解用BFC清除的过程就是：
**触发bfc的条件 → 触发bfc机制 → 计算全部子元素高度（计算height;auto）→ 父级被撑开 → 再去实现触发bfc的条件的效果**
