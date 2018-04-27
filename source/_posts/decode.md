---
title: Encode, Decode
date: 2018-02-16 13:22:42
tags: [Decode]
categories: [Algorithm]
---

这种题型 其实并不是可以总结到一起的，因为虽然很多题都是解码问题，但方法有时候还是很不一样的

#### 91. Decode Ways

```java
/**
 * 题意：给一个字符串，里面是纯数字，是经过加密后的，加密规则是 每个数字对应一个字母
 * 比如
 'A' -> 1
 'B' -> 2
 ...
 'Z' -> 26
 但 26 其实可以解密成 'Z' 或者是 'BF'
 求一共有几种解密的方法
 * */
public class DecodeWays0091 {
    /** time O(n) space O(n) 方法：dp 类似斐波那契数列
     * 思路：遍历字符串，cache之前的结果，然后当前结果，取决去前面的结果
     * 状态：既然求一共多少种方法，状态就是int
     * 初始：
     * 转移方程：如果当前数字是0，那么前一位只能是1或者2，而这时候，dp[i] = dp[i-2]
     * 如果当前数字不为0，那么前一位和当前数组成的2位数，有2种情况
     * 小于10或大于26.这时候，dp[i] = dp[i-1]
     * 注意 ：如果在10 到26之间，dp[i] = dp[i-1] + dp[i-2]
     * 注意，单独0的纯在，还有30，40 ，00的存在
     * 优化：
     * */
    public int numDecodings(String s) {
        if (s == null || s.length() == 0 || s.charAt(0) == '0') {
            return 0;
        }
        int len = s.length();
        int[] ways = new int[len + 1];
        ways[0] = 1;
        ways[1] = 1;
        for (int i = 2; i <= len; ++i) {
            if (s.charAt(i - 1) == '0') {
                if (s.charAt(i - 2) == '1' || s.charAt(i - 2) == '2') {
                    ways[i] = ways[i - 2];
                } else {
                    return 0;
                }
            } else {
                int sum = (s.charAt(i - 2) - '0') * 10 + s.charAt(i - 1) - '0';
                if (sum < 10 || sum > 26) {
                    ways[i] = ways[i - 1];
                } else {
                    ways[i] = ways[i - 1] + ways[i - 2];
                }
            }
        }
        return ways[len];
    }
}

```

#### 394. Decode String

```java

/***
 * 题意：把加密的字符串解码，规则就是，中括号里面的所有东西，都要重复中括号外面数字的次数
 * s = "3[a]2[bc]", return "aaabcbc".
 s = "3[a2[c]]", return "accaccacc".
 s = "2[abc]3[cd]ef", return "abcabccdcdcdef".
 */
public class DecodeString0394 {
    /** time O(n) space O(n) 方法：递归 分制
     * 思路 ：就是把字符串拆分成3部分，因为处理部分字符串和处理整体没有区别，
     * 出口条件就是，如果三部分中任何一部分 只有纯字符串，那么直接返回就行，
     * 第一部分，中括号左边，包括不需要decode的字母和重复次数
     * 第二部分是 中括号里面，用递归，得到新的字符串之后，乘以第一部分的重复次数，
     * 第三部分是中括号 外面，继续递归调用，这次需要把后面完全看成一个整体，和第二部分不同
     * helper函数需要额外2个参数，来标记当前部分的上下界，
     * 第二部分的步骤就是，如果遇到一个开括号，那么先找到对应的闭括号的下标，
     * 注意，要问没有没数字为0，比如0[ab] 这种情况，
     * 优化：
     * */
    public String decodeString(String s) {
        return (s == null || s.length() == 0) ? s : decodeHelper(s, 0, s.length() - 1);
    }
    private String decodeHelper(String s, int start, int end) {
        int indexOfCloseBracket = findCloseBracket(s, start, end);
        if (indexOfCloseBracket == end + 1) {
            return s.substring(start, end + 1);
        }
        StringBuilder firstPart = new StringBuilder();
        String secondPart = "";
        String lastPart = "";
        int times = 0;
        for (int i = start; i <= end; ++i) {
            char c = s.charAt(i);
            if (Character.isDigit(c)) {
                times *= 10;
                times += (c - '0');
            }
            if (Character.isLetter(c)) {
                firstPart.append(c);
            }
            if (c == '[') {
                secondPart = decodeHelper(s, i + 1, indexOfCloseBracket - 1);
                secondPart = buildString(secondPart, times);
                lastPart = decodeHelper(s, indexOfCloseBracket + 1, end);
                break;
            }
        }
        return firstPart.append(secondPart).append(lastPart).toString();
    }
    private int findCloseBracket(String s, int start, int end) {
        int open = 0;
        for (int i = start; i <= end; ++i) {
            if (s.charAt(i) == '[') {
                ++open;
            }
            if (s.charAt(i) == ']' && --open == 0) {
                return i;
            }
        }
        return end + 1;
    }
    private String buildString(String s, int count) {
        StringBuilder builder = new StringBuilder(s);
        for (int i = 1; i < count; ++i) {
            builder.append(s);
        }
        return builder.toString();
    }
}

```

