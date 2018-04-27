---
title: 字典树
date: 2018-03-02 14:52:56
tags: [Trie]
categories: [Algorithm]
---

### Template

HashMap trie:
- 优点
    - 支持所有 Character
    - 相对更省空间

- 缺点
    - 查询时间相对长
    - 尤其是有 wildcard 做 DFS 时
---
Array trie:
- 优点
    - 查询速度快，尤其是有 wildcard 做 DFS 时
- 缺点
    - 每个 node 空间占用大
    - 比较依赖指定字符集，比如 'a-z' 这种

#### 208. Implement Trie (Prefix Tree)

```java
public class ImplementTrie0208 {
    /** time O(n) space O(26^n)  n是word的长度 方法：重点就是画个图
     * 思路：新建一个 trieNode类，然后类里放一个label，一个map，一个布尔表示endOfWord
     * 全局root本身不存字母，只存一个map，然后之后的每层存一个字母，和是否是单词末尾
     * 优化：可以合并search 和 prifix 2个方法，代码复用
     * */
    TrieNode root;
    public ImplementTrie0208() {
        root = new TrieNode();
    }

    public void insert(String word) {
        if (word == null || word.length() == 0) {
            return;
        }
        TrieNode head = root;
        for (int i = 0; i < word.length(); ++i) {
            char c = word.charAt(i);
            if (!head.map.containsKey(c)) {
                head.map.put(c, new TrieNode(c));
            }
            head = head.map.get(c);
            if (i == word.length() - 1) {
                head.endOfWord = true;
            }
        }
    }

    public boolean search(String word) {
        return check(word, false);
    }

    public boolean startsWith(String prefix) {
        return check(prefix, true);
    }
    private boolean check(String word, boolean prefix) {
        if (word == null || word.length() == 0) {
            return true;
        }
        TrieNode head = root;
        for (int i = 0; i < word.length(); ++i) {
            char c = word.charAt(i);
            if (!head.map.containsKey(c)) {
                return false;
            }
            head = head.map.get(c);
        }
        return prefix || head.endOfWord;
    }
    class TrieNode {
        char c;
        Map<Character, TrieNode> map;
        boolean endOfWord;
        TrieNode() {
            map = new HashMap<>(26);
        }
        TrieNode(char c) {
            this.c = c;
            map = new HashMap<>(26);
        }
    }
}

```

#### 212. Word Search II

