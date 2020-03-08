# 二分搜索树

## 二分查找法

对于有序数列，才能使用二分查找法。

```cpp
/**
 * 如果找到 target 返回索引 index
 * 如果找不到 返回 -1
 */
template<typename T>
int binarySearch(T* arr, int n, T target) {
    /** 在 arr[l, r] 范围内查找 target */
    int l = 0, r = n - 1;
    while (l <= r) {
        int mid = l + (r - l) / 2;
        if (arr[mid] == target) {
            return mid;
        }
        if (target < arr[mid]) {
            r = mid - 1;
        } else {
            l = mid + 1;
        }
    }
    return -1;
}
```

## 二分搜索树

不仅可高效查找数据，还可以高效地插入、删除数据。

- 二叉树
- 每个节点的键值大于左孩子
- 每个节点的键值小于右孩子
- 以左右孩子为根的子树仍为二分搜索树
- 不一定是完全二叉树

![](assets/tree.png)