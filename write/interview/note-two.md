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
function _levelOrder(node, res, level) {
    if (!node) {
        return;
    }
    if (!res[level]) {
        res[level] = [];
    }
    res[level].push(node.val);
    for (var i = 0, len = node.children.length; i < len; i++) {
        _levelOrder(node.children[i], res, level + 1);
    }
}

function levelOrder(head) {
    var res = [];
    _levelOrder(head, res, 0);
    return res;
}
```