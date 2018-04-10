---
title: 回溯
date: 2018-02-21 15:42:56
tags: [Backtracking]
categories: [Algorithm]
---

[回溯解题技巧总结](https://feiou-zhang.github.io/2018/03/04/backtrackingtips/)
### Subset 系列

#### 78. Subsets
```java
public class Subsets0078 {
   /**time O(n*2^n) space O(n)   O(构造解的复杂度 * 解的个数)
    * 这种问题一般都是类似决策树的问题，对于每个选项都有选和不选2种
    subset中元素的个数是2^n,而每个元素的长度都应该是n量级的，所以整体复杂度应该是n*2^n
    思路就是用dfs搜索，用一个for循环，每次递归只加当前index之后的数字，
    每次进入递归前，先把上一次完成的subset加入到结果里。
    **不需要退出条件，而且更不能用 index == nums.length 作为退出条件，
    因为res.add 是加上一层递归得到的subset
    */
   public List<List<Integer>> subsets(int[] nums) {
       List<List<Integer>> res = new ArrayList<>();
       if (nums == null || nums.length == 0) {
           return res;
       }
       subsetsHelper(nums, res, new ArrayList<>(), 0);
       return res;
   }
   private void subsetsHelper(int[] nums, List<List<Integer>> res, List<Integer> curr, int idx) {
       res.add(new ArrayList<>(curr));
       for (int i = idx; i < nums.length; ++i) {
           curr.add(nums[i]);
           subsetsHelper(nums, res, curr, i + 1);
           curr.remove(curr.size() - 1);
       }
   }
}
```
#### 90. Subsets II

```java
/**
 * 题意：数组元素有重，但结果要无重
 * */
public class SubsetsII0090 {
    /** time O(n*2^n) space O(2^n) 方法：
     * 思路：首先要保证无重，还是要先排序数组，这样，重复的数字会相邻
     * 接下来，我们需要知道的时候，这些重复的结果是如何产生的
     * 第一次是在递归的时候，加入到结果中的，这时候，i 等于 index，
     * 第二次是在for循环的时候产生的，i++了
     * 知道了这个信息，去重就简单了， 那么就让i 不等于index的时候，如果i 和 i-1的元素一样，就跳过
     * 优化：
     * */
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        if (nums == null || nums.length == 0) {
            return res;
        }
        Arrays.sort(nums);
        subsetsWithDupHelper(nums, res, new ArrayList<>(), 0);
        return res;
    }
    private void subsetsWithDupHelper(int[] nums, List<List<Integer>> res, List<Integer> curr, int idx) {
        res.add(new ArrayList<>(curr));
        for (int i = idx; i < nums.length; ++i) {
            //also can do i != idx
            if (i > idx && nums[i] == nums[i - 1]) {
                continue;
            }
            curr.add(nums[i]);
            subsetsWithDupHelper(nums, res, curr, i + 1);
            curr.remove(curr.size() - 1);
        }
    }
}
```
#### 131. Palindrome Partitioning
```java
/**
 * 题意：给一个字符串，求所有回文components的组合
 * 比如"aab" ，返回[a,a,b] [aa,b]
 * */
public class PalindromePartitioning0291 {
    /** time O(2^n) space O(n) 方法：回溯
     * 思路：首先可以用一个helper 函数来判断当前范围的字符串是否是回文
     * 然后递归函数中，参数传入left边界，然后函数中，循环递增right边界
     * 如果当前【left,right)区间里是回文，那么把当前回文加入到list中，
     * 继续递归，这里注意，下一层的递归。left要从当前的right开始
     * 优化：小优化，不用substring，而是不断用left 和right 来标记区间
     * 加入list的时候，要用一个构建String的方法，char[], int offset, int count
     * */
    public List<List<String>> partition(String s) {
        List<List<String>> res = new ArrayList<>();
        partitionHelper(res, new ArrayList<>(), s.toCharArray(), 0);
        return res;
    }
    private void partitionHelper(List<List<String>> res, List<String> curr, char[] c, int left) {
        if (left == c.length) {
            res.add(new ArrayList<>(curr));
            return;
        }
        for (int right = left + 1; right <= c.length; ++right) {
            if (isPal(c, left, right - 1)) {
                curr.add(new String(c, left, right - left));
                partitionHelper(res, curr, c, right);
                curr.remove(curr.size() - 1);
            }
        }
    }
    private boolean isPal(char[] c, int left, int right) {
        while (left < right) {
            if (c[left++] !=c[right--]) {
                return false;
            }
        }
        return true;
    }
}

```
#### 320. Generalized Abbreviation
```java
/**
 * 题意：求一个单词的所有缩写的组合
 * 比如：string = "abc", 所有组合：["3","2c","1b1","1bc","a2","a1c","ab1","abc"]
 * */
public class GeneralizedAbbreviation0320 {
    /** time O(2^n) space O(n) 方法： dfs + 回溯
     * 思路：对于每个index，都有2种情况，要不就是直接用当前的字母，
     * 要么就abbreviate, 也就是用数字1来代替，这种情况，如果直接把1加入到stringbuilder里面的话
     * 很可能会出现11这种情况，所以，我们不能直接加1，而是利用一个count的变量，用++count来记录
     * 所以，在不abbreviate的时候，首先要把之前的count加入到stringbuilder里
     * 这题的重点是，abbreviate的时候如何回溯，因为毕竟当时没有加入任何东西到stringbuilder里面
     * 所以，这个题要在加入结果后，也回溯，这样就可以把最后没走到情况二的分支也回溯
     * 优化：
     * */
    public List<String> generateAbbreviations(String word) {
        List<String> res = new ArrayList<>();
        gaHelper(res, word, new StringBuilder(), 0, 0);
        return res;
    }
    private void gaHelper(List<String> res, String word, StringBuilder curr, int index, int count) {
        int len = curr.length();
        if (index == word.length()) {
            res.add(curr.append(count > 0 ? count : "").toString());
        } else {
            gaHelper(res, word, curr, index + 1, count + 1);
            gaHelper(res, word, curr.append(count > 0 ? count : "").append(word.charAt(index)), index + 1, 0);
        }
        curr.setLength(len);
    }
}
```
### Permutation 系列

#### 46. Permutations

```java
/**
 * 题意：给一个数组，物重复元素，返回这个数组元素所有的permutation的组合
 * */
public class Permutations0046 {
    /** time O(n!) n * T(n-1) space O(n) 方法：回溯
     * 思路：从原数组依次把元素加入到新数组，然后递归下一层，知道新数组的长度
     * 和原数组一样，加入到结果，然后每次递归之后，都回溯上个状态
     * 注意，还需要用一个Boolean数组来记录，哪个数字已经用过了，
     * 否则递归到下一层，无法知道之前层 都用了什么字母
     * 注意：这题是不用传index到下一层的，因为每一层都要用每一个数字
     * 优化：
     * */
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length == 0) {
            return res;
        }
        boolean[] used = new boolean[nums.length];
        permuteHelper(used, res, new ArrayList<>(), nums);
        return res;
    }
    private void permuteHelper(boolean[] used, List<List<Integer>> res,
                               List<Integer> curr, int[] nums) {
        if (curr.size() == nums.length) {
            res.add(new ArrayList<>(curr));
            return;
        }
        for (int i = 0; i < nums.length; ++i) {
            if (used[i]) {
                continue;
            }
            used[i] = true;
            curr.add(nums[i]);
            permuteHelper(used, res, curr, nums);
            curr.remove(curr.size() - 1);
            used[i] = false;
        }
    }
}
```
#### 47. Permutations II
```java
/**
 * 题意：给一个数组，有重复元素，返回这个数组元素所有的unique的permutation的组合
 * */
public class PermutationsII0047 {
    /** time O(n!) space O(n) 方法：回溯
     * 思路：涉及到去重，首先应该先排序数组，然后再递归中，过滤掉重复的结果
     * 特别重要：在去重的时候，如果单纯的 used[i] || i > 0 && nums[i] != nums[i - 1]
     * 会返回空，画个图就很明显看出来，这是因为，不管是递归进去，还是for循环进去，只要
     * i 和 i-1一样，没有什么可以区分，这段代码会直接跳过，所有，curr的size永远满足不了
     * 所以，需要多加一个条件，当上一个重复的元素used或者没used得时候，再skip
     * 优化：
     * */
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length == 0) {
            return res;
        }
        Arrays.sort(nums);
        boolean[] used = new boolean[nums.length];
        permuteUniqueHelper(res, new ArrayList<>(), nums, used);
        return res;
    }
    private void permuteUniqueHelper(List<List<Integer>> res, List<Integer> curr,
                                      int[] nums, boolean[] used) {
        if (curr.size() == nums.length) {
            res.add(new ArrayList<>(curr));
            return;
        }
        for (int i = 0; i < nums.length; ++i) {
            if (used[i] || i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
                continue;
            }
            curr.add(nums[i]);
            used[i] = true;
            permuteUniqueHelper(res, curr, nums, used);
            used[i] = false;
            curr.remove(curr.size() - 1);
        }
    }
}
/**very important**
 if we simply do i > 0 && nums[i] == nums[i - 1], we will not get any result,
 f there are duplicate element exist, then [i] == [i - 1] will always return true,
 that the curr.size() will never reach to the size of the array.
 idea :when the duplicate result happens, the duplicate elements should always 
 meet the case that one is used and the other one is not.
 based on this idea. we can just block one of those two situation, 
 then we will only get one of them.
 **/
```

#### 31. Next Permutation

```java
/**
 * 题意：给一个数组，求下一个permutation
 * */
public class NextPermutation0031 {
    /** time O(n) space O(1) 注意重复数字的情况！
     思路：从倒数第二个开始扫描，找到第一个小于前面一个数的数，我们叫他small
     如果找不到这个small，说明整个数组是倒序的，那么就reverse整个数组就好了
     
     然后再从倒数第一个开始扫描，找到第一个比这个数大的数，我们叫他large
     然后swap这两个数，然后，reverse后面的部分，

     需要注意的是：做题还是得多距离，各种情况要考虑周到
     比如，这个题，不要想当然的用 <，要想想，如果相等的情况
     */
    public void nextPermutation(int[] nums) {
        if (nums == null || nums.length < 2) {
            return;
        }
        int small = nums.length - 2;
        while (small >= 0 && nums[small] >= nums[small + 1]) {
            --small;
        }
        if (small < 0) {
            reverse(nums, 0, nums.length - 1);
            return;
        }
        int large = nums.length - 1;
        while (nums[large] <= nums[small]){
            --large;
        }
        swap(nums, small, large);
        reverse(nums, small + 1, nums.length - 1);
    }
    private void reverse(int[] nums, int start, int end) {
        while (start < end) {
            swap(nums, start++, end--);
        }
    }
    private void swap(int[] nums, int a, int b) {
        int tem = nums[a];
        nums[a] = nums[b];
        nums[b] = tem;
    }
}

```
#### 51. Previous Permutation LintCode
```java
public class PreviousPermutation0051LintCode {
    /** time O(n) space O(1) 方法：
     * 思路：从倒数第二个元素开始，找到第一个非降序的元素，我们叫他large
     * 如果没有找到这么一个元素，那么reverse整个list
     * 然后从倒数第一个元素开始，找到第一个比large小的元素，我们叫他small
     * 然后swap small 和large 然后reverse large 后面的部分
     * 优化：
     * */
    public List<Integer> previousPermuation(List<Integer> nums) {
        List<Integer> res = new ArrayList<>(nums);
        if (nums.isEmpty()) {
            return res;
        }
        int large = res.size() - 2;
        while (large >= 0 && res.get(large) <= res.get(large + 1)) {
            --large;
        }
        if (large < 0) {
            reverse(res, 0, res.size() - 1);
            return res;
        }
        int small = res.size() - 1;
        while (small > large && res.get(small) >= res.get(large)) {
            --small;
        }
        swap(res, small, large);
        reverse(res, large + 1, res.size() - 1);
        return res;
    }
    private void reverse(List<Integer> nums, int start, int end) {
        while (start < end) {
            swap(nums, start++, end--);
        }
    }
    private void swap(List<Integer> nums, int a, int b) {
        int tem = nums.get(a);
        nums.set(a, nums.get(b));
        nums.set(b, tem);
    }
}
```

#### 60. Permutation Sequence

```java
/**
 * 题意：求第k个permutation的结果
 * */
public class PermutationSequence0060 {
    /**time O(n) space O(n) 方法：找规律
     思路：找规律，首先肯定不用把所有的permutation都做出来然后return第k个，
     首先要知道，n个数字的permutation，以第一个数字开始的permutation有多少个
     举个例子，或者仔细想想就应该可以找到，一共有（n-1）！这么多，
     那么第二个数字，其实也就是一样的道理，所以可以先用一个数组记录一下n-1到1的阶乘
     另外需要注意的是，这(n-1)!里面的选项，
     对于n个数字，如果前面位置用了的数字，后面是不能再用了，所以要从list里面删除
     这里用LinkedList会稍微好一点，
     最后一个循环。可以初始i 等于n-1，这样能稍微简单点，
     正着扫描的话，i 要等于n-i-1，更容易出错，i >= 0, 因为sb最后的长度要加n次
     每次循环，要跟新k,k %= factorial[i];
     */
    public String getPermutation(int n, int k) {
        if (n < 0 || k < 0) {
            return "";
        }
        if (n == 1) {
            return "1";
        }
        List<Integer> candidate = new LinkedList<>();
        int[] factorial = new int[n + 1];
        factorial[0] = 1;
        for (int i = 1; i <= n; ++i) {
            factorial[i] = factorial[i - 1] * i;
            candidate.add(i);
        }
        --k;
        StringBuilder res = new StringBuilder();
        for (int i = n - 1; i >= 0; --i) {
            int index = k / factorial[i];
            res.append(candidate.remove(index));
            k %= factorial[i];
        }
        return res.toString();
    }
}

```
#### 784. Letter Case Permutation
```java
/**
 * 题意：给一个字符串，内含字母和数字，求字母大小写不同的所有permutation
 * 例如：Input: S = "a1b2"
 Output: ["a1b2", "a1B2", "A1b2", "A1B2"]
 * */
public class LetterCasePermutation0784 {
    /** time O(n!) space O(n) 时间复杂度是 n*T(n-1),所以每次n的规模减一，所以是n的阶乘
     * 方法：回溯
     * 思路：递归函数里先循环skipindex为数字的字符。然后，dfs加回溯，造一个新的字符串
     * 当新的字符串长度等于原来字符串长度的时候，加到list。并返回
     * 优化：
     * */
    public List<String> letterCasePermutation(String S) {
        List<String> res = new ArrayList<>();
        if (S == null) {
            return res;
        }
        lcpHelper(res, S, new StringBuilder(), 0);
        return res;
    }
    private void lcpHelper(List<String> res, String S, StringBuilder curr, int index) {
        while (index < S.length() && Character.isDigit(S.charAt(index))) {
            curr.append(S.charAt(index++));
        }
        if (curr.length() == S.length()) {
            res.add(curr.toString());
            return;
        }
        int size = curr.length();
        lcpHelper(res, S, curr.append(Character.toLowerCase(S.charAt(index))), index + 1);
        curr.setLength(size);
        lcpHelper(res, S, curr.append(Character.toUpperCase(S.charAt(index))), index + 1);
        curr.setLength(size);
    }
}
```
#### 267. Palindrome Permutation II
```java
/**
 * 题意：给一个字符串，求用字符串里的所有字母可以组成的所有回文
 * */
public class PalindromePermutationII0267 {
    /** time O(n * n!) space O(n) permutation一共有n！个，构建单个解需要O(n)时间
     * 空间就是 n的栈空间。
     * 思路：方法：dfs+回溯，首先判断s的奇偶，和是否最终可以组成回文，顺便建map 统计词频
     * 然后如果奇数的话，先把single char放到中间，然后从中间像两边依次添加成对元素
     * 当任何一边到头，就添加到结果，然后回溯
     * 优化：
     * */
    public List<String> generatePalindromes(String s) {
        List<String> res = new ArrayList<>();
        if (s == null || s.length() == 0 ) {
            return res;
        }
        int n = s.length();
        int[] map = new int[256];
        Character[] singleChar = new Character[] {null};
        char[] chars = new char[n];
        if (!isValid(map, s, singleChar)) {
            return res;
        }
        int left = n / 2 - 1;
        int right = n / 2;
        if (n % 2 == 1) {
            right = n / 2 + 1;
            chars[n / 2] = singleChar[0];
        }
        genertaPaliHelper(res, map, chars, left, right);
        return res;
    }
    private void genertaPaliHelper(List<String> res, int[] map, char[] chars, int left, int right) {
        if (right == chars.length) {
            res.add(new String(chars));
            return;
        }
        for (int i = 0; i < map.length; ++i) {
            if (map[i] > 1) {
                map[i] -= 2;
                chars[left] = chars[right] = (char)i;
                genertaPaliHelper(res, map, chars, left - 1, right + 1);
                map[i] += 2;
            }
        }
    }
    private boolean isValid(int[] map, String s, Character[] singleChar) {
        for (char c : s.toCharArray()) {
            ++map[c];
        }
        for (int i = 0; i < map.length; ++i) {
            if (map[i] % 2 == 1) {
                if (singleChar[0] != null) {
                    return false;
                }
                singleChar[0] = (char)i;
            }
        }
        return true;
    }
}
```
### Combinations

#### 39. Combination Sum
```java

/**
 * 题意：给一个无序无重数组只包含正数，和一个target，如果同一个元素可以用0或无限次
 * 那么求所有能得到和为target 的排列组合
 * */
public class CombinationSum0039 {
    /** time O(2^n) space O(n) 方法：回溯
     * 思路：先排序，然后循环加入元素，如果当和大于target的时候，就退出，
     * 等于target的时候加入结果，然后也退出，因为这2种情况，后面的数肯定只会更大
     * 小于target的话，就继续dfs
     * 优化：
     * */
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> res = new ArrayList<>();
        if (candidates == null || candidates.length == 0) {
            return res;
        }
        Arrays.sort(candidates);
        cmHelper(res, new ArrayList<>(), candidates, target, 0, 0);
        return res;
    }
    private void cmHelper(List<List<Integer>> res, List<Integer> curr, int[] candidates, int target, int sum, int index) {
        if (sum == target) {
            res.add(new ArrayList<>(curr));
            return;
        }
        for (int i = index; i < candidates.length; ++i) {
            if (sum > target) {
                return;
            }
            curr.add(candidates[i]);
            cmHelper(res, curr, candidates, target, sum + candidates[i], i);
            curr.remove(curr.size() - 1);
        }
    }
}
```

#### 40. Combination Sum II
```java
/**
 * 题意：给一个无序可以重数组只包含正数，和一个target，如果同一个元素至多可以用1次
 * 那么求所有能得到和为target 的排列组合
 * */
public class CombinationSumII0040 {
    /** time O(2^n) space O(kn) k是解的个数，n是数组的元素个数 方法：回溯
     * 思路：还是先排序，然后递归时，传入下一个元素的index，
     * 而且数组里面的元素可能重复，但结果不能重复，所有，在递归函数的循环里面需要查重
     * 优化：
     * */
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        List<List<Integer>> res = new ArrayList<>();
        if (candidates == null || candidates.length == 0) {
            return res;
        }
        Arrays.sort(candidates);
        cm2Helper(res, new ArrayList<>(), candidates, target, 0 , 0);
        return res;
    }
    private void cm2Helper(List<List<Integer>> res, List<Integer> curr, int[] candidates,
                           int target, int sum, int index) {
        if (sum == target) {
            res.add(new ArrayList<>(curr));
            return;
        }
        for (int i = index; i < candidates.length; ++i) {
            if (sum + candidates[i] > target) {
                return;
            }
            if (i != index && candidates[i] == candidates[i - 1]) {
                continue;
            }
            curr.add(candidates[i]);
            cm2Helper(res, curr, candidates, target, sum + candidates[i], i + 1);
            curr.remove(curr.size() - 1);
        }
    }
}
```
#### 216. Combination Sum III
```java
/**
 * 题意：1到9中，选k个数，加起来等于n，每个数只能用一次，不能哟重复解
 * */
public class CombinationSumIII0216 {
    /** time O(2^9) space O(n) 方法：dfs + 递归
     * 思路：依次加入1到9，如果当前sum已经大于n了，就return，如果等于n，而且，size是k的情况，
     * 就加入答案，否则，继续递归选下一个数字
     * 优化：
     * */
    public List<List<Integer>> combinationSum3(int k, int n) {
        List<List<Integer>> res = new ArrayList<>();
        if (n < 1 || k < 1) {
            return res;
        }
        cm3Helper(k, n, 0, 1, res, new ArrayList<>());
        return res;
    }
    private void cm3Helper(int k, int n, int sum, int idx, List<List<Integer>> res, List<Integer> curr) {
        if (curr.size() == k && sum == n) {
            res.add(new ArrayList<>(curr));
            return;
        }
        if (sum >= n || curr.size() >= k) {
            return;
        }
        for (int i = idx; i <= 9; ++i) {
            curr.add(i);
            cm3Helper(k, n, sum + i, i + 1, res, curr);
            curr.remove(curr.size() - 1);
        }
    }
}
```
#### 77. Combinations
```java

/**
 * 题意：用1到n， n个数，来做k个数的全排列组合
 * */
public class Combinations0077 {
    /** time O(2^n) space O(n) 方法：dfs+回溯
     * 这个题，说是排列组合，但并不能出现4，1 这种组合，所以其实更像是subset问题
     * 思路：依次选取1到n，然后下一层只能选当前字母后面的元素
     * 优化：如果candidate里面元素，已经少于需要的元素了，可以直接返回，
     * 比如，1-4 选3个，当第一数是3的时候，其实，后面也就只有4一个可以选了，就没必要继续了
     * n - i + 1 < k - curr.size()
     * */
    public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> res = new ArrayList<>();
        combineHelper(n, k, res, new ArrayList<>(), 1);
        return res;
    }
    private void combineHelper(int n, int k, List<List<Integer>> res, List<Integer> curr, int idx) {
        if (curr.size() == k) {
            res.add(new ArrayList<>(curr));
            return;
        }
        for (int i = idx; i <= n; ++i) {
            if (n - i + 1 < k - curr.size()) {
                continue;
            }
            curr.add(i);
            combineHelper(n, k, res, curr, i + 1);
            curr.remove(curr.size() - 1);
        }
    }
}
```
#### 254. Factor Combinations
```java
/**
 * 题意：给一个数字，求这个数字所有的因子组合，当然是不重复的，
 * 比如，对于数字12，2*6 和 6*2 是一样的，
 * */
public class FactorCombinations0254 {
    /** time O(2^n) space O(n) 方法：回溯
     * 思路：首先，如果是要不重复的，那么循环要停止到sqrt(n)，以保证无重
     * 然后，每次得到因子的时候，直接加入2个因子，然后当前list里面的因子肯定已经是一个结果了
     * 直接先加入到result里面，然后先删掉后面大的因子，回溯，最后，删掉小的因子回溯
     *
     * 比如，n是12，从2开始遍历，2可以被整除，那么加入2 和 6，这时候，2，6 肯定是结果
     * 然后，删掉6，把6当n，继续递归，这时候，list里面有一个2，而6又可以分解成2和3，
     * 然后2和3 加入到list里面，加上以前的2， 也就是把2，2，3也加到result里面，
     * 这时候，删掉3，把3当成n继续递归，这时候，3/2 已经小于2了，这说明，即使2可以加入到list
     * 那么3/2也一定是一个重复的因子，所以，当start > n/start的时候，可以直接剪枝返回
     * */
    public List<List<Integer>> getFactors(int n) {
        List<List<Integer>> res = new ArrayList<>();
        getFactorsHelper(res, new ArrayList<>(), n, 2);
        return res;
    }
    private void getFactorsHelper(List<List<Integer>> res, List<Integer> curr, int n, int start) {
        for (int i = start; i <= Math.sqrt(n); ++i) {
            if (n % i == 0 && n / i >= i) {
                curr.add(i);
                curr.add(n / i);
                res.add(new ArrayList<>(curr));
                curr.remove(curr.size() - 1);
                getFactorsHelper(res, curr, n / i, i);
                curr.remove(curr.size() - 1);
            }
        }
    }
}
```

#### 17. Letter Combinations of a Phone Number
```java
/**
 * 题意：给一个纯数字字符串，每个数组都代表了手机键盘上的对应的几个字母，
 * 要求返回这几个数组能组成的所有字母组合
 * */
public class LetterCombinationsofaPhoneNumber0017 {
    /** time O(3^n) space O(n) 方法：回溯
     * 思路：先建数字和对应字母群的映射，每层选一个数组，然后递归到下一层，
     * 递归函数里，先找到当前数字对应的字母群，然后循环遍历字母群，循环加入到sb中
     * 如果sb的长度达到digits的长度，返回， 然后循环里面，每次递归结束都回溯
     * 优化：
     * */
    public List<String> letterCombinations(String digits) {
        List<String> res = new ArrayList<>();
        if (digits == null || digits.length() == 0) {
            return res;
        }
        String[] map = new String[] {"","","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
        letterCombinationsHelper(map, digits, new StringBuilder(), res, 0);
        return res;
    }
    private void letterCombinationsHelper(String[] map, String digits, StringBuilder curr,
                                          List<String> res, int i) {
        if (curr.length() == digits.length()) {
            res.add(curr.toString());
            return;
        }
        int size = curr.length();
        String letters = map[digits.charAt(i) - '0'];
        for (int j = 0; j < letters.length(); ++j) {
            letterCombinationsHelper(map, digits, curr.append(letters.charAt(j)), res, i + 1);
            curr.setLength(size);
        }
    }
}
```

#### 22. Generate Parentheses
```java
/**
 * 题意：给定n，要求返回所有可能 的 n组合法的括号，
 * */
public class GenerateParentheses0022 {
    /** time O(n!) space O(n) 卡特兰数的复杂度（2n)!/(n-1)!n! 约等于n！
     * 方法：回溯 加 剪枝  
     * 思路：限制加 开闭括号的情况，已达到剪枝 和 验证的作用
     * 当开闭括号数量相等的时候，或者 开括号数量不到n的时候 ，可以加开括号，
     * 当开括号数量达到n的时候，或者开括号数量不闭括号数量多的时候，可以加闭括号
     * 优化：
     * */
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        if (n < 1) {
            return res;
        }
        generateParenthesisHelper(res, new StringBuilder(), 0, 0, n);
        return res;
    }
    private void generateParenthesisHelper(List<String> res, StringBuilder curr, int open, int close, int n) {
        if (close == n) {
            res.add(curr.toString());
            return;
        }
        int size = curr.length();
        if (open == close || open != n) {
            generateParenthesisHelper(res, curr.append('('), open + 1, close, n);
            curr.setLength(size);
        }
        if (open == n || open > close) {
            generateParenthesisHelper(res, curr.append(')'), open, close + 1, n);
            curr.setLength(size);
        }
    }
}
```
#### 93. Restore IP Addresses
```java
/**
 * 题意：给一个字符串，看可以有多少种合法的ip排列组合
 * */
public class RestoreIPAddresses0093 {
    /** time O(3^n) space O(3^n) 方法：dfs + 回溯
     * 思路： 用dfs+递归，首先要判断合法条件，如果是一位，那么可以是任何数
     * 如果是两位三位，那么是不能以0开头的，而且，三位的情况下，是不能超过255的
     * 另外，还可以把string分成4个segment，每个segment其实都应该有个最少字符
     * 和最多字符，比如一开始，长度最少是4，最多也就只能是12，到了第二组，最少要是3，最多是9
     * */
    public List<String> restoreIpAddresses(String s) {
        List<String> res = new ArrayList<>();
        helper(res, new StringBuilder(), 0, s.toCharArray(), 4);
        return res;
    }
    private void helper(List<String> res, StringBuilder curr, int idx, char[] c, int segment) {
        if (idx == c.length && segment == 0) {
            res.add(curr.substring(1));
            return;
        }
        if (c.length - idx < segment || c.length - idx > 3 * segment) {
            return;
        }
        int len = curr.length();
        helper(res, curr.append('.').append(c[idx]), idx + 1, c, segment - 1);
        curr.setLength(len);
        if (c[idx] != '0' && idx + 1 < c.length) {
            helper(res, curr.append('.').append(c[idx]).append(c[idx + 1]), idx + 2, c, segment - 1);
            curr.setLength(len);
        }
        if (c[idx] != '0' && idx + 2 < c.length && new String(c, idx, 3).compareTo("256") < 0) {
            helper(res, curr.append('.').append(new String(c, idx, 3)), idx + 3, c, segment - 1);
            curr.setLength(len);
        }
    }
}
```
### 类似 图遍历

#### 79. Word Search
```java
/**
 * 题意：给一个2D board，里面装有single letter，求从任何一个字母开始走，上下左右
 * 不重复用字母的情况下，是否有一条path可以组成target word
 ['A','B','C','E'],
 ['S','F','C','S'],
 ['A','D','E','E']
 * */
public class WordSearch0079 {
    /** time O(n*4^n) space O(4^n) 方法：dfs+回溯
     思路：对于board里每一个和word首字母一样的元素开始4个方向的dfs，同时把遇见过的元素标记成visited
     主函数要遍历board里面每个字母，进行递归调用。
     递归函数先检查传入的 row和col 是否越界，然后查看当前cell 是否visited，是否和当前的word字符相等
     都满足的情况下，标记当前cell 为visited，然后**依次**递归4个方向，有任何一个方向返回true就结束
     如果都没有返回true，那么回溯 visited 为false，then返回主函数，再查看下一个字母
     * 优化：小优化 开始可以检查一下 word的长度是否大于整个board的长度
     * */
    public boolean exist(char[][] board, String word) {
        if (board == null || board.length == 0 || board[0] == null || board[0].length == 0) {
            return false;
        }
        if (word == null || word.length() == 0 || word.length() > board.length * board[0].length) {
            return false;
        }
        boolean[][] visited = new boolean[board.length][board[0].length];
        for (int i = 0; i < board.length; ++i) {
            for (int j = 0; j < board[0].length; ++j) {
                if (helper(board, word, visited, i, j, 0)) {
                    return true;
                }
            }
        }
        return false;
    }
    private boolean helper(char[][] board, String word, boolean[][] visited, int row, int col, int idx) {
        if (idx == word.length()) {
            return true;
        }
        if (row < 0 || row == board.length || col < 0 || col == board[0].length || visited[row][col] ||
                board[row][col] != word.charAt(idx)) {
            return false;
        }
        visited[row][col] = true;
        if (helper(board, word, visited, row + 1, col, idx   + 1)) {
            return true;
        }
        if (helper(board, word, visited, row, col + 1, idx   + 1)) {
            return true;
        }
        if (helper(board, word, visited, row - 1, col, idx   + 1)) {
            return true;
        }
        if (helper(board, word, visited, row, col - 1, idx   + 1)) {
            return true;
        }
        visited[row][col] = false;
        return false;
    }
}

```
### 记忆化，博弈类 (w/ dp)

#### 294. Flip Game II
```java
/**
 * 题意：给一字符串，里面只包含+和-，你和朋友玩游戏，依次去把任何连续++改成--，直到有一个人没有++可改就算输
 * 问，先走的人，是不是一定可以赢
 * */

public class FlipGameII0294 {
    /** time O(n!) space O(n!) 复杂度不确定 方法：回溯
     * 思路：递归看，走一步之后，对方是否没得走，如果对方没得走了，那么对方就输，换言之就是自己赢
     * 优化：记忆化搜索，递归中有大量重复计算，可以把结果放进map里，进入递归函数的时候，先去map找
     * 注意：加入结果到map的时候，总是要加入当前s 和 当前结果
     * */
    public boolean canWin(String s) {
        return s != null && s.length() >= 2 && canWinHelper(s, new HashMap<>());
    }
    private boolean canWinHelper(String s, Map<String, Boolean> map) {
        if (map.containsKey(s)) {
            return map.get(s);
        }
        char[] chars = s.toCharArray();
        for (int i = 0; i < chars.length - 1; ++i) {
            if (chars[i] == '+' && chars[i + 1] == '+') {
                chars[i] = chars[i + 1] = '-';
                String component = new String(chars);
                if (!canWinHelper(component, map)) {
                    map.put(s, true);
                    return true;
                }
                chars[i] = chars[i + 1] = '+';
            }
        }
        map.put(s, false);
        return false;
    }
}
```

```java
/**
 * 题意：2个人从一个pool中 选数字，选过的就不能再选，谁最后选的一个数超过target总数就算赢
 * 求self 玩家是否能赢
 * */
public class CanIWin0464 {
    /** time O(n*2^n) space O(2^n) 一共2^n个解方法：博弈类，记忆化搜索，看对方是否能赢，
     * 思路：首先看，有没有情况，是双方都不能赢的，然后枚举所有情况，如果当前局自己能赢
     * 或者对方会输，就返回true，如果循环结束都不能赢，那么就返回false，每次得到任何结果
     * 都保存到map里，那么map的key是什么呢？这里，需要想一下，只要某些数字被选过了，
     * 其实是谁选的并不重要，所有，可以把所有选项变成01的stream 作为key
     * 优化：由于max只有20位，那么其实可以用一个int的32bit来表示来优化空间
     * */
    public boolean canIWin(int max, int total) {
        int[] state = new int[max + 1];
        Map<String, Boolean> map = new HashMap<>();
        return isValid(max, total) && canWinHelper(map, state, 0, total);
    }
    private boolean canWinHelper(Map<String, Boolean> map, int[] state, int curr, int total) {
        String key = Arrays.toString(state);
        if (map.containsKey(key)) {
            return map.get(key);
        }
        for (int i = 1; i < state.length; ++i) {
            if (state[i] == 0) {
                state[i] = 1;
                if (curr + i >= total || !canWinHelper(map, state, curr + i, total)) {
                    map.put(key, true);
                    state[i] = 0;
                    return true;
                }
                state[i] = 0;
            }
        }
        map.put(key, false);
        return false;
    }
    private boolean isValid(int max, int desiredTotal) {
        int total = (1 + max) * max / 2;
        total = max % 2 == 1 ? total + max / 2 + 1 : total;
        return total >= desiredTotal;
    }
}
```
`优化`
```java
public class CanIWin0464 {
    public boolean canIWin(int max, int total) {
        return isValid(max, total) && canWinHelper(new HashMap<>(), 0, 0, max, total);
    }
    private boolean canWinHelper(Map<Integer, Boolean> map, int state, int curr, int max, int total) {
        if (map.containsKey(state)) {
            return map.get(state);
        }
        for (int i = 1; i <= max; ++i) {
            int mask = 1 << i;
            if ((mask & state) != mask) {
                if (curr + i >= total || !canWinHelper(map, state + mask, curr + i,  max, total)) {
                    map.put(state, true);
                    return true;
                }
            }
        }
        map.put(state, false);
        return false;
    }
    private boolean isValid(int max, int desiredTotal) {
        int total = (1 + max) * max / 2;
        total = max % 2 == 1 ? total + max / 2 + 1 : total;
        return total >= desiredTotal;
    }
}
```

#### 394. Coins in a Line (lintcode)
```java

/**
 * 题意：有一些硬币，你和小伙伴依次交替从pool里拿1或2个硬币，最后一轮拿币的获胜
 * 求先手可否获胜
 * */
public class CoinsinaLine0394 {
    /** time O(n) space O(n) 方法：dp
     * 思路：n等于1和2的情况肯定可以获胜，当n等于x的时候，x能获胜的情况，只有在 x-1或x-2不能获胜的情况
     * 优化：可以优化为O(1)空间
     * */
    public boolean firstWillWin(int n) {
        if (n < 1) {
            return false;
        }
        boolean take1 = true;
        boolean take2 = true;
        boolean res = true;
        for (int i = 3; i <= n; ++i) {
            res = !take1 || !take2;
            take2 = take1;
            take1 = res;
        }
        return res;
    }
    public boolean firstWillWin1(int n) {
        if (n < 1) {
            return false;
        }
        if (n < 3) {
            return true;
        }
        boolean[] canWin = new boolean[n + 1];
        canWin[1] = canWin[2] = true;
        for (int i = 3; i <= n; ++i) {
            canWin[i] = !canWin[i - 2] || !canWin[i - 1];
        }
        return canWin[n];
    }
}

```
#### 375. Guess Number Higher or Lower II
```java
/**
 * 题意：猜错的话，要pay错的时候那个数字等价的钱，求最后
 * */
public class GuessNumberHigherorLowerII0375 {
    /** time O(n^3) space O(n^2) 方法：dp
     * 思路：举例子，如果1，2，3，保证找到答案的最少花费就是2，
     * 那么1，2，3，4，5呢，首先肯定还是要选3，选3之后，左边就选1， 右边就选4,那么最少花费就是3 + 4，
     * 那么4，5呢，i等于4的时候，dp[4][5] = 4, i等于5的时候，dp[4][5] 等于5，显然，4就够用了
     * 所以，对于dp[i][j] 要取更小的花费
     * 知道这些信息，我们就可以想到可以用dp来解决这个问题
     * 优化：每次可以i可以选区间中间的数开始选
     * */
    public int getMoneyAmount(int n) {
        return getMinCost(1, n, new int[n + 1][n + 1]);
    }
    private int getMinCost(int start, int end, int[][]dp) {
        if (start >= end) {
            return 0;
        }
        if (dp[start][end] != 0) {
            return dp[start][end];
        }
        dp[start][end] = Integer.MAX_VALUE;
        for (int i = start + (end - start) / 2; i <= end; ++i) {
            int leftMax = getMinCost(start, i - 1, dp);
            int rightMax = getMinCost(i + 1, end, dp);
            dp[start][end] = Math.min(dp[start][end], i + Math.max(leftMax, rightMax));
        }
        return dp[start][end];
    }
}

```