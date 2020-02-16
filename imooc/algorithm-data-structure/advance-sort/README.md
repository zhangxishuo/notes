# 高级排序算法

## 归并排序

![归并排序](.assets/merge-sort.gif)

对数组进行划分，对数组左右两侧分别进行归并排序，并对排好序的两侧进行合并。

```cpp
/** 合并数组 arr[l, mid] 和 arr[mid + 1, r] 两部分 */
template<typename T>
void __merge(T* arr, int l, int mid, int r) {
    /** 复制数组 */
    T aux[r - l + 1];
    for (int i = l; i <= r; i ++) {
        aux[i - l] = arr[i];
    }

    int i = l, j = mid + 1;
    for (int k = l; k <= r; k ++) {
        if (i > mid) {
            /** 左侧完毕，填充右侧的数据 */
            arr[k] = aux[j - l];
            j ++;
        } else if (j > r) {
            /** 右侧完毕，填充左侧的数据 */
            arr[k] = aux[i - l];
            i ++;
        } else if (aux[i - l] < aux[j - l]) {
            /** 左侧的小，填充左侧的数据 */
            arr[k] = aux[i - l];
            i ++;
        } else {
            /** 右侧的小，填充右侧的数据 */
            arr[k] = aux[j - l];
            j ++;
        }
    }
}

/** 对 arr [l, r] 的范围进行排序 */
template<typename T>
void __mergeSort(T* arr, int l, int r) {
    if (l >= r) {
        return;
    }

    /** 左右分别进行归并排序 */
    int mid = (l + r) / 2;
    __mergeSort(arr, l, mid);
    __mergeSort(arr, mid + 1, r);

    /** 对数组左右进行合并 */
    if (arr[mid] > arr[mid + 1]) {
        __merge(arr, l, mid, r);
    }
}

template<typename T>
void mergeSort(T* arr, int n) {
    __mergeSort(arr, 0, n - 1);
}
```