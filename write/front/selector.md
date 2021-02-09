### css选择器

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

#### 标签选择器
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