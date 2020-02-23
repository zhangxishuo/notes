# 高级排序算法

## 归并排序

![归并排序](assets/merge-sort.gif)

对数组从中间进行划分，对数组左右两侧分别进行归并排序，并对排好序的两侧进行合并。

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

## 快速排序

![快速排序](assets/quick-sort.gif)

选取一个基准，对数组进行划分，数组中左侧元素都比基准小，数组中右侧元素都比基准大，再对数组划分后的左右部分分别进行快速排序。

```cpp
/** 对 arr [l, r] 的范围进行快速排序划分 */
template<typename T>
int __partition(T* arr, int l, int r) {
    /** 选取基准 */
    T value = arr[l];

    /** (l, i] 的范围比 value 小，(i, j) 的范围比 value 大 */
    int i = l;
    for (int j = l + 1; j <= r; j ++) {
        if (arr[j] < value) {
            swap(arr[j], arr[i + 1]);
            i ++;
        }
    }

    /** 将基准交换到中间 */
    swap(arr[i], arr[l]);
    return i;
}

/** 对 arr [l, r] 的范围进行排序 */
template<typename T>
void __quickSort(T* arr, int l, int r) {
    if (l >= r) {
        return;
    }

    /** 划分 */
    int p = __partition(arr, l, r);

    /** 对数组左右分别进行快速排序 */
    __quickSort(arr, l, p - 1);
    __quickSort(arr, p + 1, r);
}

template<typename T>
void quickSort(T* arr, int n) {
    __quickSort(arr, 0, n - 1);
}
```

## 快速排序优化

当整个数组完全有序，最差情况，退化为`O(n^2)`。

随机选择一个基准，将这个基准与第一个元素交换，其他逻辑不变，使得快速排序适应近乎有序的数组。

## 双路快排

如果数组中有大量等于基准的元素，快速排序会使树失衡，性能较差。

```cpp
/** 对 arr [l, r] 的范围进行快速排序划分 */
template<typename T>
int __partition2(T* arr, int l, int r) {
    /** 选取基准 */
    T value = arr[l];

    /** arr[l + 1, i) <= value; arr(j, r] >= value */
    int i = l + 1;
    int j = r;

    while (i <= j) {
        while (arr[i] <= value && i <= j) {
            i ++;
        }
        while (arr[j] >= value && i <= j) {
            j --;
        }
        swap(arr[i], arr[j]);
    }

    /** 将基准交换到中间 */
    swap(arr[i], arr[l]);
    return i;
}
```

## 三路快排

使用三路快排，不考虑等于基准的情况。

```cpp
/** 对 arr [l, r] 的范围进行排序 */
template<typename T>
void __quickSort(T* arr, int l, int r) {
    if (l >= r) {
        return;
    }

    /** 划分, arr[l + 1, lt) < value, arr[lt, i] == value, arr(gt, r] > value */
    int value = arr[l];
    int lt = l + 1;
    int gt = r;
    int i = l + 1;
    while (i <= gt) {
        if (arr[i] < value) {
            swap(arr[lt], arr[i]);
            lt ++;
            i ++;
        } else if (arr[i] > value) {
            swap(arr[i], arr[gt]);
            gt --;
        } else {
            i ++;
        }
    }
    swap(arr[l], arr[lt - 1]);
    lt --;

    /** 对数组左右分别进行快速排序 */
    __quickSort(arr, l, lt - 1);
    __quickSort(arr, gt + 1, r);
}

template<typename T>
void quickSort(T* arr, int n) {
    __quickSort(arr, 0, n - 1);
}
```

## 分治算法

`Merge Sort`和`Quick Sort`都使用了分治算法。

## 衍生问题

逆序对问题(一个数组中逆序对的数量，标志了数组的有序程度)：

暴力算法：考察每一个数对。算法复杂度：`O(n^2)`。

使用归并排序的数组合并思想，在数组合并中，比较数据元素时，累加逆序对。

取数组中第`n`大的元素：

排序再获取。算法复杂度：`O(nlogn)`。

使用快速排序的划分思想，划分之后，基准所在的位置就是在整个有序数组中所在的位置。