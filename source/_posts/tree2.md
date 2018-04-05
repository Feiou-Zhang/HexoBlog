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