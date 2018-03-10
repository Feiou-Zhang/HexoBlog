---
title: 排序
date: 2018-02-10 02:07:08
tags: [Sort]
categories: [Algorithm]
---

### 快排，归并排模板

#### quickSort

```java
public class QuickSort {
    /**
     * 思路：递归函数3个形参，数组， start， end 的index
     * 当start 大于等于 end 时。函数停止
     * 首先找一个pivot 变量，可以简单的选择第一个元素
     * 然后当 start 小于 end的时候，循环，
     * 当 end指针的数字 大于等于pivot的时候，左移end指针，结束后 swap start 和end
     * 当start指针上的数字， 小于等于pivot的时候，右移start指针，结束后，swap start 和 end
     * 结束大循环后， start和end重叠，而且，这个位置上的数，就是pivot，
     * 而且这个pivot一定在正确的位置上，因为，比他小的都在他左边，右边同理
     * 然后就可以递归的做左边和右边
     * */
    public void quickSort(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
    }
    private void quickSort(int[] nums, int start, int end) {
        if (start >= end) {
            return;
        }
        int s = start;
        int e = end;
        int pivot = nums[s];
        while (s < e) {
            while (s < e && nums[e] >= pivot) {
                --e;
            }
            swap(nums, s, e);
            while (s < e && nums[s] <= pivot) {
                ++s;
            }
            swap(nums, s, e);
        }
        quickSort(nums, start, s - 1);
        quickSort(nums, s + 1, end);
    }
    private void swap(int[] nums, int i, int j) {
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}

```

#### mergeSort

```java
public class Sort {
    public void mergeSort(int[] nums) {
        sort(nums, 0, nums.length - 1);
    }
    private void meger(int[] nums, int start, int mid, int end) {
        int[] tem = new int[nums.length];
        int k = 0;
        int start1 = start;
        int start2 = mid + 1;
        while (start1 <= mid && start2 <= end) {
            if (nums[start1] <= nums[start2]) {
                tem[k++] = nums[start1++];
            } else {
                tem[k++] = nums[start2++];
            }
        }
        while (start1 <= mid) {
            tem[k++] = nums[start1++];
        }
        while (start2 <= end) {
            tem[k++] = nums[start2++];
        }
        System.arraycopy(tem, 0, nums, start, k);
    }
    private void sort(int[] nums, int start, int end) {
        if (start >= end) {
            return;
        }
        int mid = start + (end - start) / 2;
        sort(nums, start, mid);
        sort(nums, mid + 1, end);
        meger(nums, start, mid, end);
    }
}

```
#### 自下而上的并归排序，轻易不要尝试，很复杂

mergesort(buttom up)

```java
public class MergeSort {
    public void merge(int[] nums, int[] copy, int lo, int hi, int mid){
        int start = lo, //cache the start index
            k = lo, // k will the be index of the copy array
            lo2 = mid + 1; //the lo2 will be the lowest index of the second half
        while(lo <= mid && lo2 <= hi){
            if(nums[lo] <= nums[lo2]){
                copy[k++] = nums[lo++];
            }else{
                copy[k++] = nums[lo2++];
            }
        }
        //in case there is still any element left
        while(lo <= mid) copy[k++] = nums[lo++];
        while(lo2 <= hi) copy[k++] = nums[lo2++];
        
        //copy back to the original array
        System.arraycopy(copy, start, nums, start, hi - start + 1);
    }
    public void sort(int[] nums){
        int n = nums.length;
        int[] copy = new int[n];
        //we first deal with size 1, then each time we increment the size by 2 times
        for(int size = 1; size < n; size *= 2){
            //as we do not need to do anything if there is only one element in the range, 
            //so we can skip that by increamenting size + size
            for(int lo = 0; lo < n - size; lo += size + size){
                // we know the mid for sure, but not the hi, 
                //because there could be some cases that the last parts is shorter than the size
                int mid = lo + size - 1;
                int hi = Math.min(lo + size + size - 1, n - 1);
                merge(nums, copy, lo, hi, mid);
            }
        }
    }
}
```

### 桶排序

