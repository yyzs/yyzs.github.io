---
layout: post
title: "浅酌算法:冒泡排序和选择排序"
description: "关于冒泡排序和选择排序的一些事"
category: 数据结构与算法
tags: [算法, 排序]
---

冒泡排序(bubble sort)和选择排序(selection sort)都是比较简单的排序算法，简洁。第一次接触到这两个算法是在高中的计算机课程上，当年还是用VB写的,在这里我用C语言来实现。

假设需要排序的是一个int类型的数组array，数组中有n个元素，最终需要把它按从小到大排序。

##冒泡排序(Bubble Sort)
冒泡排序简单来说就是一遍又一遍的扫描这个数组，比较相邻的两个元素，如果`array[i-1] > array[i]`,即前一个元素比当前元素大，那么将这两个元素交换。经过一趟的排序，最大的元素应该在数组的末尾了。

```c
void BubbleSort(int array[], int n)
{
    int i, j;
    for (i = 0; i < n-1; i++) {
        for (j = 0; j < n-1-i; j++) {
            if (array[j-1] > array[j])
                swap(a+j-1, a+j);
        }
    }
}
```

下面考虑一种情况，如果某一次遍历的数组的时候，未发生交换，这个时候数组已经都排序好了，但上面的算法不会作出任何判断，还是要继续遍历，直到遍历了n-1次。这样造成时间上的浪费，我们对其进行优化改进。改进后如下：

增加一个标志flag来表示该趟遍历是否进行了排序，0为未排序，1为进行了排序。

```c
void BubbleSort(int array[], int n)
{
    int i, j;
    int flag;
    for (i = 0; i < n-1; i++) {
        flag = 0;
        for (j = 0; j < n-1-i; j++) {
            if (array[j-1] > array[j]) {
                flag = 1;
                swap(a+j-1, a+j);
            }
        }
        if (flag == 0)
            break;
    }
}
```

##选择排序(Selection Sort)
冒泡排序是不符合序列的要求就交换相邻元素，而选择排序是先找出最小元素，存放在数组起始位置，也就是`i=0`的位置，然后找出第二小的元素，存在在`i=1`的位置。以此类推，直到所有元素都排序完毕。

```c
void SelectionSort(int array[], int n)
{
    int i, j;
    int min_index;
    for (i = 0; i < n-1; i++) {
        min_index = i;
        for (j=i+1; j < n; j++) {
            if (array[j] < array[min_index)
                min_index = j;
        }
        swap(array+i, array+min_index);
    }
}
```

冒泡排序和选择排序都是算法中比较简单和容易实现的算法。通常都被用来对程序设计入门的同学介绍算法的概念。
