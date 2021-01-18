## 算法总结

### 数组相关

1、有序数组arr = [3, 5, 6, 12, 23, 25]，找出其中两个元素的和为一个目标值target为9，判断是否存在

```
//利用双指针，首尾指针向中间移动的策略来实现
function isExit(arr, target) {
    var start = 0, end = arr.length - 1;
    while (start < end) {
        if (arr[start] + arr[end] === target) {
            return true;
        } else if (arr[start] + arr[end] > target) {
            end--;
        } else {
            start++;
        }
    }
    return false;
}
var arr = [3, 5, 6, 12, 23, 25], target = 9;
console.log(isExit(arr, target));
```

2、使用快速排序法对数组进行排序
```
function quickSort(arr) {
    if (arr.length <= 1) {
        return arr;
    }
    var midIndex = arr.length >> 1;
    var midNum = arr.splice(midIndex, 1)[0];
    var part1 = [], part2 = [];
    for (var i = 0, len = arr.length; i < len; i++) {
        if (arr[i] < midNum) {
            part1.push(arr[i]);
        } else {
            part2.push(arr[i]);
        }
    }
    return [...quickSort(part1), midNum, ...quickSort(part2)];
}
var arr = [3, 1, 12, 43, 23, 11];
console.log(quickSort(arr));
```

