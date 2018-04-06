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

#### 148. Sort List
```java
/**
 * 题意：nlogn时间排序链表
 * */
public class SortList0148 {
    /** time O(nlogn) space O(1) 方法：merge sort
     * 思路：先递归拆分，然后合并，拆分的时候，用快慢指针找mid，然后mid.next = null做分割
     * 然后分别递归左右两边，递归出口就是，只剩一个节点的情况，就直接返回该节点，
     * 拆分之后，调用merge函数，合并就可以了
     * 优化：
     * */
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode fast = head;
        ListNode slow = head;
        ListNode mid = null;
        while (fast != null && fast.next != null) {
            mid = slow;
            slow = slow.next;
            fast = fast.next.next;
        }
        mid.next = null;
        ListNode first = sortList(head);
        ListNode second = sortList(mid);
        return merge(first, second);
    }
    private ListNode merge(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(0);
        ListNode tail = head;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                tail.next = l1;
                l1 = l1.next;
            } else {
                tail.next = l2;
                l2 = l2.next;
            }
            tail = tail.next;
        }
        if (l1 != null) {
            tail.next = l1;
        }
        if (l2 != null) {
            tail.next = l2;
        }
        return head.next;
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
### Wiggle Sort

#### 280. Wiggle Sort

```java

/**
 * 题意：给一个无序无重数组，要求in place 做一个摇摆排序，也就是小大小大小 这样的顺序
 * */
public class WiggleSort0280 {
    /** time O(n) space O(1) 方法：
     * 思路：方法就是，假设之前都已经排好序，如果当前元素处于错误位置，那么就把当前元素和前一个元素swap，
     * 比如当前位置本来应该大于前一个，但现在却小于前一个了， 那么对调之后，当前2个元素肯定是排好序的
     * 而且也一定不会影响之前的元素，因为，前一个本来就应该小于更前面一个，现在换过去个比之前更小的元素
     * 所以也一定小于更前面的元素，反之同理
     * 优化：
     * */
    public void wiggleSort(int[] nums) {
        if (nums == null || nums.length < 2) {
            return;
        }
        for (int i = 1; i < nums.length; ++i) {
            if (i % 2 == 1 && nums[i] < nums[i - 1]) {
                    swap(nums, i, i - 1);
            }
            if (i % 2 == 0 && nums[i] > nums[i - 1]) {
                swap(nums, i, i - 1);
            }
        }
    }
    private void swap(int[] nums, int a, int b) {
        int tmp = nums[a];
        nums[a] = nums[b];
        nums[b] = tmp;
    }
}
```

#### 324. Wiggle Sort II
```java
/**
 * 题意：follow up： 只能小于 或 大于，不能小于等于， 大于等于
 * */