#### 271. Encode and Decode Strings

```java
/**
 * 加密一个list 的字符串 成一个字符串，然后再用这个字符串 解密回原来的list
 * */
public class EncodeandDecodeStrings0271 {
    /** time O(n) space O(n) 方法：
     * 思路：用特殊符号来分隔字符串的方法是不可取的，因为这个特殊字符串也有可能存在在原字符串中
     * 所以，因为用每个字符串的长度，加一个特殊符号来分隔
     * 注意: decode的时候，注意清零单词长度
     * 优化：
     * */
    public String encode(List<String> strs) {
        if (strs.isEmpty()) {
            return "";
        }
        StringBuilder encodeString = new StringBuilder();
        for (String str : strs) {
            encodeString.append(str.length()).append("#").append(str);
        }
        return encodeString.toString();
    }

    // Decodes a single string to a list of strings.
    public List<String> decode(String s) {
        List<String> decodeList = new ArrayList<>();
        if (s == null || s.length() == 0) {
            return decodeList;
        }
        int len = 0;
        for (int i = 0; i < s.length();) {
            char c = s.charAt(i);
            if (Character.isDigit(c)) {
                len *= 10;
                len += (c - '0');
                ++i;
            }
            if (c == '#') {
                int end = ++i + len;
                StringBuilder curr = new StringBuilder();
                for (; i < end; ++i) {
                    curr.append(s.charAt(i));
                }
                decodeList.add(curr.toString());
                len = 0;
            }
        }
        return decodeList;
    }
}
```

#### 297. Serialize and Deserialize Binary Tree

```java
/**
 * 题意，序列化和反序列化一颗二叉树
 * */
public class SerializeandDeserializeBinaryTree0297 {
    /** time O(n) space O(n) 方法：先序遍历，
     * 思路：用一个特殊符号来分隔节点，用另一个特殊符号来充当null值
     * 注意，encode的时候，如果当前节点是null，要在steam里面加个#表示这个地方有空值
     * 在decode的时候，建新节点的时候，先看当前steam是不是#，是#就跳过
     * follow up：如果这个树是BST，可以怎么优化
     * */
    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if (root == null) {
            return "null";
        }
        StringBuilder encode = new StringBuilder();
        Deque<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            root = queue.poll();
            if (root == null) {
                encode.append(",#");
            } else {
                encode.append(",").append(root.val);
                queue.offer(root.left);
                queue.offer(root.right);
            }
        }
        return encode.substring(1);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data.equals("null")) {
            return null;
        }
        String[] token = data.split(",");
        TreeNode root = new TreeNode(Integer.parseInt(token[0]));
        Deque<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        for (int i = 1; i < token.length; ++i) {
            TreeNode head = queue.poll();
            if (!token[i].equals("#")) {
                head.left = new TreeNode(Integer.parseInt(token[i]));
                queue.offer(head.left);
            }
            if (++i < token.length && !token[i].equals("#")) {
                head.right = new TreeNode(Integer.parseInt(token[i]));
                queue.offer(head.right);
            }
        }
        return root;
    }
}

```
#### 449. Serialize and Deserialize BST

