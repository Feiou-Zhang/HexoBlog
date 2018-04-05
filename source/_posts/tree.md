---
title: 二叉搜索树，中序遍历，二分
date: 2018-02-12 22:19:57
tags: [Tree]
categories: [Algorithm]
---

![bst](BST.png)

遇到BST，通常要想到2种思路，1. 中序遍历，2. 二分查找，
而且顺序应该是先想用中序去解决，然后想可不可以用二分做优化


### 二分查找

#### 235. Lowest Common Ancestor of a Binary Search Tree
```java
/**
 * 题目大意： 找到2个节点的最近的父节点
 * */
public class LowestCommonAncestorofaBinarySearchTree0235 {
    /** time O(logn) space O(1) 方法：二分
     * 思路：对于没个root，如果p q在 root的2边，那么root肯定就是lca
     * 反之，如果p，q在同侧，那么就递归该恻
     * 优化：
     * */
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        //首先应该，p q 是否为空 或者 是否相等
        if (root == null || root == p || root == q) {
            return root;
        }
        if (p.val < root.val && q.val > root.val || p.val > root.val && q.val < root.val) {
            return root;
        }
        return p.val < root.val ? lowestCommonAncestor(root.left, p, q) :
                                  lowestCommonAncestor(root.right, p, q);
    }
}

```
#### 270. Closest Binary Search Tree Value

```java
/**
题意：找到离target最近的一个节点在一个 BST，target为double
 */
public class ClosestBinarySearchTreeValue0270 {
    /** time O(logn) space O(1) 方法：
     * 思路：中序遍历，每次更新当前节点和target的绝对差，知道这个绝对差开始增加的时候停止，时间O(n)
     * 优化：二分，对于每个root，如果绝对差 小于之前的最好结果，那么更新最好结果
     * 如果target小于左节点，那么遍历左边，如果大于右节点，遍历右边
     * */
    public int closestValue(TreeNode root, double target) {
        TreeNode head = root;
        int closestVal = root.val;
        while (head != null) {
            if (Math.abs(head.val - target) < Math.abs(closestVal - target)) {
                closestVal = head.val;
            }
            head = target < head.val ? head.left : head.right;
        }
        return closestVal;
    }
}

```

#### 272. Closest Binary Search Tree Value II

```java
/**
题意： 在bst里面找和target最近的k个值
 */
public class ClosestBinarySearchTreeValueII0272 {
    /** time O(n) space(n) 方法：中序遍历 + 队列 + 递归
     思路：就是用一个队列，然后用中序遍历，从第一个node开始依次和target比，当队列里面加满k个值之后，
     再之后再遇到新node的时候, 比较当前node与target的差和队首和target的差，如果前者更小，就poll队首，
     加入当前node，反之就可以直接返回了，因为后面的肯定都比当前的大了
     优化： need to think about the O(logN) solution
     */
    public List<Integer> closestKValues(TreeNode root, double target, int k) {
        Deque<Integer> queue = new ArrayDeque<>(k + 1);
        inorder(root, target, k, queue);
        return new ArrayList<Integer>(queue);
    }
    private void inorder(TreeNode root, double target, int k, Deque<Integer> queue) {
        if (root == null) {
            return;
        }
        inorder(root.left, target, k, queue);
        queue.offer(root.val);
        if (queue.size() > k) {
            if (Math.abs(root.val - target) < Math.abs(queue.peek() - target)) {
                queue.poll();
            } else {
                queue.pollLast();
                return;
            }
        }
        inorder(root.right, target, k, queue);
    }
}
```

#### 285. Inorder Successor in BST

```java
/**
求bst的某节点的后继节点
 */
public class InorderSuccessorinBST0285 {
    /** time O(logn) space O(1) 方法：
     * 思路：中序遍历，p后面的节点就是结果，
     * 优化：应该可以优化成用二分来解决，如果当前p 大于等于 root，后继就不可能在左边，那么遍历右子树
     * 如果p 小于 root，那么root本身就可能是后继，但还是要继续遍历左子树
     * 小优化：root其实可以直接从 p的右孩子开始遍历。因为p之前的节点都已经没用了
     * */
    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        if (root == null || p == null) {
            return null;
        }
        TreeNode head = p.right != null ? p.right : root;
        TreeNode succ = null;
        while (head != null) {
            if (p.val >= head.val) {
                head = head.right;
            } else {
                succ = head;
                head = head.left;
            }
        }
        return succ;
    }
}

```
#### 450. Delete Node in a BST

