---
title: 二叉树
date: 2018-04-02 02:08:58
tags: [Tree]
categories: [Algorithm]
---

### 先序

#### 257. Binary Tree Paths
```java
public class BinaryTreePaths0257 {
    /** time O(2^h) space O() 方法：
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

#### 101. Symmetric Tree

```java
public class SymmetricTree0101 {
    /** time O(n) space O() 方法：队列
     * 思路：迭代版的稍微有一点点麻烦比递归的，首先初始要把root.left 和root.right都放进去 
     * 然后同样检查他们是否一起是null，如果是，不同于递归版，这里要continue，而不是直接返回true
     * 反之，如果不一样，直接返回false，然后依次把left.left, right.right, left.right, right.left
     * 放入队列继续检查，这里还有一点需要注意，队列的实现要用LinkedList，而不能用ArrayDeque，因为要加null
     * 优化：
     * */
    public boolean isSymmetric(TreeNode root) {
        //return root == null || isSymmetric(root.left, root.right);
        return isSymeetricIterative(root);
    }
    private boolean isSymmetric(TreeNode a, TreeNode b) {
        if (a == null && b == null) {
            return true;
        }
        if (a == null || b == null || a.val != b.val) {
            return false;
        }
        return isSymmetric(a.left, b.right) && isSymmetric(a.right, b.left);
    }
    private boolean isSymeetricIterative(TreeNode root) {
        if (root == null) {
            return true;
        }
        Deque<TreeNode> queue = new LinkedList<>();
        queue.offer(root.left);
        queue.offer(root.right);
        while (!queue.isEmpty()) {
            TreeNode left = queue.poll();
            TreeNode right = queue.poll();
            if (left == null && right == null) {
                continue;
            }
            if (left == null || right == null || left.val != right.val) {
                return false;
            }
            queue.offer(left.left);
            queue.offer(right.right);
            queue.offer(left.right);
            queue.offer(right.left);
        }
        return true;
    }
}
```
#### 110. Balanced Binary Tree
```java

/**
 * 题意：平衡术的定义： 一颗二叉树的任何节点的高度差不大于1
 * a binary tree in which the depth of the two subtrees of every node
 * never differ by more than 1.
 * */
public class BalancedBinaryTree0110 {
    /** time O(n) space O(1) 方法：递归
     * 思路：分别算左右子树的高度，如果任何一边不平衡，或者高度差大于1， 那么返回不平衡，
     * 否则，返回高度， 所以，递归函数 需要既return 高度，又 return 是否平衡
     * 优化：
     * */
    public boolean isBalanced(TreeNode root) {
        Tuple result =isBalancedHelper(root);
        return result.isBalanced;
    }
    private Tuple isBalancedHelper(TreeNode root) {
        if (root == null) {
            return new Tuple(0, true);
        }
        if (root.left == null && root.right == null) {
            return new Tuple(1, true);
        }
        Tuple left = isBalancedHelper(root.left);
        Tuple right = isBalancedHelper(root.right);
        if (!left.isBalanced || !right.isBalanced || Math.abs(left.height - right.height) > 1) {
            return new Tuple(-1, false);
        }
        return new Tuple(1 + Math.max(left.height, right.height), true);
    }
    class Tuple {
        int height;
        boolean isBalanced;
        Tuple(int height, boolean isBalanced) {
            this.height = height;
            this.isBalanced = isBalanced;
        }
    }
    private int isBalancedHelper1(TreeNode root) {
        if (root == null) {
            return 0;
        }
        if (root.left == null && root.right == null) {
            return 1;
        }
        int left = isBalancedHelper1(root.left);
        int right = isBalancedHelper1(root.right);
        if (left == -1 || right == -1 || Math.abs(left - right) > 1) {
            return -1;
        }
        return 1 + Math.max(left, right);
    }
}

```
#### 104. Maximum Depth of Binary Tree
```java
/**
 * 题意：求二叉树的最大深度
 * */
public class MaximumDepthofBinaryTree0104 {
    /** time O(n) space O(h) 方法：递归
     * 思路：dfs, 如果是null就返回0，如果是叶子就返回1，
     * 然后 当前 root的高度 就是 递归（左子树） 和 递归（右子树）更大的高度 加1
     * 优化：
     * */
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        if (root.left == null && root.right == null) {
            return 1;
        }
        return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
    }
}
```

### 杂题
#### 222. Count Complete Tree Nodes
```java

/**
 * 题意：给一个完全树，最后一排不一定满的树， heap树，求一共有多少个节点
 * */
