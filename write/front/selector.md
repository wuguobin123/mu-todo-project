### css选择器

#### 通用选择器
```
//所有元素的字体颜色都为红色
* {
  color: red;
}
```

#### class选择器
```
<ul>
  <li></li>
  <li class="item"></li>
  <li></li>
</ul>

.item {
  font-size: 30px;
  color: red;
}
```
#### id选择器
```
  <ul>
    <li id="itemId">1</li>
    <li>2</li>
    <li>3</li>
  </ul>

  #itemId {
    color: red;
  }
```

#### 元素选择器
```
  <div>
    <p></p>
    <h2></h2>
    <p></p>
  </div>
  p {
    color: red;cd
  }
```

#### 属性选择器
```
//常用语法如下：
[attr] //具备某个属性
[attr=value]   //某个属性值为value的元素
[attr~=value]  //某个属性值多个内容，中间用空格隔开，其中一个内容为value
[attr|=value]  //某个属性值多个内容，中间用连字符-进行连接，其中值以value开头
[attr^=value]  //某个属性值以value开头
[attr$=value]  //某个属性值以value结束
[attr*=value]  //某个属性值包含value的元素
```

#### 后代选择器
  两个标签之间用空格隔开
```
/*
  div标签后的所有p标签,不仅是子标签中的p
*/
div p {
  font-size: 30px;
}
```

#### 分组选择器
  多个标签使用逗号分隔
```
/*
  定义的标签或者类选择器都会选择对应的标签
*/
div, .box {
    color: red;
}
```


### 组合器

#### 直接子代组合器
  两个标签之间用>隔开
```
/*
  div标签下的儿子标签p
*/
div > p {
  font-size: 30px;
}
```

#### 紧邻兄弟组合器
  两个标签之间使用+分隔
```
/*
  某个标签后的所有兄弟标签中的第一个会被选中
*/
<ul>
  <li></li>
  <li></li>
  <li></li>
</ul>
//除了第一个li，其他li都可以选中
li + li {
  color: red;
}
```
```
<div>
  <h2>111</h2>
  <p>1</p>
  <p>2</p>
</div>
//选中紧邻h2的第一个p标签
h2 + p {
  color: red;
}
```

#### 一般兄弟组合器
  两个标签之间使用~来进行连接
```
/*
  某个标签后的所有兄弟标签都会被选中
*/
<div>
  <h2>111</h2>
  <p>1</p>
  <p>2</p>
</div>
//选中h2标签之后的所有p标签
h2 ~ p {
  color: red;
}
```

### 伪类
```
E:first-child //父元素下的第一个子元素
E:hover //鼠标悬停在E元素上时匹配
E:focus //匹配获取焦点的E元素
E:active //鼠标在其上按下，但是还没有释放时匹配
E:visited //匹配所有已被点击的链接
E:link  //匹配所有未被点击的链接
```

```
//与界面有关的伪类
E:enabled //匹配表单中激活的元素
E:disabled //匹配表单中禁用的元素
E:checked //匹配radio或者checkbox被选中的元素
E::selection //::selection选择器匹配元素中被用户选中或处于高亮状态的部分。::selection只可以应用于少数的CSS属性：color, background, cursor,和outline。
```

```
//结构型伪类
E:nth-child(n) //匹配第n个元素，第一个编号为1
E:nth-last-child(n) //匹配倒数第n个子元素，第一个编号为1
E:last-child  //等同于E:nth-last-child(1)
E:nth-of-type(n) //与E:nth-child类似，只是匹配使用相同类型标签的元素
E:nth-last-of-type(n) //与E:nth-last-child类似，只是匹配使用相同类型标签的元素
E:first-of-type //等同于E:nth-of-type(1)
E:last-of-type //等同于E:nth-last-of-type(1)
E:only-child  //父元素下只有一个子元素，匹配该子元素
E:only-of-type  //匹配父元素下仅有的一个同类型的子元素
E:empty //匹配不存在内容的元素

//DEMO如下
E:nth-child(odd) //选中奇数位置的元素
E:nth-child(even) //选中偶数位置的元素
E:nth-child(n) //选中第n位置的E元素，这时如果第n位置上不是E类型标签，不会有任何标签选中。这时计数的规则是所有的标签都进行了计数
E:nth-of-type(n) //选中第n位置的E元素，计数的规则只是针对于相同类型的标签来进行的
```

### 伪元素
```
E:after //E元素后设置内容
E:before //E元素前设置内容
E:first-letter //匹配E元素内的第一个字母
E:first-line //匹配E元素内的第一行内容
```