```java
/**
 * 题意：删除bst中的目标节点，并保持bst属性
 * root = [5,3,6,2,4,null,7]
 key = 3

        5
       / \
      3   6
     / \   \
    2   4   7

 Given key to delete is 3. So we find the node with value 3 and delete it.

 One valid answer is [5,4,6,2,null,null,7], shown in the following BST.

       5
      / \
     4   6
    /     \
   2       7

 Another valid answer is [5,2,6,null,4,null,7].

        5
       / \
      2   6
       \   \
        4   7*/
public class DeleteNodeinaBST0450 {
    /** time O(logn) space O(1) 方法：二分
     * 思路：如果target比root大，遍历右边，比root小，遍历左边
     * 如果等于root，有3种情况，1。root为leaf，直接删除
     * 2. root 只有左孩子或只有右孩子，那么直接用root父节点。指向左或右节点即可
     * 3. root同时有左右孩子，那么就可以先swap一下root和他的后继或前驱。然后删除后继或前驱
     * 因为后继和前驱必定是叶子
     * 注意，删除前驱不能直接 前驱=null，需要递归调用来删除前驱
     * 优化：
     * */
    public TreeNode deleteNode(TreeNode root, int key) {
        if (root == null) {
            return root;
        }
        if (key < root.val) {
            root.left = deleteNode(root.left, key);
        }
        if (key > root.val) {
            root.right = deleteNode(root.right, key);
        }
        if (key == root.val) {
            if (root.left != null && root.right != null) {
                TreeNode pre = findPre(root.left);
                root.val = pre.val;
                root.left = deleteNode(root.left, root.val);
                return root;
            }
            return root.left == null ? root.right : root.left;
        }
        return root;
    }
    private TreeNode findPre(TreeNode node) {
        for (; node.right != null; node = node.right);
        return node;
    }
}

```
#### 776. Split BST

```java

/**
 题目大意就是 将二叉搜索拆成两棵子BST，左子树的所有节点均不大于V，右子树的所有节点均大于V
 而且，V有可能不在树里

 Example 1:

 Input: root = [4,2,6,1,3,5,7], V = 2
 Output: [[2,1],[4,3,6,null,null,5,7]]
 Explanation:
 Note that root, output[0], and output[1] are TreeNode objects, not arrays.

 The given tree [4,2,6,1,3,5,7] is represented by the following diagram:

        4
      /   \
    2      6
   / \    / \
 1   3  5   7

 while the diagrams for the outputs are:

      4
     / \
    3   6      and     2
       / \            /
      5   7          1
 Note:

 The size of the BST will not exceed 50.
 The BST is always valid and each node's value is different.*/
public class SplitBST0776 {
    /** time O(H) space O(1) 方法：BST 二分
     * 思路：首先判断边界条件，root为空或者root本身就是V的情况
     * 之后，如果左孩子是V，那么cache左孩子，root左指针连接左孩子的右孩子，
     * 注意，这时候要把cache的左孩子的右孩子清空，要不然结果就重复了，右孩子同理
     * 如果v不等于左右孩子，那么向下二分
     * 优化：
     * */
    public TreeNode[] splitBST(TreeNode root, int V) {
        if (root == null) {
            return new TreeNode[] {null, null};
        }
        if (root.val == V) {
            TreeNode left = root.left;
            root.left = null;
            return new TreeNode[] {left, root};
        }
        TreeNode head = root;
        while (head != null) {
            if (head.left != null && head.left.val == V) {
                TreeNode left = head.left;
                head.left = head.left.right;
                left.right = null;
                return new TreeNode[] {left, root};
            }
            if (head.right != null && head.right.val == V) {
                TreeNode right = head.right;
                head.right = head.right.left;
                right.left = null;
                return new TreeNode[] {right, root};
            }
            head = V < head.val ? head.left : head.right;
        }
        return new TreeNode[] {null, root};
    }
}

```

### 中序遍历

#### 98. Validate Binary Search Tree

