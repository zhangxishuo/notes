# 排序基础

## 为什么要学习O(n^2)的排序算法？

- 一切的基础
- 编码简单，易于实现，是一些简单场景的首选
- 在一些特殊情况下，简单的排序算法更有效
- 简单的排序算法衍生出复杂的排序算法
- 作为子过程，改进更复杂的排序算法

## 随机数组生成

```cpp
int* generateRandomArray(int n, int min, int max) {
    assert(min <= max);

    random_device rd;
    default_random_engine gen = default_random_engine(rd());
    uniform_int_distribution<int> dis(min,max);

    int* arr = new int[n];
    for (int i = 0; i < n; i ++) {
        arr[i] = dis(gen);
    }
    return arr;
}
```

## 选择排序

选择当前数组中的最小的元素，与第一个元素交换，选择第二小的元素，与第二个元素交换。

![选择排序](assets/selection-sort.gif)

```cpp
template<typename T>
void selectionSort(T* arr, int n) {
    for (int i = 0; i < n; i ++) {
        /** 寻找 [i, n) 区间里的最小值 */
        int minIndex = i;
        for (int j = i + 1; j < n; j ++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }

        /** 交换元素位置 */
        swap(arr[i], arr[minIndex]);
    }
}
```

## 插入排序

就像扑克牌一样，从第二个元素开始，前面的所有元素顺序有序，后面的新元素插入到自己在前面数组中的适当位置。

在排序近乎有序的数组时，效率非常高。如果数组已经有序，复杂度为`O(n)`。

![插入排序](assets/insertion-sort.gif)

```cpp
template<typename T>
void insertionSort(T* arr, int n) {
    /** 从第二个元素遍历到最后 */
    for (int i = 1; i < n; i ++) {
        /** 寻找适合当前元素的位置 */
        T current = arr[i];
        int j;
        for (j = i; j > 0 && current < arr[j - 1]; j --) {
            arr[j] = arr[j - 1];
        }
        arr[j] = current;
    }
}
```