```java
/**
 * 题意：找出list里面所有可以在board里面找到的单词
 * Given words = ["oath","pea","eat","rain"] and board =
 ['o','a','a','n'],
 ['e','t','a','e'],
 ['i','h','k','r'],
 ['i','f','l','v']
 ]
 Return ["eat","oath"].
 */
public class WordSearchII0212 {
    /** time O(mn *4^k) space O(4^k) mn 是board的大小, k是单词长度
     思路就是如果word list里面word很多的话，每次调用4放方向的dfs查询，肯定不效率
     1. 一个TrieNode 类，2种构造函数，4个变量，map， character， endofWord
     这里多加一个word，在leaf节点里。方便加入最后结果
     2. insert函数，把单个单词加到trie
     3. buildtrie函数，把整个list的每个单词加到trie,(小优化：如果单词长度大于max则不加)
     4. explore函数，4个方向 探索board，
     **先查看当前节点是否是leaf，如果是，结果中加入word，并把endOfword标记成false
     因为如果不标记成false，之后从第二，第三个字母bfs的时候，会重复加入该单词
     a, 检查越界，visited，当前字母在trie的map里面是否为空
     b. 标记visited，4方向dfs，trie 传入下一层 节点
     c. 回溯visited
     5，findwords主函数，遍历整个board
     */
    public List<String> findWords(char[][] board, String[] words) {
        List<String> res = new ArrayList<>();
        if (board == null || board.length == 0 || board[0] == null || board[0].length == 0 ||
                words == null || words.length == 0) {
            return res;
        }
        TrieNode root = buildTrie(board.length * board[0].length, words);
        boolean[][] visited = new boolean[board.length][board[0].length];
        for (int i = 0; i < board.length; ++i) {
            for (int j = 0; j < board[0].length; ++j) {
                explore(board, visited, res, root, i, j);
            }
        }
        return res;
    }
    private void explore(char[][] board, boolean[][] visited, List<String> res,
                         TrieNode root, int row, int col) {
        if (root.endOfWord) {
            res.add(root.word);
            root.endOfWord = false;
        }
        if (row < 0 || row == board.length || col < 0 || col == board[0].length ||
               visited[row][col] || !root.map.containsKey(board[row][col])) {
            return;
        }
        visited[row][col] = true;
        root = root.map.get(board[row][col]);
        explore(board, visited, res, root, row + 1, col);
        explore(board, visited, res, root, row - 1, col);
        explore(board, visited, res, root, row, col + 1);
        explore(board, visited, res, root, row, col - 1);
        visited[row][col] = false;
    }
    private TrieNode buildTrie(int max, String[] words) {
        TrieNode root = new TrieNode();
        for (String word : words) {
            if (word == null || word.length() == 0 || word.length() > max) {
                continue;
            }
            insert(word, root);
        }
        return root;
    }
    private void insert(String word, TrieNode root) {
        for (int i = 0; i < word.length(); ++i) {
            char c = word.charAt(i);
            if (!root.map.containsKey(c)) {
                root.map.put(c, new TrieNode(c));
            }
            root = root.map.get(c);
            if (i == word.length() - 1) {
                root.endOfWord = true;
                root.word = word;
            }
        }
    }
    class TrieNode {
        char c;
        Map<Character, TrieNode> map;
        boolean endOfWord;
        String word;
        TrieNode() {
            map = new HashMap<>();
        }
        TrieNode(char c) {
            this.c = c;
            map = new HashMap<>();
        }
    }
}
```

