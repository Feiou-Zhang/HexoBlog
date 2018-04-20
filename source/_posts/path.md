---
title: 路径问题
date: 2018-04-05 22:49:55
tags: [Path]
categories: [Algorithm]
---

#### 403. Frog Jump
```java

/**
 * 题意：一只青蛙过河。河流分成x个单元，每一个单元可能会有石头。青蛙可以跳到石头上，但是不能跳入水中。
 给定一列石头的位置（以单元数计），顺序递增，判断青蛙是否可以跳到最后一块石头上。
 初始时，青蛙站在第一块石头上并且假设只能向前跳1个单元。
 如果青蛙上一次跳过k个单元，那么它下一次只能跳 k - 1, k, 或者 k + 1 个单元。注意青蛙只能向前跳。
 */
public class FrogJump0403 {
    /** time O(n^2) space O(n^2) 方法：dp
     * 暴力： 用dfs，每次 分别dfs k - 1, k, 或者 k + 1
     * 错误思路： dfs + 记忆化 map里面如果保存当前单位是否可以成功到达终点的话，会有个问题，
     * 因为同一个单位，可能上一步是跳3步过来的，也可能是跳4步过来的，如果盲目记忆，就会出错，
     * 如果记当前单位和上次跳的步数，空间成本有太高。
     * dp思路：用map key是每个单位，value用一个set 记录所有 到达这个单位 用的步数
     * 这样，已知当前单位，和上次用的步数，自然可以得到下次能到达的单位，以及来到这个单位用的步数
     * 最后查看最后一步的set是否为空就可以了
     * 注意： 初始化的时候，要 加一个 map.get(0).add(0); 否则，map一直是空的
     * 优化：
     * */
    public boolean canCross(int[] stones) {
        if (stones == null || stones.length == 0) {
            return false;
        }
        Map<Integer, Set<Integer>> map = new HashMap<>();
        for (int stone : stones) {
            map.put(stone, new HashSet<>());
        }
        map.get(0).add(0);
        for (int current : stones) {
            for (int lastSteps : map.get(current)) {
                for (int nextSteps = lastSteps - 1; nextSteps <= lastSteps + 1; ++nextSteps) {
                    if (nextSteps > 0 && map.containsKey(current + nextSteps)) {
                        map.get(current + nextSteps).add(nextSteps);
                    }
                }
            }
        }
        return !map.get(stones[stones.length - 1]).isEmpty();
    }
}

```
#### 62. Unique Paths
```java
/**
 * 题意：给一个二维矩阵，m x n，求从左上起点，到右下终点一共有多少种不同的走法
 * */
public class UniquePaths0062 {
    /** time O(mn) space O(n) 方法：dp
     * 思路：对于某个格子，只能是从上面或左边来，所以，当前格子的path数，
     * 就等于从左边来的path数 加上从上面来的path数 之和，由于重复计算，
     * 我们可以用一个一维数组记录一下之前path数的结果，
     * 优化：
     * */
    public int uniquePaths(int m, int n) {
        int[] paths = new int[n + 1];
        paths[1] =  1;
        for (int i = 1; i <= m; ++i) {
            for (int j = 2; j <= n; ++j) {
                paths[j] += paths[j - 1];
            }
        }
        return paths[n];
    }
}

```
#### 63. Unique Paths II
```java
/**
 * 题意：给一个二维矩阵，m x n，求从左上起点，到右下终点一共有多少种不同的走法
 * 路径中0代表可以走 ，1代表障碍
 * */
public class UniquePathsII0063 {
    /** time O(mn) space O(n) 方法：dp
     * 思路：对于某个格子，只能是从上面或左边来，所以，当前格子的path数，
     * 就等于从左边来的path数 加上从上面来的path数 之和，由于重复计算，
     * 我们可以用一个一维数组记录一下之前path数的结果，
     * 当在原数组中遇到1的时候，把cache数组里面的对应位置改为0，
     * 注意，j的初始不能和第一问一样初始为2，因为第一列有可能有障碍
     * 优化：
     * */
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        if (obstacleGrid[0][0] == 1 || obstacleGrid[m - 1][n - 1] == 1) {
            return 0;
        }
        int[] paths = new int[n + 1];
        paths[1] =  1;
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (obstacleGrid[i - 1][j - 1] == 1) {
                    paths[j] = 0;
                } else {
                    paths[j] += paths[j - 1];
                }
            }
        }
        return paths[n];
    }
}
```
#### 64. Minimum Path Sum
```java

/**
 * 题意：给一个二维矩阵，m x n， 每个格子有一个非负数，求从左上起点，到右下终点
 * 最小的路径和
 * */
public class MinimumPathSum0064 {
    /** time O(mn) space O(m) 方法：dp
     * 思路：当前格子的最小和，肯定是当前数字加上，上面来的路径和，和 左边来的路径和 二者更小的一个
     * 注意，第一排 和 第一列 要单独算。
     * 优化：
     * */
    public int minPathSum(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int[] minSum = new int[n];
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (i == 0 && j == 0) {
                    minSum[j] = grid[i][j];
                } else if (i == 0) {
                    minSum[j] = grid[i][j] + minSum[j - 1];
                } else if (j == 0) {
                    minSum[j] = grid[i][j] + minSum[j];
                } else {
                    minSum[j] = grid[i][j] + Math.min(minSum[j - 1], minSum[j]);
                }
            }
        }
        return minSum[n - 1];
    }
}

```
#### 174. Dungeon Game
```java
/**
 * 题意：地牢游戏，骑士从左上角 去右下角 救公主
 * 每个房间 可能让骑士掉血或涨血，请问，骑士开始至少需要多少血才能保证可以救到工公主
 * */
public class DungeonGame0174 {
    /** time O(mn) space O(m) 方法：dp
     * 思路：求最少需要的血，意思就是到达最后一个房间的时候，血量需要是1
     * 那么进入最后一个房间之前，如果最后一个房间是非负数，那么最少就需要1，
     * 如果最后一个房间是负数，那么血量就至少需要 1 减去这个负数
     * 其余房间同理
     * 优化：
     * */
    public int calculateMinimumHP(int[][] dungeon) {
        int row = dungeon.length;
        int col = dungeon[0].length;
        int[] min = new int[col];
        for (int i = row - 1; i >= 0; --i) {
            for (int j = col - 1; j >= 0; --j) {
                if (i == row - 1 && j == col - 1) {
                    min[j] = dungeon[i][j] >= 0 ? 1 : 1 - dungeon[i][j];
                } else if (i == row - 1) {
                    min[j] = dungeon[i][j] >= min[j + 1] ? 1 : min[j + 1] - dungeon[i][j];
                } else if (j == col - 1) {
                    min[j] = dungeon[i][j] >= min[j] ? 1 : min[j] - dungeon[i][j];
                } else {
                    int smaller = Math.min(min[j + 1], min[j]);
                    min[j] = dungeon[i][j] >= smaller ? 1 : smaller - dungeon[i][j];
                }
            }
        }
        return min[0];
    }
}
```
#### 329. Longest Increasing Path in a Matrix
```java
/**
 * 给一个二维矩阵，求最长的升序序列的长度
 nums = [
 [3,4,5],
 [3,2,6],
 [2,2,1]
 ]
 Return 4
 The longest increasing path is [3, 4, 5, 6]. Moving diagonally is not allowed.
 * */
public class LongestIncreasingPathinaMatrix0329 {
    /** time O(mm) space O(mn)
     * 思路，记忆化dfs，对于每个点，向4个方向dfs，然后中途可以记录每个点可以到达的最远的长度
     * 记忆化搜索的话，需要额外的 O(n^2)的空间，但应该节省很多时间， trade off
     * 1. 递归函数需要的几个元素，原数组，longest数组，行列index，上一次的值
     * 2. 递归函数里，先检查是否越界，和当前数字是否小于等于上一次的值，如果不qualified，return 0
     * 3. 如果符合条件，再看，当前cell是否已经cache过最大值了，如果有，直接返回
     * 4. dfs 4个方向，初始为1，因为，自己本身肯定是一个长度，然后 加上 递归返回结果
     * 5. 取4个方向中最大的结果，cache成当前数字的longest，并返回
     * */
    public int longestIncreasingPath(int[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0] == null || matrix[0].length == 0) {
            return 0;
        }
        int longestPath = 1;
        int[][] longest = new int[matrix.length][matrix[0].length];
        for (int i = 0; i < matrix.length; ++i) {
            for (int j = 0; j < matrix[0].length; ++j) {
                longestPath = Math.max(longestPath, helper(matrix, longest, i, j, Integer.MIN_VALUE));
            }
        }
        return longestPath;
    }
    private int helper(int[][] matrix, int[][] longest, int row, int col, int lastNumber) {
        if (row < 0 || row == matrix.length || col < 0 || col == matrix[0].length || matrix[row][col] <= lastNumber) {
            return 0;
        }
        if (longest[row][col] > 0) {
            return longest[row][col];
        }
        int up =  1 + helper(matrix, longest, row - 1, col, matrix[row][col]);
        int down = 1 + helper(matrix, longest, row + 1, col, matrix[row][col]);
        int left = 1 + helper(matrix, longest, row, col - 1, matrix[row][col]);
        int right = 1 + helper(matrix, longest, row, col + 1, matrix[row][col]);
        longest[row][col] = maxOfFour(up, down, left, right);
        return longest[row][col];
    }
    private int maxOfFour(int a, int b, int c, int d) {
        return Math.max(a, Math.max(b, Math.max(c, d)));
    }
}
```
#### 120. Triangle
```java
/**
 * 题意：数字三角形，找到一条路径从顶点到底部 的 和最小
 * */
public class Triangle0120 {
    /** time O(n^2) space O(n) 方法：dp
     * 思路：有点像bubble sort，自下而上的把小的数都 加 到上面
     * 原理上其实本身是可以做一个自上而下的dfs，但由于有大量的重复计算，可以用cache 局部结果
     * 所以就可以先把所有局部结果算出来，也就自然而然的可以用dp来解决
     * 优化：
     * */
    public int minimumTotal(List<List<Integer>> triangle) {
        int n = triangle.size();
        int[] sum = new int[n];
        for (int i = 0; i < n; ++i) {
            sum[i] = triangle.get(n - 1).get(i);
        }
        for (int i = n - 2; i >= 0; --i) {
            int size = triangle.get(i).size();
            for (int j = 0; j < size; ++j) {
                sum[j] = triangle.get(i).get(j) + Math.min(sum[j], sum[j + 1]);
            }
        }
        return sum[0];
    }
}

```