public class CountCompleteTreeNodes0222 {
    /** time O(h * h) space O(h) 方法：一定二分
     * 思路：第一种方法，查看最左边高度和 最右边高度是否一样，如果一样直接返回1<<高度 -1
     * 如果不一样，就分别递归查看左右子树
     * 思路2，查看左右节点的，most Left，（左右子树的起点）这样，如果一样，说明左边一定是完美树
     * 如果不一样，右边一定是完美数，这种方法比第一种好在 每次可以 剪枝一边
     * 优化：
     * */
    public int countNodes(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int totalNodes = 1;
        int leftSide = getHeight(root.left);
        int rightSide = getHeight(root.right);
        if (leftSide == rightSide) {
            totalNodes += (1 << leftSide) - 1 + countNodes(root.right);
        } else {
            totalNodes += (1 << rightSide) - 1 + countNodes(root.left);
        }
        return totalNodes;
    }
    private int getHeight(TreeNode node) {
        int height = 0;
        while (node != null) {
            node = node.left;
            ++height;
        }
        return height;
    }
}

```
#### 255. Verify Preorder Sequence in Binary Search Tree
```java
/**
 * 题意：验证一个BST的先序顺序 是否合法
 * */
public class VerifyPreorderSequenceinBinarySearchTree0255 {
    /** time O() space O() 方法：
     * 通用思路：
         10
        /  \
       5   12
      / \
     2   6
     前序遍历的时候，只要是降序的时候，节点都是root的左边，当出现第一个升序的节点，这个节点肯定是
     刚好比这个节点小一点的节点（root）的右孩子，而这时候，后面的节点肯定都不能小于root，
     所以，这个root节点也就变成了之后节点的下限（min），所以，问题就变成了如何找到升序节点的父节点

     方法一： 用栈，额外使用O(h)的空间， 如果当前节点比之前节点小，压入栈
     当比之前节点大的时候，就到了某个root的右节点，
     这时候，从栈顶开始找第一个比这个大节点，刚小一点的节点，就是root
     而之后的所有节点，也就都不能小于这个root了。然后循环之前的步骤

     * 优化：其实都不需要保存 上一个节点，每次直接和 栈顶比就可以了，
     * 因为每次都会把当前节点压入栈，所以，当前的栈顶，就是上一次的节点
     *
     那么如果希望优化空间复杂度，就当然是从 栈着手，如果不用栈，我们可以用什么途径找到比当前节点稍小一点的值？
     暴力的话，就循环遍历之前所有额节点。但需要O(n)复杂度，那么整体复杂度也就变成了n方
     具体做法就是 ，用一个for循环从i-1开始遍历，从比root小的节点里面 找到一个最大的。

     方法二：如果既想不用额外空间，又想保持O(n)的时间复杂度，那么也可以用更改数组的方式来做
     思路就是把遍历过的sub array 模拟 成栈用，用一个stackTop 变量来记录栈顶，初始-1 表示栈为空，
     while 当前值大于栈顶的时候，pop栈顶，也就是--stackTop，结束是，栈顶变量在比当前值大的index上
     这时候，把当前值push栈顶，也就是 preorder[++stackTop] = 当前值，这样就把当前值放到了index的后面
     
     * */
    public boolean verifyPreorder(int[] preorder) {
        if (preorder == null || preorder.length < 3) {
            return true;
        }
        Deque<Integer> stack = new ArrayDeque<>();
        int lastNode = preorder[0];
        Integer min = null;
        stack.push(lastNode);
        for (int i = 1; i < preorder.length; ++i) {
            if (min != null && preorder[i] < min) {
                return false;
            }
            if (preorder[i] < lastNode) {
                stack.push(preorder[i]);
                lastNode = preorder[i];
            } else {
                while (!stack.isEmpty() && stack.peek() < preorder[i]) {
                    min = stack.pop();
                }
                stack.push(preorder[i]);
            }
        }
        return true;
    }
    public boolean verifyPreorder1(int[] preorder) {
        if (preorder == null || preorder.length < 3) {
            return true;
        }
        int stackTop = 0;
        Integer min = null;
        for (int i = 1; i < preorder.length; ++i) {
            if (min != null && preorder[i] < min) {
                return false;
            }
            if (preorder[i] < preorder[stackTop]) {
                ++stackTop;
            } else {
                while (stackTop >= 0 && preorder[stackTop] < preorder[i]) {
                    min = preorder[stackTop--];
                }
                preorder[++stackTop] = preorder[i];
            }
        }
        return true;
    }
}
```