```java
/**Given a binary tree, determine if it is a valid binary search tree (BST).

 Assume a BST is defined as follows:

 The left subtree of a node contains only nodes with keys less than the node's key.
 The right subtree of a node contains only nodes with keys greater than the node's key.
 Both the left and right subtrees must also be binary search trees.
 Example 1:
   2
  / \
 1   3
 Binary tree [2,1,3], return true.
 Example 2:
   1
  / \
 2   3
 Binary tree [1,2,3], return false.*/
public class ValidateBinarySearchTree0098 {
    /** time O(n) space O(1) 方法：中序遍历 或者 递归
     * 递归思路：从上到下，依次检查所有节点，每个节点都应该在一个范围内
     * 比如左孩子的范围应该就是（min，root.val），右孩子的范围是（root.val， max）
     * 注意，min和max的初始还是要用null，防止冲突
     * 优化：
     * */
    public boolean isValidBST(TreeNode root) {
        TreeNode head = root;
        TreeNode pre = null;
        while (head != null) {
            if (head.left == null) {
                if (pre != null && head.val <= pre.val) {
                    return false;
                }
                pre = head;
                head = head.right;
            } else {
                TreeNode tmp = head.left;
                while (tmp.right != null && tmp.right != head) {
                    tmp = tmp.right;
                }
                if (tmp.right == null) {
                    tmp.right = head;
                    head = head.left;
                } else {
                    if (pre != null && head.val <= pre.val) {
                        return false;
                    }
                    pre = head;
                    head = head.right;
                    tmp.right = null;
                }
            }
        }
        return true;
    }
    //方法2 递归
    public boolean isValidBST(TreeNode root) {
        return helper(root, null, null);
    }
    private boolean helper(TreeNode curr, Integer min, Integer max) {
        if (curr == null) {
            return true;
        }
        if (min != null && curr.val <= min) {
            return false;
        }
        if (max != null && curr.val >= max) {
            return false;
        }
        return helper(curr.left, min, curr.val) && helper(curr.right, curr.val, max);
    }
}
```
#### 99. Recover Binary Search Tree

```java
/** 题目大意：给定的bst里面有2个元素位置放错了，要求还原这个bst
 * 要求用O(1)空间
 * */
public class RecoverBinarySearchTree0099 {
    /** time O(n) space O(1) 方法：morris 中序遍历
     * 思路：记录2个node，first 和 second, 中序遍历的时候需要记录一个pre节点
     * 如果当前节点小于pre，那么现在就有2种情况，
     * 1，当前2个节点都是错的2个节点
     * 2. pre是第一个错的节点，后面还会有一个错的节点
     * 所以，第一次发现错误节点的时候，要标记 first 为 pre， second 为curr
     * 然后第二次如果又发现了一次错误的节点 ，那么就更新一下second就行了
     * 优化：
     * */
    public void recoverTree(TreeNode root) {
        if (root == null || root.left == null && root.right == null) {
            return;
        }
        TreeNode head = root;
        TreeNode pre = null;
        TreeNode firstMistake = null;
        TreeNode secondMisttake = null;

        while (head != null) {
            if (head.left == null) {
                if (pre != null && head.val < pre.val) {
                    if (firstMistake == null) {
                        firstMistake = pre;
                    }
                    secondMisttake = head;
                }
                pre = head;
                head = head.right;
            } else {
                TreeNode tmp = head.left;
                while (tmp.right != null && tmp.right != head) {
                    tmp = tmp.right;
                }
                if (tmp.right == null) {
                    tmp.right = head;
                    head = head.left;
                } else {
                    if (pre != null && head.val < pre.val) {
                        if (firstMistake == null) {
                            firstMistake = pre;
                        }
                        secondMisttake = head;
                    }
                    pre = head;
                    head = head.right;
                    tmp.right = null;
                }
            }
        }
        if (firstMistake != null) {
            int tmp = firstMistake.val;
            firstMistake.val = secondMisttake.val;
            secondMisttake.val = tmp;
        }
    }
}

```
#### 173. Binary Search Tree Iterator
```java
/**
 * 题目大意，写一个bst的迭代器，next 和 hasnext都要 O(1）时间
 * 空间 O(h), next返回下一个最小值，
.*/
public class BinarySearchTreeIterator0173 {
    /** time O(1) space O(h) 方法：用栈做中序遍历
     * 思路：头结点不为空的时候，一直往左走，同时把节点push入栈
     * 直到最左的叶子节点，pop出来，记录这个叶子节点的值，然后头结点指向右孩子
     * 优化：
     * */
    Deque<TreeNode> stack;
    TreeNode head;
    public BinarySearchTreeIterator0173(TreeNode root) {
        stack = new ArrayDeque<>();
        head = root;
    }

    /** @return whether we have a next smallest number */
    public boolean hasNext() {
        return head != null || !stack.isEmpty();
    }

    /** @return the next smallest number */
    public int next() {
        while (head != null) {
            stack.push(head);
            head = head.left;
        }
        head = stack.pop();
        int smallest = head.val;
        head = head.right;
        return smallest;
    }
}
```
#### 230. Kth Smallest Element in a BST
```java
/**
 * 题目大意： 找到在bst里面第k小的元素
 Note:
 You may assume k is always valid, 1 ≤ k ≤ BST's total elements.

 Follow up:
 如何优化 What if the BST is modified (insert/delete operations) often
 and you need to find the kth smallest frequently?
 * */
public class KthSmallestElementinaBST0230 {
    /** time O(k) space O(1) 方法：
     * 思路：中序遍历，遍历到在第k个数停止。这个题用二分也可以做，但二分的复杂度反而更高
     * 因为需要每次看当前数一共有多少节点。但其实，如果建树的时候，加入这个属性，就可以用O(H)
     * 优化：
     * follow up： 这题的难点其实在于Follow Up：如果我们频繁的操作该树，并且频繁的调用kth函数，
     * 有什么优化方法使时间复杂度降低至O(h)？h是树的高度。我们可以在TreeNode中加入一个rank成员
     * 这个变量记录的是该节点的左子树中节点的个数，其实就是有多少个节点比该节点小。
     * 这样我们就可以用二叉树搜索的方法来解决。这个添加rank的操作可以在建树的时候一起完成。
     * https://segmentfault.com/a/1190000008305885
     * */
    public int kthSmallest(TreeNode root, int k) {
        TreeNode head = root;
        while (head != null) {
            if (head.left == null) {
                if (--k == 0) {
                    return head.val;
                }
                head = head.right;
            } else {
                TreeNode tmp = head.left;
                while (tmp.right != null && tmp.right != head) {
                    tmp = tmp.right;
                }
                if (tmp.right == null) {
                    tmp.right = head;
                    head = head.left;
                } else {
                    if (--k == 0) {
                        return head.val;
                    }
                    head = head.right;
                    tmp.right = null;
                }
            }

        }
        return -1;
    }
}

```
#### 530. Minimum Absolute Difference in BST

