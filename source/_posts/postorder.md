---
title: 先序，后序，层序遍历的应用
date: 2018-02-14 17:08:16
tags: [Tree]
categories: [Algorithm]
---

### Post Order

#### 145. Binary Tree Postorder Traversal

```java
public class BinaryTreePostorderTraversal0145 {
    /** time O(n) space O(1) 方法：迭代 + 栈
     * 思路：左，右，root，左子树就和中序一样，循环入栈，
     * 遍历右孩子的时候，首先 head 等于 peek栈顶，
     * 因为这时候，右子树有可能还遍历过，也有可能还没遍历过
     * 如果右孩子为空，或者右孩子是lastvisit的话，就可以访问root，pop栈顶
     * 更新lastvisit为root, 同时把root标为null，进入下个循环。
     * 否则，要先遍历右子树
     * 优化：morrios 后序遍历？有点难。。。
     * */
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        TreeNode head = root;
        TreeNode lastVisit = null;
        Deque<TreeNode> stack = new ArrayDeque<>();

        while (head != null || !stack.isEmpty()) {
            while (head != null) {
                stack.push(head);
                head = head.left;
            }
            head = stack.peek();
            if (head.right == null || head.right == lastVisit) {
                res.add(stack.pop().val);
                lastVisit = head;
                head = null;
            } else {
                head = head.right;
            }
        }
        return res;
    }
}
```

#### 236. Lowest Common Ancestor of a Binary Tree

```java
/**题目大意： 在二叉树找到2个节点的最近的父节点
 *         _______3______
         /              \
     ___5__          ___1__
    /      \        /      \
   6       2       0       8
          /  \
         7   4
 */
public class LowestCommonAncestorofaBinaryTree0236 {
    /** time O(n) space On) 方法：类后序遍历
     * 思路：递归左子树，如果过程中，有任意节点等于p或者q，那么那一支就返回p或q，否则肯定会返回null，右子树同理
     * 左右子树都完事后，查看root，如果如果的左右子树都有值，说明，这个root就是lca，否则，左孩子有值就返回左孩子
     * 右孩子有值就返回右孩子，都没值就返回null，
     * 优化：可以用后序迭代做？但应该不算什么优化，都还是需要n的栈空间
     * */
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) {
            return root;
        }
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left != null && right != null) {
            return root;
        }
        return left != null ? left : right;
    }
}
```

#### 333. Largest BST Subtree

```java
/**
 * 题意：在一颗二叉树中找到其中最大的一颗BST，然后返回bst的总节点数
 *     10
      / \
     5  15
    / \   \
   1   8   7
 比如这颗树，最大的bst就是 1，5，8
 * Follow up：如果用O（N）做
 * */
public class LargestBSTSubtree0333 {
    /** time O(n) space O(n) 方法：后序遍历 + 递归
     * 思路：后序遍历，递归函数应该然后返回一个范围 和 当前bst的size
     * 对于任何节点，它都应该大于左孩子的upper bound，同时小于右孩子的lower bound
     * 满足这种情况下，当前节点的 bst的尺寸就应该是 左孩子的尺寸 + 右孩子的尺寸 + 1
     * 这里需要注意的是，如果定义一个节点的 上界和下界，这里有3种情况，
     * 1.如果node是空，那么就返回下界为intmax，上界为intmin就可以，尺寸为0
     * 2.如果node是leaf，那么上下界都是自己，尺寸为1
     * 3.如果node只有左孩子或只有右孩子的情况就比较麻烦，
     * 因为某一边肯定就会return intmax, intmin上来，这样一来，比如root没有右孩子
     * 那么右孩子return intmax, intmin，这时候，root要小于右孩子的下界，也就是小于 intmax
     * 没什么问题，但这时候，如果用intmin当做root的上界，之后就会导致错误了。
     * 所以，定义上下界的时候，要防止这种情况。所以，下界要取node值和左孩子下界中小的那个
     * 然后用一个全局变量，每次更新bst的最大尺寸。
     * 如果当前root已经不在范围内了，返回一个逆上下界就可以了
     * 优化：
     * */
    public int largestBSTSubtree(TreeNode root) {
        int[] max = new int[] {1};
        postOrderValidate(max, root);
        return root == null ? 0 : max[0];
    }
    private int[] postOrderValidate(int[] max, TreeNode node) {
        if (node == null) {
            return new int[] {Integer.MAX_VALUE, Integer.MIN_VALUE, 0};
        }
        if (node.left == null && node.right == null) {
            return new int[] {node.val, node.val, 1};
        }
        int[] leftEdge = postOrderValidate(max, node.left);
        int[] rightEdge = postOrderValidate(max, node.right);
        if (node.val > leftEdge[1] && node.val < rightEdge[0]) {
            int size = leftEdge[2] + rightEdge[2] + 1;
            int lower = Math.min(node.val, leftEdge[0]);
            int upper = Math.max(node.val, rightEdge[1]);
            max[0] = Math.max(max[0], size);
            return new int[] {lower, upper, size};
        }
        return new int[] {Integer.MIN_VALUE, Integer.MAX_VALUE, 0};
    }
}

```

### 变异后序

#### 538. Convert BST to Greater Tree

