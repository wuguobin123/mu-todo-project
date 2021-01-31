## flex布局

在开发过程中，flex布局使用的非常频繁，因为使用它可以很好的实现左右居中和上下居中的布局。

### 作用在容器上的属性
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
  flex-flow(该属性是flex-direction和flex-wrap两个属性的缩写)的取值
  语法：flex-flow: <flex-direction> | <flex-wrap>
  div {
    display: flex;
    flex-flow: row wrap;
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

```
/*
  align-content(多行显示时，纵轴方向上控制在容器中显示的位置)的取值
  1、stretch(每行子项等比例拉伸，整列会撑满容器)
  2、flex-start(在容器的顶部开始排列)
  3、flex-end(在容器的底部开始排列)
  4、center(在容器的中间位置排列)
  5、space-around(容器中每个元素左右空间相同，所以我们看到中间元素距离左右元素的距离是最左侧、最右侧元素距离顶部的一倍)
  6、space-between(上下子项靠近顶部、底部，其他的元素均匀分布)
  7、space-evenly（容器中每个元素两侧空白间距都相同）

*/
```

### 作用在容器中子项的属性
```
/*
  order(控制某个子项在容器中显示的排序位置)
  容器中子项order的默认值为0，想要将某个子项的位置放在前边可以将某项设置为更小的值，比如-1
*/
```

```
/*
  flex-grow每个子项默认值为0，也就是容器中每个子项不进行扩展(扩展容器中每个子项的宽度，扩展所侵占的空间是出去子项元素占的空间剩下的空间)
  1、如果给容器中的单个子项设置flex-grow
  如果flex-grow的值小于1，那么该子项的扩展宽度为flex-grow的值与剩余空间的乘积
  如果flex-grow的值大于1，那么该子项的扩展宽度独占整个剩余空间的宽度
  2、如果给容器中的多个子项设置flex-grow
  如果多个flex-grow相加的和小于1，那么每项的扩展宽度为每个flex-grow的值与整个剩余空间的乘积，此时还存在剩余空间
  如果多个flex-grow相加的和大于1，那么按照比例对剩余空间进行划分，然后分摊给对应的子项中。此时不再存在剩余空间
*/

<div class="parent">
  <div class="child"></div>
  <div class="child"></div>
  <div class="child"></div>
</div>

.parent {
  display: flex;
}
.child {
  flex-grow: 0.25;
}
```

```
/*
  flex-shrink每个子项的默认值为1（当flex容器的空间不足是，子项会进行缩放）
  flex-shrink的值不支持负值，默认值为1，也就是默认所有子项都进行缩放。如果设置为0，则不进行缩放，保持原有fit-content的宽度
  1、如果给单个子项设置flex-shrink
  - 当flex-shrink的值小于1，那么进行不完全缩放，会有一部分溢出flex容器
  - 当flex-shrink的值等于大于1，那么进行完全缩放，整好填满flex的容器
  2、如果给多个子项设置flex-shrink
  - 当子项flex-shrink的和小于1，那么进行不完全缩放，会有一部分内容溢出flex容器，每个元素收缩尺寸占完全收缩的尺寸”的比例就是设置的flex-shrink的值。
  - 当子项flex-shrink的和大于1，那么会进行完全缩放，每个子项收缩的比例和flex-shrink值的比例一样
*/

<div class="parent">
  <div class="child"></div>
  <div class="child"></div>
  <div class="child"></div>
</div>

.parent {
  display: flex;
}
.child {
  flex-shrink: 0.25;
}
```

```
/*
  flex-basis定义了在分配剩余空间之前设置子项的默认宽度。
  flex-basis默认值为auto，如果设置了width，按照width来设置，如果没有设置，则根据内容进行显示
  如果同时设置了flex-basis和width，则会忽略width，根据flex-basis来进行显示。实际上不建议为子项设置width，因为不够弹性
  当空间不足的时候，子项的宽度通常不是设置的flex-basis的值，因为子项会进行收缩
*/
```

```
/*
  flex属性是flex-grow、flex-shrink和flex-basis的缩写
  flex的默认值为 0 1 auto
  flex: none;等同于 flex: 0 0 auto;
  flex: auto; 等同于 flex: 1 1 auto;
*/  
```

```
/*
  align-self控制某个子项在纵轴上显示的位置
  - auto 继承align-items属性
  - stretch 子项的高度和容器的高度一致
  - flex-start  在容器的顶部开始显示
  - flex-end 在容器的底部开始显示
  - center  在容器保持上下居中
  - baseline  与容器中的基线保持一致
*/
```

注意： 在使用flex布局时，给子项设置float、clear、vertical-align属性是无效的。