```java
/**题目大意 在BST里面找任何2个节点的最小差值（绝对值）

 Example:

 Input:

   1
    \
    3
   /
  2

 Output:
 1

 Explanation:
 The minimum absolute difference is 1, which is the difference between 2 and 1
 (or between 2 and 3).
 Note: There are at least two nodes in this BST.*/
public class MinimumAbsoluteDifferenceinBST0530 {
    /** time O(n) space O(1) 方法：morris中序遍历
     * 思路：鉴于bst中序是升序数组，那么在一个升序数组里，任何一个数与另外一个数的最小差，
     * 只可能是和这个数前面或者后面的数，所以，中序遍历只要记录一个pre就可以了
     * 优化：可以用Morris来做O(1)空间
     * */
    public int getMinimumDifference(TreeNode root) {
        TreeNode head = root;
        TreeNode pre = null;
        int min = Integer.MAX_VALUE;
        while (head != null) {
            if (head.left == null) {
                if (pre != null) {
                    min = Math.min(min, Math.abs(head.val - pre.val));
                }
                pre = head;
                head = head.right;
            } else {
                TreeNode tmp = head.left;
                while (tmp.right != null && tmp.right != head) {
                    tmp = tmp.right;
                }
                if (tmp.right == null) {
                    tmp.right = head;
                    head = head.left;
                } else {
                    if (pre != null) {
                        min = Math.min(min, Math.abs(head.val - pre.val));
                    }
                    pre = head;
                    head = head.right;
                    tmp.right = null;
                }
            }
        }
        return min;
    }
}

```
#### 378. Convert Binary Search Tree to Doubly Linked List
Lintcode
```java
/** LintCode
 * 题意：把一个BST 转化成一个升序双向链表，
 * */
public class ConvertBinarySearchTreetoDoublyLinkedList0378 {
    /** time O() space O() 方法：
     * 思路：中序遍历，利用一个prev节点
     * 优化：
     * */
    public DoublyListNode bstToDoublyList(TreeNode root) {
        if (root == null) {
            return null;
        }
        DoublyListNode head = new DoublyListNode(0);
        DoublyListNode prev = head;
        Deque<TreeNode> stack = new ArrayDeque<>();
        while (root != null || !stack.isEmpty()) {
            while (root != null) {
                stack.push(root);
                root = root.left;
            }
            root = stack.pop();
            DoublyListNode curr = new DoublyListNode(root.val);
            prev.next = curr;
            curr.prev = prev;

            prev = prev.next;
            root = root.right;
        }
        head.next.prev = null;
        return head.next;
    }
    public class DoublyListNode {
        int val;
        DoublyListNode next, prev;
        DoublyListNode(int val) {
            this.val = val;
            this.next = this.prev = null;
        }
    }
}
```