```java
/**
 * 题目大意：给定一棵二叉查找树（BST），将其转化为“Greater Tree”，
 * 原始BST中的每一个节点都替换为不小于其本身的各节点的和。*/
public class ConvertBSTtoGreaterTree0538 {
    /** time O(n) space O(n) 方法：
     * 思路：可以用有一个中序遍历，然后从后向前做一个sum 树
     * 这样可以用O(n) time 和 space 可以解决
     * 优化：可以用一个逆序遍历 右中左遍历？
     * 注意：sum要用一个全局变量，
     * */
    public TreeNode convertBST(TreeNode root) {
        convertBSTHelper(root, new int[]{0});
        return root;
    }
    private void convertBSTHelper(TreeNode node, int[] sum) {
        if (node == null) {
            return;
        }
        convertBSTHelper(node.right, sum);
        node.val += sum[0];
        sum[0] = node.val;
        convertBSTHelper(node.left, sum);
    }
}

```

#### 199. Binary Tree Right Side View
```java
/**
 * 题意：打印 从右边看到的一个二叉树的所有节点，意思同层的节点只显示最右面的
 * For example:
 Given the following binary tree,
      1            <---
    /   \
   2     3         <---
   \      \
    5     4       <---
 You should return [1, 3, 4].
 * */
public class BinaryTreeRightSideView0199 {
    /** time O(n) space O(n) 方法：diy后序遍历，root, right left
     * 思路：递归函数中 加一个level 参数，同level只打印最右边节点
     * 优化：非递归可以用一个层序遍历，每行只打印最后一个节点
     * */
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        rightSideViewHelper(root, 0, res);
        return res;
    }
    private void rightSideViewHelper(TreeNode node, int level, List<Integer> res) {
        if (node == null) {
            return;
        }
        if (level == res.size()) {
            res.add(node.val);
        }
        rightSideViewHelper(node.right, level + 1, res);
        rightSideViewHelper(node.left, level + 1, res);
    }
}
```

### Level Order

#### 314. Binary Tree Vertical Order Traversal

```java
/**
 * 题意 ：vertical的遍历二叉树，要求就是节点要从左到右打印
 * */
public class BinaryTreeVerticalOrderTraversal0314 {
    /** time O(n) space O(n) 方法：层序遍历 + hashmap
     * 思路： 层序遍历，以root开始，每次像左走的时候，index，offset -1， 向右走的时候 + 1
     * 这样每个节点都有个vertical的index，相同index的节点，加到map里面就行
     * 这样，有同样index的节点就可以放在一起，需要用个全局遍历记录一下最左边的index
     * 注意：这题DFS是不能做的，因为深度优先的话，有些左边的节点就有几率优先右边的节点加入list
     * 如果bfs做的话，就需要新建一个class，把index和node同时记录起来
     * 需要注意的是，之后都要用新的class，不要用TreeNode
     */
    public List<List<Integer>> verticalOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        Map<Integer, List<Integer>> map = new HashMap<>();
        Deque<TreeNodeWithIndex> queue = new ArrayDeque<>();
        queue.offer(new TreeNodeWithIndex(root, 0));
        int mostLeft = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            while (--size >= 0) {
                TreeNodeWithIndex head = queue.poll();
                if (!map.containsKey(head.index)) {
                    map.put(head.index, new ArrayList<>());
                }
                mostLeft = Math.min(mostLeft, head.index);
                map.get(head.index).add(head.node.val);
                if (head.node.left != null) {
                    queue.offer(new TreeNodeWithIndex(head.node.left, head.index - 1));
                }
                if (head.node.right != null) {
                    queue.offer(new TreeNodeWithIndex(head.node.right, head.index + 1));
                }
            }
        }
        for (; map.containsKey(mostLeft); ++mostLeft) {
            res.add(map.get(mostLeft));
        }
        return res;
    }
    class TreeNodeWithIndex {
        TreeNode node;
        int index;
        TreeNodeWithIndex(TreeNode node, int index) {
            this.node = node;
            this.index = index;
        }
    }
}

```

#### 116. Populating Next Right Pointers in Each Node

```java

/**
 题意：给一颗二叉树的每个节点加一个next指针，指针指向同层的右边的相邻节点
 For example,
 Given the following binary tree,
      1
    /  \
   2    3
  / \    \
 4   5    7
 After calling your function, the tree should look like:
      1 -> NULL
    /  \
   2 -> 3 -> NULL
  / \    \
 4-> 5 -> 7 -> NULL*/
public class PopulatingNextRightPointersinEachNode0117 {
    /** time O(n) space O(1) 方法：层序遍历
     * 思路：用一个pre节点和一个dummy节点，连接同层所有节点
     * 步骤就是，pre.next 指向 root的左孩子，和 右孩子，然后root也平移到下一个同层的root
     * 优化：
     * */
    public class TreeLinkNode {
        int val;
        TreeLinkNode left, right, next;
        TreeLinkNode(int x) { val = x; }
    }
    public void connect(TreeLinkNode root) {
        if (root == null) {
            return;
        }
        TreeLinkNode head = root;
        while (head != null) {
            TreeLinkNode dummy = new TreeLinkNode(0);
            TreeLinkNode pre = dummy;
            while (head != null) {
                if (head.left != null) {
                    pre.next = head.left;
                    pre = pre.next;
                }
                if (head.right != null) {
                    pre.next = head.right;
                    pre = pre.next;
                }
                head = head.next;
            }
            head = dummy.next;
        }
    }
}

```