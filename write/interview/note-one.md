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

3、利用归并算法进行排序
```
function _mergeOrderArr(arr1, arr2) {
    var res = [];
    while (arr1.length && arr2.length) {
        res.push((arr1[0] < arr2[0] ? arr1 : arr2).shift());
    }
    return [...res, ...arr1, ...arr2];
}

function mergeSort(arr) {
    if (arr.length <= 1) {
        return arr;
    }
    var midIndex = arr.length >> 1;
    var leftPart = mergeSort(arr.slice(0, midIndex));
    var rightPart = mergeSort(arr.slice(midIndex, arr.length));
    return _mergeOrderArr(leftPart, rightPart);
}
var arr = [3, 1, 12, 43, 23, 11];
console.log(mergeSort(arr));
```

4、利用小顶堆、大顶堆来求数组中前k个大的元素


5、二维数组进行顺时针进行打印（从左到右，从上到下，从右到左，从下到上）


6、求两个数组的交集
```
function intersection(arr1, arr2) {
    return arr1.filter((item) => {
        return arr2.includes(item);
    })
}
var arr1 = [3, 5, 21, 12, 9], arr2 = [1, 4, 5, 2, 21];
console.log(intersection(arr1, arr2));
```

7、求数组中最大的连续1的个数
```
function getMaxCount(arr) {
    var count = 0, maxCount = 0;
    for (var i = 0, len = arr.length; i < len; i++) {
        if (arr[i] === 1) {
            count++;
        } else {
            maxCount = Math.max(count, maxCount);
            count = 0;
        }
    }
    return Math.max(count, maxCount);
}
var arr = [1, 0, 1, 1, 1, 1, 1, 1, 0, 0, 1, 1, 1 ,1, 1, 1, 1, 1, 1];
console.log(getMaxCount(arr));
```
