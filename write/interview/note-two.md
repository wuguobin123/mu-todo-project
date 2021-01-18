## 算法总结

### 链表相关

1、链表反转
```
function reverseList(head) {
    var pre = null;
    var cur = head;
    while (cur) {
        var temp = cur.next;
        cur.next = pre;
        pre = cur;
        cur = temp;
    }
    return pre;
}
```

2、找到两个链表公共部分的起始结点

3、遍历多叉树
```


```