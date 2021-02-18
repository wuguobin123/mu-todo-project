## 算法总结

### 数组相关
数组思路总结：
- 如果数组是有序的，可以思考的方向有二分法，左右指针向中间移动的方式来处理。
- 对数组中的数字进行排序，时间复杂度高的算法是快速排序和归并排序，两种方式都是对原有数组进行分组来进行比较，最后合并。其实现也是使用递归的方式来进行。
- 二维数组的遍历，涉及到行指针和列指针的移动（快手面试问的这个方向的问题）

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

8、利用广度优先获取全数组排序
```
function _combine(arr, temp, res) {
    if (arr.length === temp.length) {
        res.push(temp);
        return;
    }
    for (var i = 0, len = arr.length; i < len; i++) {
        if (!temp.includes(arr[i])) {
            temp.push(arr[i]);
            _combine(arr, JSON.parse(JSON.stringify(temp)), res);
            temp.pop();
        }
    }
}

function combine(arr) {
   var res = [];
   _combine(arr, [], res);
   return res;
}
var arr = [1, 2, 3];
console.log(combine(arr));
```

9、给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组的开始位置和结束位置。你的算法时间复杂度必须是 O(log n) 级别
```
function getPositions(arr, target) {
    var start = 0, end = arr.length - 1;
    var index = -1;
    while (start <= end) {
        var mid = (start + end) >> 1;
        if (arr[mid] === target) {
            index = mid;
            break;
        } else if (arr[mid] < target) {
            start = mid + 1;
        } else {
            end = mid - 1;
        }
    }
    var left = index, right = index;
    while (arr[left - 1] === target) {
        left--;
    }
    while (arr[right + 1] === target) {
        right++;
    }
    return [left, right];
}
var arr = [2, 3, 6, 7, 7, 7, 9, 10];
console.log(getPositions(arr, 7));
```

10、删除有序数组的重复项
```
function removeDuplicates(arr) {
    var i = 0;
    for (var j = 1, len = arr.length; j < len; j++) {
        if (arr[i] !== arr[j]) {
            i++;
            arr[i] = arr[j];
        }
    }
    return i + 1;
}
var arr = [1, 3, 5, 5, 5, 7];
console.log(removeDuplicates(arr));
```