## 算法总结

### 链表相关

1、链表反转
```
//创建一个null，将当前节点的下一个节点保存在临时变量中，然后将当前节点的next指向pre(cur.next=pre)，重新给pre赋值(pre=cur),重新给cur赋值(cur=temp)
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
 ```
 //首先对两个链表的长度进行计算，并将长的链表先进行移动，目的是为了对比两个链表时，长度是一致的，这样对比才能得到公共的起始节点
 function getCommonNode(headA, headB) {
     var len1 = 0, len2 =  0;
     var cur1 = headA, cur2 = headB;
     while (cur1) {
         len1++;
         cur1 = cur1.next;
     }
     while (cur2) {
         len2++;
         cur2 = cur2.next;
     }
     cur1 = headA;
     cur2 = headB;
     if (len1 > len2) {
         for (var i = len1 - len2; i > 0; i--) {
             cur1 = cur1.next;
         }
     } else {
         for (var i = len2 - len1; i > 0; i--) {
             cur2 = cur2.next;
         }
     }
     var minVal = Math.min(len1, len2);
     while (minVal) {
         if (cur1 === cur2) {
             return cur1;
         }
         cur1 = cur1.next;
         cur2 = cur2.next;
         minVal--;
     }
    return null;
 }
 ```

3、层级遍历多叉树
```
//利用递归获取每层节点
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

4、合并两个有序链表
```
//利用一个无意义的节点进行串联，同时对两个链表进行节点之间的对比
function mergeLinklist(headA, headB) {
    var l1 = headA, l2 = headB;
    var dummy = new ListNode(-1);
    var pre = dummy;
    while (l1 && l2) {
        if (l1.val < l2.val) {
            pre.next = l1;
            l1 = l1.next;
        } else {
            pre.next = l2;
            l2 = l2.next;
        }
        pre = pre.next;
    }
    pre.next = l2 ? l2 : l1;
    return dummy.next;
}
```