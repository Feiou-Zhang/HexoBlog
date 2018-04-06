---
title: 路径问题
date: 2018-04-05 22:49:55
tags: [Path]
categories: [Algorithm]
---

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