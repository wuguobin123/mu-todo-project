## 算法总结

### 数组相关

1、有序数组arr = [3, 5, 6, 12, 23, 25]，找出其中两个元素的和为一个目标值target为9，判断是否存在

```
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