```java

/**
 * 题意：encode 和 decode 一个bst，要求，尽量压缩
 * */
public class SerializeandDeserializeBST0449 {
    /** time O(n^2) space O(n) 方法：先序遍历 + 递归造树
     * 思路：encode直接先序遍历，并且不用处理null值
     * decode的时候，首先先把data都放进一个队列里面，
     * 然后用一个 helper 函数。递归的把当前队列小于栈顶的元素放到left 队列里面
     * 优化：
     * */
    public String serialize(TreeNode root) {
        if (root == null) {
            return "null";
        }
        Deque<TreeNode> stack = new LinkedList<>();
        StringBuilder encode = new StringBuilder();
        while (root != null || !stack.isEmpty()) {
            while (root != null) {
                stack.push(root);
                encode.append(",").append(root.val);
                root = root.left;
            }
            root = stack.pop();
            root = root.right;
        }
        return encode.substring(1);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data.equals("null")) {
            return null;
        }
        Deque<Integer> queue = new ArrayDeque<>();
        String[] nodes = data.split(",");
        for (String node : nodes) {
            queue.offer(Integer.parseInt(node));
        }
        return buildTree(queue);
    }
    private TreeNode buildTree(Deque<Integer> queue) {
        if (queue.isEmpty()) {
            return null;
        }
        TreeNode root = new TreeNode(queue.poll());
        Deque<Integer> left = new ArrayDeque<>();
        while (!queue.isEmpty() && queue.peek() < root.val) {
            left.offer(queue.poll());
        }
        root.left = buildTree(left);
        root.right = buildTree(queue);
        return root;
    }
}
```
#### 536. Construct Binary Tree from String
```java
/**
 * 题意：从一个字符串构建一颗二叉树，规则是这样，
 * Input: "4(2(3)(1))(6(5))"

        4
      /   \
     2     6
    / \   /
   3  1  5
 * */
public class ConstructBinaryTreefromString0536 {
    /** time O(n^2) space O() 方法：先序遍历，暴力？
     * 思路：先找到root的值，然后通过后面括号的匹配。分别找到左子树，和 右子树，然后递归建树
     * 优化：既然是括号问题 ，应该可以用栈来优化？
     * */
    public TreeNode str2tree(String s) {
        return (s == null || s.length() == 0) ? null : helper(s.toCharArray(), 0, s.length() - 1);
    }
    private TreeNode helper(char[] c, int start, int end) {
        int sign = 1;
        if (c[start] == '-') {
            sign = -1;
            ++start;
        }
        int value = 0;
        while (start <= end && c[start] != '(') {
            value = 10 * value + c[start++] - '0';
        }
        Integer leftStart = null;
        if (++start <= end) {
            leftStart = start;
        }
        int open = 1, close = 0;
        while (open > close && start <= end) {
            if (c[start] == '(') {
                ++open;
            }
            if (c[start] == ')') {
                ++close;
            }
            if (open > close) {
                ++start;
            }
        }
        int leftEnd = start - 1;
        Integer rightStart = null;
        if (++start <= end) {
            rightStart = ++start;
        }
        TreeNode root = new TreeNode(sign * value);
        root.left = (leftStart == null) ? null : helper(c, leftStart, leftEnd);
        root.right = (rightStart == null) ? null : helper(c, rightStart, end - 1);
        return root;
    }
}

```
#### 385. Mini Parser

```java

/**
 * 题意：把给定的一个string 反序列化成一个NextedInteger类
 * 例如 "[123,[456,[789]]]"
 * */
public class MiniParser0385 {
    /** time O(n) space O(n) 方法：用栈，分情况处理
     * 思路：如果s里面是纯数字直返，反之，遇到开括号的时候应该用栈顶加入后面的数字或list
     * 如果是逗号或者闭括号，就可以确定，数字的结尾或者当前list的结束，
     * 如果是闭括号，就可以pop除栈顶
     * 注意：考虑input 字符串是一个空的 nestedinteger的情况，比如"[]" 或者"[[]]"
     * */
    public NestedInteger deserialize(String s) {
        if (s == null || s.length() == 0) {
            return null;
        }
        if (!s.startsWith("[")) {
            return new NestedInteger(Integer.parseInt(s));
        }
        Deque<NestedInteger> stack = new ArrayDeque<>();
        NestedInteger root = new NestedInteger();
        stack.push(root);
        for (int i = 1, start = 1; i < s.length(); ++i) {
            char c = s.charAt(i);
            if (c == '[') {
                NestedInteger curr = new NestedInteger();
                stack.peek().add(curr);
                stack.push(curr);
                start = i + 1;
            } else if (c == ',' || c == ']') {
                if (start < i) {
                    int val = Integer.parseInt(s.substring(start, i));
                    stack.peek().add(new NestedInteger(val));
                }
                if (c == ']') {
                    stack.pop();
                }
                start = i + 1;
            }
        }
        return root;
    }
}

```