#### 561. Array Partition I
```java
/**
 * 题意：给一个无序数组，range是-10000 到 10000，求，如果把数组里面的数 两两分组
 * 然后每组里面取最小的数来代表这个组，然后使所有组的综合最大，那么这个最大值是多少
 * */
public class ArrayPartitionI561 {
    /** time O(n) space O(n) 方法：桶排序
     * 思路：如果每组取最小的数来代表这个组，那么逻辑就是 尽量让那个稍大的数，尽量小，
     * 所以，也就是排序好之后，两两一组，然后把单数index的数相加求和
     * 优化：但这样排序需要O(nlogn)，如果用桶排序，可以用O(n)时间和空间来优化，tradeoff
     * 具体就是开一个2w的数组，然后做个桶排，依次把基数位加到sum里
     * */
    public int arrayPairSum(int[] nums) {
        if (nums == null || nums.length == 0 || nums.length % 2 == 1) {
            return 0;
        }
        int[] tmp = new int[20001];
        for (int num : nums) {
            ++tmp[num + 10000];
        }
        int sum = 0;
        boolean odd = true;
        for (int i = 0; i < tmp.length; ++i) {
            while (tmp[i]-- > 0) {
                if (odd) {
                    sum += (i - 10000);
                    odd = false;
                } else {
                    odd = true;
                }
            }
        }
        return sum;
    }
}

```
### diy快排

#### 75. Sort Colors
```java
/**
 * 题意：给一个无序数组，里面有0，1，2三种元素，代表3种颜色
 * 要求，排序，
 * */
public class SortColors0075 {
    /** time O(n) space O(1) 方法：3-way partition
     * 思路：首先想到的就是桶排序，需要O(K)空间
     * 优化：3-way partition
     * 3-way partition的方法就是，用3个指针，left，right 和 i
     * 如果nums[i] 比1小，那么i和left swap，反之 i 和 right swap
     * 如果nums[i] 等于1，那么只++i，
     * 注意：nums[i] 比1小 的时候。swap的时候，要同时increment i和left
     * 但比1大的时候，只decrement right， 原因就是换到左边的数，位置是确定了的，
     * 但从右边换过来的数，还有可能是比1大的数，所以，要继续检查
     * 这样，可以用left保证，左边都比一小，right保证右边都比1大，
     * */
    public void sortColors(int[] nums) {
        if (nums == null || nums.length < 2) {
            return;
        }
        int left = 0;
        int right = nums.length - 1;
        int i = 0;
        while (i <= right) {
            if (nums[i] < 1) {
                swap(nums, i++, left++);
            } else if (nums[i] > 1) {
                swap(nums, i, right--);
            } else {
                ++i;
            }
        }
    }
    private void swap (int[] nums, int a, int b) {
        int tem = nums[a];
        nums[a] = nums[b];
        nums[b] = tem;
    }
}

```
#### 215. Kth Largest Element in an Array
```java
/**
 * 无序数组中，找到第k个大的数*/
public class KthLargestElementinanArray0215 {
    /** time O(n) space O(n) 方法：优先队列 或者 diy快排
     * 思路：1. 维护一个k大小的最小堆，扫一遍数组 ，如果堆得size大于k了，就poll出最小的
     * 这样，当堆里只有k个元素的时候，堆顶的就是第k大的
     * 复杂度的话，时间是 nlogk，空间是k
     * 优化：如果可以改input数组的话，可以自己实现一个快排，这样，可以接近去O（n）时间
     * 当结束一整次swap 之后，如果 len - l == k 说明，l现在就在从右边数第k个index上
     * 直接返回，否则，就按照逻辑。循环左边或右边
     * */
    public int findKthLargest(int[] nums, int k) {
        if (nums == null) {
            return 0;
        }
        int left = 0;
        int right = nums.length - 1;
        while (left < right) {
            int l = left;
            int r = right;
            int pivot = nums[l];
            while (l < r) {
                while (l < r && nums[r] >= pivot) {
                    --r;
                }
                swap(nums, r, l);
                while (l < r && nums[l] <= pivot) {
                    ++l;
                }
                swap(nums, l, r);
            }
            if (nums.length - l == k) {
                return nums[l];
            } else if (nums.length - l > k) {
                left = l + 1;
            } else {
                right = r - 1;
            }
        }
        return nums[left];
    }
    private void swap (int[] nums, int a, int b) {
        int tem = nums[a];
        nums[a] = nums[b];
        nums[b] = tem;
    }
}

```