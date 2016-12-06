---
layout: post
title: "浅酌算法:快速排序"
description: "关于快速排序的一些事"
category: 数据结构与算法
tags: [算法, 排序]
---

快速排序算是平均性能最好的排序算法了，是一种划分交换的排序算法，采用的是分治的策略。

快速排序的基本思想是：通过一趟排序将排序的数据划分成两部分，其中一部分的数据都比另一部分的所有数据都大，然后按照该方法对这两个部分的数据再进行排序排序，整个排序过程是个递归的过程。

步骤：

1. 从待排序数组中挑出一个元素，作为基准数;
2. 划分过程中，比基准数小的数放在左边，比基准数大的数放在右边（相同可以放在任一边）;
3. 再分别对左右两个区块递归重复操作1、2，直到各区块只有一个元素。


        void quicksort(int a[], int left, int right)
        {
            int i;
        
            if (right <= left)
                return;
            i = partition(a, left, right);
            quicksort(a, left, i-1);
            quicksort(a, i+1, right);
        }


上面代码中left指数组的左下标, right指数组的右下标。这段递归的代码很容易理解，关键的函数就是划分函数partition。partition函数返回的是划分时基准数的下标。

下面来看划分函数的实现过程：

举例来说，要将数组`a[] = {9, 5, 3, 7, 8, 2, 1, 4, 6}`排序。

1. key指向基准数，这里我们取数组的最后一个数组为基准数，`key = a[right]`。
2. i 下标指向小于或等于key的数的最后一个，j 下标指向还未判断的数，i+1 就是大于key的数

    ![quicksort](/assets/images/quicksort1.png)

3. 如果`a[j]`大于`key`，那么交换`a[i+1]`和`a[j]`。

    ![quicksort](/assets/images/quicksort2.png)

    这是`a[j]`的值位于小于或等于key的区域，`a[i+1]`的值还是位于大于key区域。


下面是实现代码：

    int partition(int a[], int left, int right)
    {
        int i, j, key;
    
        i = left - 1;
        key = a[right];
    
        for(j = left; j < right; j++)
        {
            if (a[j] <= key)
            {
                i++;
                swap(a+i, a+j);
            }
        }
        swap(a+i+1, a+right);
        return i+1;
    }