#### 211. Add and Search Word - Data structure design
```java
/** 题意：设计add，和search 2个函数，search的时候，可以用。来代表任何字母
 * addWord("bad")
 addWord("dad")
 addWord("mad")
 search("pad") -> false
 search("bad") -> true
 search(".ad") -> true
 search("b..") -> true*/
public class AddandSearchWord0211 {
    /**  time worst case O(26^n) space O(26^n)
     * 如果是“...z”这种情况，且字典树比较满的时候，就会是26^n run time
     * 思路就是如果遇到“。”那么就把该层map里面所有非null的节点都向下dfs一次
     *
     */
    TrieNode root;
    public AddandSearchWord0211() {
        root = new TrieNode();
    }

    /** Adds a word into the data structure. */
    public void addWord(String word) {
        if (word == null || word.length() == 0) {
            return;
        }
        TrieNode head = root;
        for (int i = 0; i < word.length(); ++i) {
            char c = word.charAt(i);
            if (!head.map.containsKey(c)) {
                head.map.put(c, new TrieNode(c));
            }
            head = head.map.get(c);
        }
        head.endOfWord = true;
    }

    /**  A word could contain the dot character '.' to represent any one letter. */
    public boolean search(String word) {
        TrieNode head = root;
        return searchHelper(word, 0, head);
    }
    private boolean searchHelper(String word, int index, TrieNode head) {
        if (word == null || word.length() == 0 || index == word.length()) {
            return head.endOfWord;
        }
        char c = word.charAt(index);
        if (c == '.') {
            for (TrieNode trieNode : head.map.values()) {
                if (searchHelper(word, index + 1, trieNode)) {
                    return true;
                }
            }
        }
        return head.map.containsKey(c) && searchHelper(word, index + 1, head.map.get(c));
    }
    class TrieNode {
        char c;
        Map<Character, TrieNode> map;
        boolean endOfWord;
        TrieNode() {
            map = new HashMap<>();
        }
        TrieNode(char c) {
            this.c = c;
            map = new HashMap<>();
        }
    }
}
```
#### 425. Word Squares
```java
/**
 * 题意：给定一组没有重复的单词，从中寻找所有可能的“四方连词”。单词可以重复使用
 * */
public class WordSquares0425 {
    /** time O(n^5) space O(n^5) 方法：字典树 + dfs + 回溯
     * 最坏可能 有n个单词，长度为5，前4个都一样，只有最后一个字母不一样，那么以每个prefix开始的list 都有n个
     * 那么递归中，就会变成对于每个单词 都延伸出n个递归，但由于单词长度为5，那么最多5层，也就是 n的5次方
     * 做法一，暴力 dfs加回溯 遍历字符串数组，依次放入所有字符串，因为字符串可以重复使用，所以每次index 从 0开始
     * 然后检查新加入的字符串 是否合法， 判断标准是，新加入单词的第i位是否 和 list里面第i个单词的第size 个字母一样
     * 大集合 超时，然后 用了一个 记忆化 搜索做优化，如果 以当前单词开始，不能最终成功，那么map加入 <word, false>
     * 还是超时，继续优化成 以某些单词的组合开始，是否可以成功，用 拼接后的字符串组合做key，还是超时
     * 看了眼 tag，原来要用 字典树，然后 ，，很巧的 一开始竟然 做了一个 单词不可以复用的解法吗，用set检查
     *
     * ！ 正确思路 ！先构建字典树，每个节点  加一个 list，记录以当前子字符串 为 prefix 单词list
     * 然后dfs的时候，先找到 当前 需要加入单词的 prefix 要求， 用list里面所有单词的 index为size的字符 总和
     * 然后再字典树里面找到含有这个prefix的 单词表，然后 依次加入，然后回溯
     * 优化：
     * */
    public List<List<String>> wordSquares(String[] words) {
        List<List<String>> res = new ArrayList<>();
        if (words == null || words.length == 0) {
            return res;
        }
        TrieNode root = buildTrie(words);
        int length = words[0].length();
        for (String word : words) {
            wordSquaresDFS(res, new ArrayList<>(Collections.singletonList(word)), root, length);
        }
        return res;
    }
    private void wordSquaresDFS(List<List<String>> res, List<String> list, TrieNode root, int target) {
        if (list.size() == target) {
            res.add(new ArrayList<>(list));
            return;
        }
        StringBuilder prefix = new StringBuilder();
        for (String s : list) {
            prefix.append(s.charAt(list.size()));
        }
        List<String> prefixList = getPrefixList(prefix.toString(), root);
        for (String word : prefixList) {
            list.add(word);
            wordSquaresDFS(res, list, root, target);
            list.remove(list.size() - 1);
        }
    }
    private List<String> getPrefixList(String word, TrieNode root) {
        for (int i = 0; i < word.length(); ++i) {
            if (!root.map.containsKey(word.charAt(i))) {
                return new ArrayList<>();
            }
            root = root.map.get(word.charAt(i));
        }
        return root.prefix;
    }
    private TrieNode buildTrie(String[] words) {
        TrieNode root = new TrieNode();
        for (String word : words) {
            insert(word, root);
        }
        return root;
    }
    private void insert(String word, TrieNode root) {
        for (int i = 0; i < word.length(); ++i) {
            if (!root.map.containsKey(word.charAt(i))) {
                root.map.put(word.charAt(i), new TrieNode(word.charAt(i)));
            }
            root = root.map.get(word.charAt(i));
            root.prefix.add(word);
        }
    }
    class TrieNode {
        char c;
        Map<Character, TrieNode> map;
        List<String> prefix;
        TrieNode() {
            map = new HashMap<>(26);
            prefix = new ArrayList<>();
        }
        TrieNode(char c) {
            this.c = c;
            map = new HashMap<>(26);
            prefix = new ArrayList<>();
        }
    }
}

```