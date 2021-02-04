### 常用css属性总结

在实际开发中，发现对一些css属性应用的不是太熟练，这里总结一下，供自己查阅；

#### box-shadow
```
//三个值时：x轴的偏移量，y轴的偏移量，阴影的颜色
box-shadow: 1px 1px red; 

//四个值时：x轴的偏移量，y轴的偏移量，阴影模糊半径，阴影的颜色
box-shadow: 1px 1px 3px red;

//五个值时：x轴的偏移量，y轴的偏移量，阴影模糊半径，阴影扩展半径，阴影的颜色
box-shadow: 1px 1px 3px 2px red;

//阴影向内显示
box-shadow: inset 5em 1em red;

//多个数量的阴影，需要用逗号隔开
box-shadow: 1px 2px red, 0px -2px blue;
```

#### cursor
```
//将鼠标设置为小手形状
cursor: pointer;
```

#### text-decoration
```
//将a标签下边的横线设置为不显示
text-decoration: none;
```

####  transition
```
//属性多长时间可以完成显示
transition-duration: 0.3s;
//设置对哪个属性进行过渡显示
transition-property: transform;

//通过伪类（hover，focus，active）、js也可以触发过渡。使用过渡的前提是确定对某个属性进行过渡定义
transition: transition-property | transition-property（这两个属性的缩写）
```

#### transform
该属性可以设置盒子（display:block）为倾斜、缩放、平移、旋转
```
transform的值可以设置为none，一个或者多个值
```

