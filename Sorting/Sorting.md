# 排序

## 排序算法类模板

```java
public class Example {

    public static void sort(Comparable[] a) {
        // 具体算法见下文
    }

    private static boolean less(Comparable v, Comparable w) {
        return v.comparableTo(w) < 0;
    }

    private static void exch(Comparable[] a. int i, int j) {
        Comparable t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
  
    private static void show(Comparable[] a) {
        for (int i = 0; i < a.length; i++)
            StdOut.print(a[i] + " ");
        StdOut.println();
    }
  
    public static void isSorted(Comparable[] a) {
        for (int i = 1; i < a.length; i++) {
            if (less(a[i], a[i-1])) {
                return false;
            }
        }
        return true;
    }
}
```



## 简单排序

### 选择排序

一种最简单的排序算法：首先，找到数组中最小的元素，其次，将它和数组的第一个元素交换位置（如果第一个元素就是最小元素那么它就和自己交换）。再次，在剩下的元素中找到最小的元素，将它与数组的第二个元素交换位置。如此往复，直到将整个数组排序。

特点：

* 运行时间和输入无关；
* 数据移动的最少。

```java
public class Selection {

    public static void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 0; i < N; i++) {
            int min = i;
            for (int j = i + 1; j < N; j++) {
                if(less(a[j], a[min])) {
                    min = j;
                }
            }
            exch(a, i, min);
        }
    }

    // less(), exch(), show(), isSorted()方法请见模板
}
```



### 插入排序

与选择排序一样，当前索引的左边都是有序的，但他们的最终位置还不确定，为了给更小的元素腾出空间，他们可能会被移动。但是当索引到达数组的右端时，数组排序就完成了。

特点：

* 插入排序所需的时间取决于输入中元素的初始顺序，当用插入排序对一个有序数组排序时，运行时间是线性的 O(N)；
* 插入排序对于**部分有序**的数组十分高效；
* 插入排序对于**小规模**数组也十分高效。

```java
public class Insertion {

    public static void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 1; i < N; i++) {
            for (int j = i; j > 0 && less(a[j], a[j - 1]); j--) {
                exch(a, j, j - 1);
            }
        }
    }

    // less(), exch(), show(), isSorted()方法请见模板
}
```



## 高效排序

### 希尔排序

对于大规模乱序数组插入排序很慢，因为它只会交换相邻的元素，因此元素只能一点一点地从数组的一端移动到另一端。

希尔排序为了加快速度简单地改进了插入排序，交换不相邻的元素以对数组的局部进行排序，并最终用插入排序将局部有序的数组排序。

希尔排序的思想是使数组中任意间隔为 h 的元素都是有序的。这样的数组被称为 **h 有序数组**。换句话说，一个 h 有序数组就是 h 个互相独立的有序数组编织在一起组成的一个数组。

在进行排序时，如果 h 很大，我们就能将元素移动到很远的地方，为实现更小的 h 有序创造方便。用这种方式，对于任意以 1 结尾的 h 序列，我们都能够将数组排序。这就是希尔排序。

实现希尔排序的一种方法是对于每个 h，用插入排序将 h 个子数组独立地排序。但因为子数组时相互独立的，一个更简单的方法是在 h- 子数组中将每个元素交换到比它大的元素之前去（将比它的元素向右移动一格）。只要在插入排序的代码中将移动元素的距离由 1 改为 h 即可。这样，希尔排序的实现就转化为了一个类似于插入排序但使用不同增量的过程。

```java
public class Shell {

    public static void sort(Comparable[] a) {
        int N = a.length;
        int h = 1;
        while (h < N / 3) {
            h = 3 * h + 1;
        }
        while (h >= 1) {
            for(int i = h; i < N; i++) {
                for(int j = i; j >= h && less(a[j], a[j - h]); j -= h) {
                    exch(a, j, j - h);
                }
            }
            h /= 3;
        }
    }

    // less(), exch(), show(), isSorted()方法请见模板
}
```



### 归并排序

归并排序基于*归并*这个简单的操作，即将两个有序的数组归并称为一个更大的有序数组。

要将一个数组排序，可以先（递归地）将它分为两半分别排序，然后将结果归并起来。

归并排序最吸引人的性质是它能够保证将任意长度为 N 的数组排序所需时间和 NlogN 成正比；它的主要缺点则是它所需的额外空间和 N 成正比。

**自顶向下的归并排序**

```java
public class Merge {

    private static Comparable[] aux;

    public static void sort(Comparable[] a) {
        aux = new Comparable[a.length];
        sort(a, 0, a.length - 1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }
        int mid = lo + (hi - lo) / 2;
        sort(a, lo, mid);
        sort(a, mid + 1, hi);
        merge(a, lo, mid, hi);
    }

    private static void merge(Comparable[] a, int lo, int mid, int hi) {
        int i = lo;
        int j = mid + 1;

        for(int k = hi; k >= lo; k--) {
            aux[k] = a[k];
        }

        for(int k = lo; k <= hi; k++) {
            if      (i > mid)              a[k] = aux[j++];
            else if (j > hi)               a[k] = aux[i++];
            else if (less(aux[i], aux[j])) a[k] = aux[i++];
            else                           a[k] = aux[j++];
        }
    }

    // less(), exch(), show(), isSorted()方法请见模板
}
```



**自底向上的归并排序**

递归实现的归并排序是算法设计中**分治思想**典型应用：将一个大问题分割成小问题分别解决，然后用所有的小问题的答案来解决整个大问题。

```java
public class MergeBU {

    private static Comparable[] aux;

    public static void sort(Comparable[] a) {
        int N = a.length;
        aux = new Comparable[N];
        for(int sz = 1; sz < N; sz += sz)
            for(int lo = 0; lo < N - sz; lo += sz + sz)
                merge(a, lo, lo + sz - 1, Math.min(lo + sz + sz -1, N - 1));
    }



    public static void merge(Comparable[] a, int lo, int mid, int hi) {
        int i = lo;
        int j = mid + 1;
        for(int k = lo; k <= hi; k++)
            aux[k] = a[k];
        
        for(int k = lo; k <= hi; k++)
            if (i > mid) {
                a[k] = aux[j++];
            } else if (j > hi) {
                a[k] = aux[i++];
            } else if (less(aux[i], aux[j])) {
                a[k] = aux[i++];
            } else {
                a[k] = aux[j++];
            }
    }
  
    // less(), exch(), show(), isSorted()方法请见模板
}
```



### 快速排序

快速排序，它可能是应用最广泛的排序算法了。快速排序流行的原因是它实现简单、适用于各种不同的输入数据且在一般应用中比其他排序算法快得多。

快速排序引人注目的特点包括它是原地排序（只需要一个很小的辅助栈），且将长度为 N 的数组排序所需时间和 NlgN 成正比。

```java
public class Quick {

    public static void sort(Comparable[] a) {
        sort(a, 0, a.length - 1);
    }

    private static void sort(Comparale[] a, int lo, int hi) {
        if (hi <= lo) {
            return ;
        }

        int j = partition(a, lo, hi);
        sort(a, lo, j - 1);
        sort(a, j + 1, hi);
    }

    private static int partition(Comparable[] a, int lo, int hi) {
        int i = lo;
        int j = hi + 1;
        Comparable v = a[lo];
        while (true) {
            while(less(a[++i], v)) if(i == hi) break;
            while(less(v, a[--j])) if(j == lo) break;
            if(i >= j) break;
            exch(a, i, j);
        }
        exch(a, lo, j);
        return j;
    }
  
    // less(), exch(), show(), isSorted()方法请见模板
}
```