public class WiggleSortII0324 {
    /** 时间复杂度 O（n）, space O(1)
思路 : 先用diy quick sort 找到中位数，然后，中位数左边小于中位数，右边大于中位数
然后，依次吧后面的大数放到前面的山峰（偶数index），需要注意的时候，如果数组长度是偶数
那么要从倒数第二个开始，如果是奇数，从倒数第一个开始，逻辑就是把后面的山谷换到前面当山峰
扫一遍之后，理应结束了，但数组里面有重复元素，所以很有可能有些山峰和山谷是同一个高度
想一下，可以知道，会导致这种情况的数字只有可能是中位数，因为如果重复数字在前面或者后面，
重复数字肯定就错开了，they must be staggered 比如 1423999   1929394
所以，这时候，我们只需要把扫2遍数组，把山峰和山谷上的中位数往两边换
     如何保证换走后 新位置的正确性呢，
     因为左边山峰的元素是从后面换过来的，所以肯定比中位数大，
     而，左边山谷的元素本来就是比中位数小的元素，所以换过去只会更小，所以也成立
     唯一需要注意的地方就是，要先检查，要换过的位置上，是否已经是中位数了，如果是，就先跳到下一位
j = (n % 2 == 0) ? (n - 2) : (n - 1) 的意思就是，如果n是偶数，最后一位是山峰
*/
    public void wiggleSort(int[] nums) {
        if (nums == null || nums.length < 2) {
            return;
        }
        int n = nums.length;
        int mid = findMedian(nums, n / 2);
        //右山谷换到左山峰
        for (int i = 1, j = (n % 2 == 0) ? n - 2: n - 1; i < j; i += 2, j -= 2) {
            swap(nums, i, j);
        }
        //把右边山谷的中位数，换到左边山谷
        for (int i = 0, j = (n % 2 == 0) ? n - 2: n - 1; i < j; j -= 2) {
            while (i < j && nums[i] == mid) {
                i += 2;
            }
            if (i < j && nums[j] == mid) {
                swap(nums, i, j);
                i += 2;
            }
        }
        //把左边山峰的中位数，换到右边山峰
        for (int i = 1, j = (n % 2 == 1) ? n - 2: n - 1; i < j; i += 2) {
            while (i < j && nums[j] == mid) {
                j -= 2;
            }
            if (i < j && nums[i] == mid) {
                swap(nums, i, j);
                j -= 2;
            }
        }
    }
    private int findMedian(int[] nums, int k) {
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
                swap(nums, l, r);
                while (l < r && nums[l] <= pivot) {
                    ++l;
                }
                swap(nums, l, r);
            }
            if (l == k) {
                return nums[l];
            } else if (l < k) {
                left = l + 1;
            } else {
                right = r - 1;
            }
        }
        return nums[left];
    }
    private void swap(int[] nums, int a, int b) {
        int tmp = nums[a];
        nums[a] = nums[b];
        nums[b] = tmp;
    }
}

```

#### 376. Wiggle Subsequence
```java
/**
 * 题意：如果一个序列的相邻数字之差在正数和负数之间交替变换，则称此序列为一个“摆动序列”。第一个差值（如果存在的话）正负均可。
 * 少于两个元素的序列也被认为是摆动序列。例如，[1,7,4,9,2,5] 是一个摆动序列，因为差值(6,-3,5,-7,3)正负交替。
 * 反例， [1,4,7,2,5] 以及 [1,7,4,5,5] 不是摆动序列，第一个是因为前两个差值连续为正，第二个是因为最后一个差值是0。
 * 给定一个整数序列，返回其最长摆动子序列的长度。一个子序列可以通过从原始序列中删除一定数目的（也可以为0）元素得到。
 * */
public class WiggleSubsequence0376 {
    /** time O(n) space O(1) 方法：贪心
     * 思路：因为第一个数可以是峰也可以是谷，所以，需要枚举2种情况，用一个布尔变量来标记上一个元素是峰还是谷
     * 如果上一个元素是低谷，且当前元素比它大，那么count加1，布尔标记成峰，反之亦然，
     * 这里有个点需要注意，如果出现了不符合条件的情况，count自然不加，布尔也不变，当峰或谷的值应该继续变，
     * 意思就是，如果上一个是峰，当前这个本来应该是要比上一个小的，但现在是比上一个大了，那么布尔不变，(上一个仍然是峰）
     * 但峰值我们应该选当前这个，而不是上一个，因为当前这个更大，就有更多机会比后面的数大，反之亦然
     * 所以，i可以一直和 i-1 比。
     * 优化：
     * */
    public int wiggleMaxLength(int[] nums) {
        if (nums == null || nums.length < 1) {
            return 0;
        }
        if (nums.length == 1) {
            return 1;
        }
        return Math.max(getMax(nums, true), getMax(nums, false));
    }
    private int getMax(int[] nums, boolean isValley) {
        int max = 1;
        for (int i = 1; i < nums.length; ++i) {
            if (isValley && nums[i] > nums[i - 1]) {
                isValley = false;
                ++max;
            } else if (!isValley && nums[i] < nums[i - 1]) {
                isValley = true;
                ++max;
            }
        }
        return max;
    }
}

```