### 第三种情况 - 构建树

构建树的情况比较特别，不属于中序和二分

#### 96. Unique Binary Search Trees

```java
/** 求N个节点一共有几种bst的构建方式，

 For example,
 Given n = 3, there are a total of 5 unique BST's.

  1         3     3      2      1
   \       /     /      / \      \
    3     2     1      1   3      2
   /     /       \                 \
  2     1         2                 3*/
public class UniqueBinarySearchTrees0096 {
    /** time O(N^2) space O(N) 方法：DP
     * 思路：两重循环，外层是一共有多少节点 1 到 n，
     * 里层是 左边有多少节点，右边节点的个数便可求出，total - 1 - left，减一是root
     * 所以，当前一共的构建方式就是左边的数量乘以右边的数量，
     * 因为对于左边的每一种，都对应右面的每一种
     * 优化：
     * */
    public int numTrees(int n) {
        if (n < 1) {
            return 0;
        }
        int totalBST = 0;
        int[] bst = new int[n + 1];
        bst[0] = 1;
        for (int total = 1; total <= n; ++total) {
            for (int left = 0; left < total; ++left) {
                bst[total] += bst[left] * bst[total - 1 - left];
            }
        }
        return bst[n];
    }
}
```

#### 95. Unique Binary Search Trees II 

![uniqueBST](UniqueBST.jpg)
```java
/** 列举N个节点所有BST的构建方式，

 For example,
 Given n = 3, there are a total of 5 unique BST's.

    1         3     3      2      1
     \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3*/
public class UniqueBinarySearchTreesII0095 {
    /** time close to O(n!), becaues this is a catelan number
     which is f(n) = f(1)*f(n-1) + f(2)*f(n-2) .. + f(n)*f(0) = (2n!) / (n+1)!*n!
     * 方法：递归
     * 思路：对于每一个区间，递归建 左子树的list 和 右子树的list，然后双循环建树
     * 注意：递归出口为当start大于end，这时候，不能单纯返回一个空list，而是要再list里面加个空节点
     * 优化：递归建数存在大量重复工作，可以用dp优化，cache一下sub result，但需要额外O(N!)空间
     * */
    public List<TreeNode> generateTrees(int n) {
        return n > 0 ? buildBST(1, n) : new ArrayList<>();
    }
    private List<TreeNode> buildBST(int start, int end) {
        List<TreeNode> bst = new ArrayList<>();
        if (start > end) {
            bst.add(null);
        }
        for (int i = start; i <= end; ++i) {
            List<TreeNode> left = buildBST(start, i - 1);
            List<TreeNode> right = buildBST(i + 1, end);
            for (TreeNode l : left) {
                for (TreeNode r : right) {
                    TreeNode root = new TreeNode(i);
                    bst.add(root);
                    root.left = l;
                    root.right = r;
                }
            }
        }
        return bst;
    }
}
```

#### 108. Convert Sorted Array to Binary Search Tree

```java
/**
题目大意：给一个有序数组，构建一个平衡的BST
 */
public class ConvertSortedArraytoBinarySearchTree0108 {
    /** time O(n) space O(1) 方法：递归
     * 思路：找到数组的中位数，然后以该数为root，左子树，为该数在数组中最左边的数，右边同理
     * 优化：
     * */
    public TreeNode sortedArrayToBST(int[] nums) {
        return nums == null ? null : helper(nums, 0, nums.length - 1);
    }
    private TreeNode helper(int[] nums, int start, int end) {
        if (start > end) {
            return null;
        }
        int mid = start + (end - start) / 2;
        TreeNode root = new TreeNode(nums[mid]);
        root.left = helper(nums, start, mid - 1);
        root.right = helper(nums, mid + 1, end);
        return root;
    }
}
```