### Stack

#### 71. Simplify Path
```java
/**
 * 题意：简化linux的文件路径
 * 比如"/a/./b/../../c/e/" 简化成"/c/e"
 * */
public class SimplifyPath0071 {
    /** time O(n) space O(n) 方法：deque
     * 思路：原则上 一个点不表示当前层。两个点表示上一次
     * 所以，遇到一个点，不用做任何事，遇到两个点，需要删除上一层，走到上上层
     * 注意很多 边界条件，比如 多个//，大于2个点。还有最后得到一个空的StringBuilder的情况
     * 具体可以用stack来做，遇到文件夹则压入栈，如果单个。或者多个点。或者空，(//)则跳过
     * 遇到两个点，则pop栈顶
     * 优化：
     * */
    public String simplifyPath(String path) {
        if (path == null || path.length() == 0) {
            return "/";
        }
        Deque<String> deque = new ArrayDeque<>();
        String[] paths = path.split("/+");
        for (String p : paths) {
            if (p.equals(".") || p.length() == 0) {
                continue;
            }
            if (p.equals("..")) {
                if (!deque.isEmpty()) {
                    deque.pop();
                }
            } else {
                deque.push(p);
            }
        }
        StringBuilder res = new StringBuilder();
        while (!deque.isEmpty()) {
            res.append("/").append(deque.pollLast());
        }
        return res.length() == 0 ? "/" : res.toString();
    }
}
```
### Binary Tree

