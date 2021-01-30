## flex布局

在开发过程中，flex布局使用的非常频繁，因为使用它可以很好的实现左右居中和上下居中的布局。

```
//设置flex布局
div {
  display: flex;
}
```

```
/*
  flex-direction（设置容器中元素排列的方向）的取值
  1、row(横向展示)
  2、row-reverse（flex子项反方向进行排列）
  3、column(纵轴展示)
  4、column-reverse(flex子项纵轴上反方向排列显示)
*/
div {
  display: flex;
  flex-direction: row;
}
```

```
/*
  flex-wrap(设置容器中元素可以多行进行排列)的取值
  1、wrap(允许换行显示)
  2、nowrap(默认值，不允许换行显示)
  3、wrap-reverse(允许换行显示，同时反方向排列显示)
*/
div {
  display: flex;
  flex-direction: row;
  //允许换行/不设置，则默认显示为不换行
  flex-wrap: wrap;
}
```

```
//元素间的空间分配和空间分配

/*
  align-items(子元素在容器中纵轴显示的位置)的取值
  1、stretch(子元素的元素与容器的高度一致)
  2、flex-start(子元素的高度为内容高度，并且子元素显示在容器的顶部)
  3、flex-end(子元素的高度为内容高度，并且子元素显示在容器的底部)
  4、center(子元素的高度为内容高度，并且子元素在容器上下局中)
  5、baseline(子元素相对于flex容器的基线对齐)
*/
div {
  display: flex;
  flex-direction: row;
  align-items: center;
}

/*
justify-content（子元素在容器横轴显示的位置）的取值
1、flex-start(子元素靠容器的最左边开始进行排列)
2、center(子元素挨着在容器的中间进行显示)
3、flex-end(子元素靠容器的最右侧进行排列)
4、space-between(左右靠最左侧、最右侧，其他的元素均匀分布)
5、space-around(容器中每个元素左右空间相同，所以我们看到中间元素距离左右元素的距离是最左侧、最右侧元素距离顶部的一倍)
6、space-evenly(容器中每个元素两侧空白间距都相同)
*/
div {
  display: flex;
  flex-direction: row;
  justify-content: center;
}
```