#### 109. Convert Sorted List to Binary Search Tree

```java
/**
 题目大意：给一个有序链表，构建一个平衡的BST
 */
public class ConvertSortedListtoBinarySearchTree0109 {
    /** time O(Nlogn) space O(h) 方法：快慢指针找中点，中点为root，左子链表为左子树
     * 思路：用head 和 tail来记录当前子链表的区间
     * 优化：好像是有O(n)的方法
     * */
    public TreeNode sortedListToBST(ListNode head) {
        return head == null ? null : sortedListToBSTHelper(head, null);
    }
    private TreeNode sortedListToBSTHelper(ListNode head, ListNode tail) {
        if (head == tail) {
            return null;
        }
        ListNode fast = head;
        ListNode slow = head;
        while (fast != tail && fast.next != tail) {
            fast = fast.next.next;
            slow = slow.next;
        }
        TreeNode root = new TreeNode(slow.val);
        root.left = sortedListToBSTHelper(head, slow);
        root.right = sortedListToBSTHelper(slow.next, tail);
        return root;
    }
}

```
#### 654. Maximum Binary Tree

```java
/** 题目大意： 给一个无需 无重复元素 数组，要求建一个二叉树，每个root都要是当前子数组的最大值
 * Input: [3,2,1,6,0,5]
 Output: return the tree root node representing the following tree:

         6
      /    \
     3      5
     \     /
     2    0
      \
      1
 * */
public class MaximumBinaryTree0654 {
    /** 递归 time O(n^2) space O(n^2) 用栈 time O(n) space O(n)
     * 思路：直观的递归建树，每次在子数组中先找最大值，然后左边递归建左子树，右边同理
     * 但问题就是需要n方的时间，
     * 优化：可以利用一个栈，遍历数组，每次把当前元素建新节点，如果当前节点小于栈顶，
     * 那么既然是顺序遍历，那么当前节点肯定是栈顶的右子树
     * 如果当前节点大于栈顶，那么栈里，刚好比当前节点小一点的节点就肯定是当前节点的左子树
     * 而这个节点现在又变成了栈顶节点的右子树
     * */
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        Deque<TreeNode> stack = new ArrayDeque<>();
        for (int num : nums) {
            TreeNode curr = new TreeNode(num);
            while (!stack.isEmpty() && num > stack.peek().val) {
                curr.left = stack.pop();
            }
            if (!stack.isEmpty() && num < stack.peek().val) {
                stack.peek().right = curr;
            }
            stack.push(curr);
        }
        return stack.isEmpty() ? null : stack.peekLast();
    }
}
```
#### 105. Construct Binary Tree from Preorder and Inorder Traversal

```java
/**
 * 通过给定的前序，中序遍历结果，还原一颗二叉树
 * */
public class ConstructBinaryTreefromPreorderandInorderTraversal0105 {
    /** time O(n) space O(n) 方法：利用前序 和中序的特性 + map
     * 思路：前序遍历，第一个数是是root，后面一个是左子树root
     * 中序遍历，root左边的数都属于左子树，右边同理，
     * 通过前序遍历，可以找到root的值，这样也就可以找到root在中序数组中的位置
     * 优化：每次找位置需要O(N),如果用hashmap来映射一下中序的值和下标，可以减少到O(1)
     * */
    public TreeNode buildTree(int[] pre, int[] in) {
        if (pre == null || in == null || pre.length == 0 || pre.length != in.length) {
            return null;
        }
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < in.length; ++i) {
            map.put(in[i], i);
        }
        return buildHelper(map, pre, in, 0, in.length - 1, 0);
    }
    private TreeNode buildHelper(Map<Integer, Integer> map, int[] pre, int[] in,
                                 int instart, int inend, int prestart) {
        if (instart > inend) {
            return null;
        }
        TreeNode root = new TreeNode(pre[prestart]);
        int inroot = map.get(pre[prestart]);
        int secondPrestart = 1 + prestart + (inroot - instart);
        root.left = buildHelper(map, pre, in, instart, inroot - 1, prestart + 1);
        root.right = buildHelper(map, pre, in, inroot + 1, inend, secondPrestart);
        return root;
    }
}
```