#### 257. Binary Tree Paths
```java

public class BinaryTreePaths0257 {
    /** time O(2^h) space O() 方法：先序遍历
     * 思路：每次进入递归函数中，先加入root值，然后记录一下此时stringbuilder的长度
     * 再检查当前节点是否是叶子节点，如果是 加入到结果
     * 反之，看左右孩子，哪个不为null，就递归哪边，然后回溯
     * 优化：
     *
     * 可以检查，当叶子节点进入到递归函数的时候，是否能得到正确答案
     * */
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        helper(res, root, new StringBuilder());
        return res;
    }
    private void helper(List<String> res, TreeNode root, StringBuilder curr) {
        curr.append("->").append(root.val);
        int len = curr.length();
        if (root.left == null && root.right == null) {
            res.add(curr.substring(2));
        } else {
            if (root.left != null) {
                helper(res, root.left, curr);
                curr.setLength(len);
            }
            if (root.right != null) {
                helper(res, root.right, curr);
                curr.setLength(len);
            }
        }
    }
}
```
#### 112. Path Sum
```java
/**
 * 题意：给一颗二叉树，和一个目标和，判断是否有路径从root到leaf 的和等于这个目标和
 * */
public class PathSum0112 {
    /** time O(n) space O(h) 方法：递归
     * 思路：dfs，如果没有负数的话 ，可以剪枝
     * 优化：
     * */
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null) {
            return false;
        }
        if (root.left == null && root.right == null) {
            return sum == root.val;
        }
        return hasPathSum(root.left, sum - root.val) || hasPathSum(root.right, sum - root.val);
    }
}

```
#### 666. Path Sum IV
```java
/**
     * 题意：用数组的方式表示一颗二叉树，求所有路径和
     * Input: [113, 215, 221]
     * 第一个字母代表层数，第二个字母代表位置，第三是值
     Output: 12
     Explanation:
     The tree that the list represents is:
        3
       / \
      5   1

     The path sum is (3 + 5) + (3 + 1) = 12.
 *
 * */
public class PathSumIV0666 {
    /** time O(n) space O(n) 方法：
     可以用一个map 保存所有的节点，key是位置(层数和具体位置)，value是值
     然后递归函数中，传入父节点的key 以及当前的总和
     根据父节点的key，可以算得 左右孩子的key，如果左右孩子都不在map里面，直接返回当前和
     否则，就递归不为空的 左或右孩子， 左右返回 左孩子 和 右孩子的 总和
     这里只需要返回左孩子 和 右孩子 是因为，每个孩子都已经加过了root的值
     */
    public int pathSum(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : nums) {
            int key = num / 10;
            int value = num % 10;
            map.put(key, value);
        }
        return getSum(map, nums[0] % 10, nums[0] / 10);
    }
    private int getSum(Map<Integer, Integer> map, int sum, int Parentkey) {
        int leftKey = ((Parentkey / 10) + 1) * 10 + (Parentkey % 10) * 2 - 1;
        int rightKey = leftKey + 1;
        if (!map.containsKey(leftKey) && !map.containsKey(rightKey)) {
            return sum;
        }
        int leftValue = map.containsKey(leftKey) ? getSum(map, sum + map.get(leftKey), leftKey): 0;
        int rightValue = map.containsKey(rightKey) ? getSum(map, sum + map.get(rightKey), rightKey): 0;
        return leftValue + rightValue;
    }
}

```
#### 124. Binary Tree Maximum Path Sum
```java
/**
 * 题意：给一个二叉树，找到最大和的路径，这条路径不需要经过root，也不需要从leaf出发或结束
 * 但至少需要1个节点
 * */
public class BinaryTreeMaximumPathSum0124 {
    /** time O(n) space O(h) 方法：后序遍历
     * 思路：后序遍历，如果左右节点 返回负数，就返回0，意思就是不选择后面的负数和，
     * 每一轮访问到root节点的时候，更新一下 全局最大值，然后返回 左右节点更大的一个 和
     * 优化：
     * */
    public int maxPathSum(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int[] max = new int[] {Integer.MIN_VALUE};
        helper(root, max);
        return max[0];
    }
    private int helper(TreeNode root, int[] max) {
        int left = Math.max(0, root.left == null ? 0 : helper(root.left, max));
        int right = Math.max(0, root.right == null ? 0 : helper(root.right, max));
        max[0] = Math.max(max[0], root.val + left + right);
        return Math.max(root.val + left, root.val + right);
    }
}
```
#### 129. Sum Root to Leaf Numbers
```java
/**
 * 给一颗二叉树，求所有root到leaf的路线和

    1
   / \
  2   3
 The root-to-leaf path 1->2 represents the number 12.
 The root-to-leaf path 1->3 represents the number 13.

 Return the sum = 12 + 13 = 25.
 */
public class SumRoottoLeafNumbers0129 {
    /** time O(n) space O(h) 方法：递归
     * 思路：可以用bfs 层序遍历，自定义个一个node节点 ，里面放之前的和，
     * 然后下一层就把poll出来的和 乘以 10 再加上当前节点的值，再 offer 进队列
     * 递归的思路就是，把当前的sum 传到下一层，如果当前节点是叶子节点，那么把sum加入到 结果
     * 优化：
     * */
    public int sumNumbers(TreeNode root) {
        return root == null ? 0 : sumNumbersHelper(root, root.val);
    }
    private int sumNumbersHelper(TreeNode root, int sum) {
        if (root.left == null && root.right == null) {
            return sum;
        }
        int total = 0;
        if (root.left != null) {
            total += sumNumbersHelper(root.left, sum * 10 + root.left.val);
        }
        if (root.right != null) {
            total += sumNumbersHelper(root.right, sum * 10 + root.right.val);
        }
        return total;
    }
    public int sumNumbers1(TreeNode root) {
        if (root == null) {
            return 0;
        }
        Queue<TreeNode> nodes = new LinkedList<>();
        Queue<Integer> total = new LinkedList<>();
        nodes.offer(root);
        total.offer(root.val);
        int sum = 0;
        while (!nodes.isEmpty()) {
            TreeNode head = nodes.poll();
            int currentTotal = total.poll();
            if (head.left == null && head.right == null) {
                sum += currentTotal;
            }
            if (head.left != null) {
                nodes.offer(head.left);
                total.offer(currentTotal * 10 + head.left.val);
            }
            if (head.right != null) {
                nodes.offer(head.right);
                total.offer(currentTotal * 10 + head.right.val);
            }
        }
        return sum;
    }
}
```