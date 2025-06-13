# Lec28_Sorting
!!! note
    - bogo sort
    - [selection sort](#Selection_sort)
    - [insertion sort](#Insertion_sort)
    - [merge sort](#Merge_sort)
    - TimSort
    - [quick sort](#Quick_sort)

#### Selection_sort
```cpp
void selectionSort(vector<int>& v) {
	for (int i = 0; i < v.size() - 1; i++) {
		int min = i;
		for (int j = i + 1; j < v.size(); j++) {
			if (v[j] < v[min]) {
				min = j;
			}
		}

		if (min != i) {
			int tmp = v[i];
			v[i] = v[min];
			v[min] = tmp;
		}
		// swap(v[i], v[min]);
	}
}
```

#### Insertion_sort
```cpp
void insertionSort(vector<int>& v) {
	for (int i = 1; i < v.size(); i++) {
		int cur = v[i]; // 当前要插入排序的元素
		int j = i - 1;

		while (j >= 0 && v[j] > cur) {
			v[j + 1] = v[j];
			j--;
		}
		v[j + 1] = cur;
	}
}
```

选择排序和插入排序的时间复杂度都是$O(N^2)$

#### Merge_sort
```cpp
void mergeSort(vector<int>& v) {
	if (v.size() >= 2) {
		// divide
		vector<int> left(v.begin(), v.begin() + v.size() / 2);
		vector<int> right(v.begin() + v.size() / 2, v.end());
		
		// sort
		mergeSort(left);
		mergeSort(right);

		// merge
		int i1 = 0, i2 = 0;
		for (int i = 0; i < v.size(); i++) {
			if (i2 >= right.size() || 
			   (i1 < left.size() && left[i1] < right[i2])) {
				v[i] = left[i1++];
			} else {
				v[i] = right[i2++];
			}
		}
	} // else: base case. Do nothing.
}
```

这里有很多不必要的vector复制，改为传递索引直接在原数组上操作：

**完整代码**：

```cpp
#include "console.h"
#include "simpio.h"
#include <vector>

using namespace std;

void mergeSort(vector<int>& v);
void mergeSortHelper(vector<int>& v, vector<int>& tmp, int left, int right);
void merge(vector<int>& v, vector<int>& tmp, int left, int mid, int right);

int main() {
    vector<int> v = {3, 10, 4, 1, 5, 0, 2, 6, 5, 3, 5};
    mergeSort(v);
    for (int num : v) {
        cout << num << " ";
    }
    cout << endl;
    return 0;
}

void mergeSort(vector<int>& v) {
    if (v.size() < 2) {
        return;
    }
    vector<int> tmp(v.size());
    mergeSortHelper(v, tmp, 0, v.size() - 1);
}

void mergeSortHelper(vector<int>& v, vector<int>& tmp, int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;

        mergeSortHelper(v, tmp, left, mid);
        mergeSortHelper(v, tmp, mid + 1, right);

        merge(v, tmp, left, mid, right);
    }
}

void merge(vector<int>& v, vector<int>& tmp, int left, int mid, int right) {
    int i1 = left; // 左子数组起始索引
    int i2 = mid + 1; // 右子数组起始索引
    int j = left; // 临时存放结果数组起始索引

    // 遍历左右子数组，比较大小，小的放入tmp。
    // 直到一方遍历完，结束循环
    while (i1 <= mid && i2 <= right) {
        if (v[i1] < v[i2]) {
            tmp[j++] = v[i1++];
        } else {
            tmp[j++] = v[i2++];
        }
    }

    // 哪个有剩就将剩下的元素放入tmp数组
    while (i1 <= mid) {
        tmp[j++] = v[i1++];
    }
    while (i2 <= right) {
        tmp[j++] = v[i2++];
    }

    // 此时tmp数组已排好序，复制回原数组即可
    for (int k = left; k <= right; k++) {
        v[k] = tmp[k];
    }
}
```

#### Quick_sort


归并排序是稳定的排序算法，而快速排序不稳定。
 
稳定性：相等元素的相对顺序在排序前后不变

---

