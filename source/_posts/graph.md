---
title: 图，并查集，拓扑
date: 2018-02-11 02:27:53
tags: [Graph]
categories: [Algorithm]
---

### Union Find 并查集

并查集 解决的问题，大概就是，是否联通，是否有环, 或者图里面有几个部分之类的问题
这个部分，能用并查集做的，其实也几乎可以用dfs做，所以每道题贴2种方法

并查集的时间复杂度：Weighted Quick-Union With Path Compression

如果是用 有权重外加压缩路径的方法的话， find 和 union的复杂度都是接近于O(1) amortized 
这个时间复杂度是 出处于 inverse ackermann function，
用a（n）来表达，n是任何数的时候，a（n）都不超过5
#### 261. Graph Valid Tree

```java
/**
 * 给n个节点，标号0 到 n-1，和一个list的无向边，求这些边可不可以组成一颗树
 * 假设list里面没有重复边，
 * 例子 Given n = 5，edge [[0, 1], [0, 2], [0, 3], [1, 4]], return true.

 Given n = 5 ，[[0, 1], [1, 2], [2, 3], [1, 3], [1, 4]], return false.*/
public class GraphValidTree0261UnionFind {
    /** time O(V + E) space O(V + E) 方法：dfs 或者union find
     * 思路：UnoinFind 照常一个 union方法，一个find方法，外加一个count
     * 每次union，count减一
     * 遍历 所有边，每次union的之前，先看2个定点是否已经属于同一个root了
     * 如果是，说明这条边就会把树变成图，（环），如果没有任何环
     * 而且最后count等于1，说明所有节点都联通，是valid
     * 优化：
     * */
    public boolean validTree(int n, int[][] edges) {
        UnionFind uf = new UnionFind(n);
        for (int[] edge : edges) {
            if (uf.find(edge[0]) == uf.find(edge[1])) {
                return false;
            }
            uf.union(edge[0], edge[1]);
        }
        return uf.getCount() == 1;
    }
    class UnionFind {
        Map<Integer, Integer> parents = new HashMap<>();
        Map<Integer, Integer> ranking = new HashMap<>();
        int count;
        public UnionFind(int n) {
            for (int i = 0; i < n; ++i) {
                parents.put(i, i);
                ranking.put(i, 0);
            }
            count = n;
        }
        public int find(int target) {
            while (target != parents.get(target)) {
                target = parents.get(target);
            }
            return target;
        }
        public int getCount() {
            return count;
        }
        public void union(int a, int b) {
            int rootA = find(a);
            int rootB = find(b);
            if (rootA == rootB) {
                return;
            }
            --count;
            int rankA = ranking.get(rootA);
            int rankB = ranking.get(rootB);
            if (rankA > rankB) {
                parents.put(rootB, rootA);
                ranking.put(rootA, rankA + rankB);
            } else {
                parents.put(rootA, rootB);
                ranking.put(rootB, rankA + rankB);
            }
        }
    }
}
```

```java
public class GraphValidTree0261 {
    /** time O(V + E) space O(V + E) 方法：dfs 或者union find
     * 思路：首先可以建个图，然后用一个数组来标记每个节点的状态，0为未访问，1：访问中，2.访问过
     * 然后从任何一个节点开始，dfs，看有没有环 和 联不联通，如果无环且联通的话，说明valid
     * 优化：
     * */
    public boolean validTree(int n, int[][] edges) {
        int[] nodeStatus = new int[n];
        ArrayList[] graph = new ArrayList[n];
        for (int i = 0; i < n; ++i) {
            graph[i] = new ArrayList<Integer>();
        }
        for (int[] edge : edges) {
            graph[edge[0]].add(edge[1]);
            graph[edge[1]].add(edge[0]);
        }
        if (validTreeHelper(graph, nodeStatus, 0)) {
            for (int node : nodeStatus) {
                if (node != 2) {
                    return false;
                }
            }
            return true;
        }
        return false;
    }
    private boolean validTreeHelper(ArrayList[] graph, int[] nodeStatus, int i) {
        if (nodeStatus[i] == 2) {
            return false;
        }
        nodeStatus[i] = 1;
        ArrayList<Integer> curr = graph[i];
        for (int c : curr) {
            if (nodeStatus[c] == 2) {
                return false;
            }
            if (nodeStatus[i] == 0) {
                if (!validTreeHelper(graph, nodeStatus, c)) {
                    return false;
                }
            }
        }
        nodeStatus[i] = 2;
        return true;
    }
}
```

#### 323. Number of Connected Components in an Undirected Graph

```java
/**
 * Given n nodes labeled from 0 to n - 1 and a list of undirected edges
 * write a function to find the number of connected components in an undirected graph.

 Example 1:
 0          3
 |          |
 1 --- 2    4
 Given n = 5 and edges = [[0, 1], [1, 2], [3, 4]], return 2.

 Example 2:
 0           4
 |           |
 1 --- 2 --- 3
 Given n = 5 and edges = [[0, 1], [1, 2], [2, 3], [3, 4]], return 1.

 Note:
no duplicate edges will appear in edges. Since all edges are undirected,
 [0, 1] is the same as [1, 0] and thus will not appear together in edges.*/
public class NumberofConnectedComponentsinanUndirectedGraph0323UnionFind {
    /** time O(V + E) space O(V + E) 方法：并查集，看有几部分有相同root就可以
     * 思路：unoin所有边，每次有新的2条边可以union的话，（有不同root的话)
     * count减1，这样，最后还剩几，就是有几部分
     * 优化：
     * */
    public int countComponents(int n, int[][] edges) {
        UnionFind uf = new UnionFind(n);
        for (int[] edge : edges) {
            uf.union(edge[0], edge[1]);
        }
        return uf.getCount();
    }
    class UnionFind {
        Map<Integer, Integer> parents = new HashMap<>();
        Map<Integer, Integer> ranking = new HashMap<>();
        int count;
        public UnionFind(int n) {
            for (int i = 0; i < n; ++i) {
                parents.put(i, i);
                ranking.put(i, 0);
                count = n;
            }
        }
        public int find(int target) {
            while (target != parents.get(target)) {
                target = parents.get(target);
            }
            return target;
        }
        public int getCount() {
            return count;
        }
        public void union(int a, int b) {
            int rootA = find(a);
            int rootB = find(b);
            if (rootA == rootB) {
                return;
            }
            --count;
            int rankA = ranking.get(rootA);
            int rankB = ranking.get(rootB);
            if (rankA > rankB) {
                parents.put(rootB, rootA);
                ranking.put(rootA, rankA + rankB);
            } else {
                parents.put(rootA, rootB);
                ranking.put(rootB, rankA + rankB);
            }
        }
    }
}
```

```java
public class NumberofConnectedComponentsinanUndirectedGraph0323 {
    /** time O(V + E) space O(V + E) 方法：建图，然后dfs，几次走完就是几个components
     * 思路：先用一个arraylist 数组 建图，然后用一个 array标记每个节点的状态
     * 可以用0表示未访问，1表示访问中，2表示访问结束，访问中的作用就是防止在dfs过程中回头
     * 扫描的图的时候，从数组index 0开始，扫完整个数组，遇到几次0 就是有几个components
     * 优化：
     * */
    public int countComponents(int n, int[][] edges) {
        if (edges == null || edges.length == 0 || edges[0] == null || edges[0].length == 0) {
            return n;
        }
        ArrayList[] graph = buildGraph(n, edges);
        int[] nodes = new int[n];
        int countComponents = 0;
        for (int i = 0; i < n; ++i) {
            if (nodes[i] == 0) {
                exploreGraph(nodes, graph, i);
                ++countComponents;
            }
        }
        return countComponents;
    }
    private void exploreGraph(int[] nodes, ArrayList[] graph, int index) {
        nodes[index] = 1;
        ArrayList<Integer> curr = graph[index];
        for (int i : curr) {
            if (nodes[i] == 0) {
                exploreGraph(nodes, graph, i);
            }
        }
        nodes[index] = 2;
    }
    private ArrayList[] buildGraph(int n, int[][] edges) {
        ArrayList[] graph = new ArrayList[n];
        for (int i = 0; i < n; ++i) {
            graph[i] = new ArrayList<Integer>();
        }
        for (int[] edge :edges) {
            graph[edge[0]].add(edge[1]);
            graph[edge[1]].add(edge[0]);
        }
        return graph;
    }
}
```

#### 547. Friend Circles

```java
/**
 * 给一个二维矩阵，m[i][i] 代表学生i自己， m[i][j] m[j][i] 代表 i和j是朋友，朋友关系
 * 可以传递，问，这个矩阵里面一共有几个朋友圈
 * */
public class FriendCircles0547UnionFind {
    /** time O(n) space O(n) 方法：dfs或者并查集
     * 思路：并查集 太直观了，
     * 优化：
     * */
    public int findCircleNum(int[][] M) {
        UnionFind uf = new UnionFind(M.length);
        for (int i = 0; i < M.length; ++i) {
            for (int j = i + 1; j < M[0].length; ++j) {
                if (M[i][j] == 1) {
                    uf.union(i, j);
                }
            }
        }
        return uf.getCount();
    }
    class UnionFind {
        Map<Integer, Integer> parents = new HashMap<>();
        Map<Integer, Integer> ranking = new HashMap<>();
        int count;
        public UnionFind(int n) {
            count = n;
            for (int i = 0; i < n; ++i) {
                parents.put(i, i);
                ranking.put(i, 1);
            }
        }
        public int find(int target) {
            while (target != parents.get(target)) {
                target = parents.get(target);
            }
            return target;
        }
        public void union(int a, int b) {
            int rootA = find(a);
            int rootB = find(b);
            if (rootA == rootB) {
                return;
            }
            --count;
            int rankA = ranking.get(rootA);
            int rankB = ranking.get(rootB);
            if (rankA > rankB) {
                parents.put(rootB, rootA);
                ranking.put(rootA, rankA + rankB);
            } else {
                parents.put(rootA, rootB);
                ranking.put(rootB, rankA + rankB);
            }
        }
        public int getCount() {
            return count;
        }
    }
}

```

```java

public class FriendCircles0547 {
    /** time O(n) space O(n) 方法：dfs
     * 思路：dfs的话就是先建图，然后dfs探索,
     * 优化：
     * */
    public int findCircleNum(int[][] M) {
        if (M == null || M.length == 0 || M[0] == null || M[0].length == 0) {
            return 0;
        }
        int friendCircles = 0;
        ArrayList[] graph = new ArrayList[M.length];
        int[] visit = new int[M.length];
        for (int i = 0; i < M.length; ++i) {
            graph[i] = new ArrayList<Integer>();
        }
        for (int i = 0; i < M.length; ++i) {
            for (int j = i + 1; j < M[0].length; ++j) {
                if (M[i][j] == 1) {
                    graph[i].add(j);
                    graph[j].add(i);
                }
            }
        }
        for (int i = 0; i < M.length; ++i) {
            if (visit[i] == 0) {
                dfsExplore(visit, graph, i);
                ++friendCircles;
            }
        }
        return friendCircles;
    }
    private void dfsExplore(int[] visit, ArrayList[] graph, int curr) {
        visit[curr] = 1;
        ArrayList<Integer> list = graph[curr];
        for (int l : list) {
            if (visit[l] == 0) {
                dfsExplore(visit, graph, l);
            }
        }
    }
}
```

#### 684. Redundant Connection

这个题，真是差点写疯了。有太多点值得记录一下。
首先，看了讨论区的一些答案，都有点不入眼的感觉。首先，几十个答案全是一个模子里面出来的
简洁的不太看的明白，而且，其中使得这个简洁答案的原因也有点归功于一开始初始化一个2000的数组，
我个人并不太喜欢这么做，不太灵活，如果面试中n很大怎么办？我依旧用hashmap来实现的
但确实弄得有点复杂，find和union 这两个函数倒是可以照搬模板，但add

---
博客写到一半 突然发现好像是自己犯傻了，不知道怎么的，鬼使神差自己写了一个add函数，把一个别人10行
程序，写成了60多行，当初想的是每次新加入一条边，怎么加入到已有的并查集里面，因为很有可能
2个顶点都还不存在，或者有一个不存在，把各种情况都cover了下，写博客的时候，灵光一闪，突然
想到可以在find的时候，把a,b 两顶点加到map里面，然后比较，如果find(a) == find(b) 说明当前
这条边是多余的，如果不相等，那么就把这条边和之前合并。

不过之前写的答案里面有个需要注意的地方，以前看别人博客提到过一个事就是比较两个Integer的时候不要用==
尽量还是用equals，我不以为然，因为，从来没遇到过问题，这次竟然真的踩坑了，之前在add函数里面初始了
root为null，如果当前定点不在map里的时候。

```java
/**
题意：给若干边，顺序遍历建图，求从条边开始，这个图开始有环，边的范围是3到1000个

 Example 1:
 Input: [[1,2], [1,3], [2,3]]
 Output: [2,3]
 Explanation: The given undirected graph will be like this:
   1
  / \
 2 - 3
 Example 2:
 Input: [[1,2], [2,3], [3,4], [1,4], [1,5]]
 Output: [1,4]
 Explanation: The given undirected graph will be like this:
 5 - 1 - 2
 |   |
 4 - 3
 Note:
 The size of the input 2D-array will be between 3 and 1000.
 integer in the 2D-array will be between 1 and N, where N is the size of the input array.*/
public class RedundantConnection0684 {
    /** time O(n) space O(n) 方法：并查集
     * 思路：建图，一个边一个边的建，每次加入新边的时候，先去建好的图里走一下，看看A点能不能走到B点
     * 如果会，说明当前的边就是一个多余的边，时间需要 n方，因为每次加入新边都要走走一遍图
     * 优化：顺着这个思路，其实很容易就能想到，找A点能不能走到B点其实就是在看 A和B是不是出自同一个root
     * 用并查集就可以很效率的解决这个问题，只要 Find(a) == Find(b) 就说明 ab这条边是多余的
     * 重点在于这个题，需要再find的时候，如果这个点不存在的话，要把它加入到map中
     * */

    public int[] findRedundantConnection(int[][] edges) {
        UnionFind uf = new UnionFind();
        for (int[] edge :edges) {
            if (uf.find(edge[0]) == uf.find(edge[1])) {
                return edge;
            }
            uf.union(edge[0], edge[1]);
        }
        return new int[] {0, 0};
    }
    class UnionFind {
        Map<Integer, Integer> parents = new HashMap<>();
        Map<Integer, Integer> ranking = new HashMap<>();
        public int find(int target) {
            if (!parents.containsKey(target)) {
                parents.put(target, target);
                ranking.put(target, 1);
            }
            while (target != parents.get(target)) {
                target = parents.get(target);
            }
            return target;
        }
        public void union(int a, int b) {
            int rootA = find(a);
            int rootB = find(b);
            int rankA = ranking.get(rootA);
            int rankB = ranking.get(rootB);
            if (rankA > rankB) {
                parents.put(rootB, rootA);
                ranking.put(rootA, rankA + rankB);
            } else {
                parents.put(rootA, rootB);
                ranking.put(rootB, rankA + rankB);
            }
        }
    }
}
```

#### 305. Number of Islands II 

```java
/**
 * 题意：给一个m乘n的board，初始全是0（水）和一个list的 xy坐标，依次在坐标位置上填1（陆地）
 * 陆地之间可以相连，要求打印出每次board上面有多少陆地
 * */
public class NumberofIslandsII0305 {
    /** time find O(1) union O(1) 一共进行了k次，positions的数量为k space O(n) 
     * 方法：并查集， 思路：首先需要把二维转成一维
     * 注意；这个题，要多一个add 函数，因为，不能简单的初始所有位置，
     * 因为这样，union的时候无法区别上下左右4个位置是否已经有陆地了，
     * 或者需要另外一个board来标记，但比较浪费空间，
     * 优化：
     * */
    public List<Integer> numIslands2(int m, int n, int[][] positions) {
        UnionFind uf = new UnionFind(m * n);
        List<Integer> res = new ArrayList<>();

        for (int[] position : positions) {
            int a = convertIndex(position[0], position[1], n);
            uf.add(a);
            if (position[0] - 1 >= 0) {
                uf.union(a, convertIndex(position[0] - 1, position[1], n));
            }
            if (position[0] + 1 < m) {
                uf.union(a, convertIndex(position[0] + 1, position[1], n));
            }
            if (position[1] - 1 >= 0) {
                uf.union(a, convertIndex(position[0], position[1] - 1, n));
            }
            if (position[1] + 1 < n) {
                uf.union(a, convertIndex(position[0], position[1] + 1, n));
            }
            res.add(uf.getCount());
        }
        return res;
    }
    private int convertIndex(int x, int y, int n) {
        return x * n + y;
    }
    class UnionFind {
        Map<Integer, Integer> parents;
        Map<Integer, Integer> ranking;
        int count;
        public UnionFind(int size) {
            parents = new HashMap<>(size);
            ranking = new HashMap<>(size);
        }
        public void add(int land) {
            if (parents.containsKey(land)) {
                return;
            }
            parents.put(land, land);
            ranking.put(land, 1);
            ++count;
        }
        public int find(int target) {
            while (target != parents.get(target)) {
                target = parents.get(target);
            }
            return target;
        }
        public void union(int a, int b) {
            if (!parents.containsKey(a) || !parents.containsKey(b)) {
                return;
            }
            int rootA = find(a);
            int rootB = find(b);
            if (rootA == rootB) {
                return;
            }
            --count;
            int rankA = ranking.get(rootA);
            int rankB = ranking.get(rootB);
            if (rankA > rankB) {
                parents.put(rootB, rootA);
                ranking.put(rootA, rankA + rankB);
            } else {
                parents.put(rootA, rootB);
                ranking.put(rootB, rankA + rankB);
            }
        }
        public int getCount() {
            return count;
        }
    }
}
```

### dfs更直观

#### 130. Surrounded Regions

```java
/**
 * 题意：给一个2D board，填充 O 和 X，如果一个区域被X环绕，那么把区域内的O变成X
 * For example,
 X X X X
 X O O X
 X X O X
 X O X X
 After running your function, the board should be:

 X X X X
 X X X X
 X X X X
 X O X X
 */
public class SurroundedRegions0130 {
    /** time O(N^2) space O(1) 方法：DFS
     * 思路：可以先从4边开始，把不能被包围的O全都先改成一个特舒符号比如'*'
     * 然后剩下的O肯定都可以被包围了，可以一股脑全改成X，最后把*改回O就可以了
     * 优化：
     * */
    public void solve(char[][] board) {
        if (board == null || board.length == 0 || board[0] == null || board[0].length == 0) {
            return;
        }
        for (int i = 0; i < board.length; ++i) {
            exploreFromEdges(board, i, 0);
            exploreFromEdges(board, i,board[0].length - 1);
        }
        for (int i = 0; i < board[0].length; ++i) {
            exploreFromEdges(board, 0, i);
            exploreFromEdges(board, board.length - 1, i);
        }
        for (int i = 0; i < board.length; ++i) {
            for (int j = 0; j < board[0].length; ++j) {
                if (board[i][j] == 'O') {
                    board[i][j] = 'X';
                }
                if (board[i][j] == '*') {
                    board[i][j] = 'O';
                }
            }
        }
    }
    private void exploreFromEdges(char[][] board, int row, int col) {
        if (row < 0 || row == board.length || col < 0 || col == board[0].length || board[row][col] != 'O') {
            return;
        }
        board[row][col] = '*';
        exploreFromEdges(board, row + 1, col);
        exploreFromEdges(board, row, col + 1);
        exploreFromEdges(board, row - 1, col);
        exploreFromEdges(board, row, col - 1);
    }
}

```

#### 200. Number of Islands

```java
/**
 *题意： 给一个2D board，有1 和 0 组成，1是陆地，0是水，假设board周围也都是水
 * 如果陆地之间可以相连，（不能斜线）那么这个board里面一共有几块陆地
 Example 1:

 11110
 11010
 11000
 00000
 Answer: 1

 Example 2:

 11000
 11000
 00100
 00011
 Answer: 3
 */
public class NumberofIslands0200 {
    /**  time O(n^2) 空间的话2种方案，一个是O(1)但改原数组，一个是O(n^2)
     * 思路就是 从每个1开始向4个方向dfs，dfs过程中碰见任何1，都标记成x，这样每个岛就只会记录一次1，
     * 之后再把'x'变回来就行， 或者就用一个 n^2的布尔数组标记visited
     * */
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0 || grid[0] == null || grid[0].length == 0) {
            return 0;
        }
        int row = grid.length;
        int col = grid[0].length;
        int count = 0;
        for (int i = 0; i < row; ++i) {
            for (int j = 0; j < col; ++j) {
                if (grid[i][j] == '1') {
                    explore(grid, i, j);
                    ++count;
                }
                if (grid[i][j] == 'x') {
                    grid[i][j] = '1';
                }
            }
        }
        return count;
    }
    private void explore(char[][] grid, int row, int col) {
        if (row < 0 || row >= grid.length || col < 0 || col >= grid[0].length || grid[row][col] != '1') {
            return;
        }
        grid[row][col] = 'x';
        explore(grid, row + 1, col);
        explore(grid, row - 1, col);
        explore(grid, row, col+ 1);
        explore(grid, row, col - 1);
    }
}

```

### bfs

#### 310. Minimum Height Trees

```java
/**
 * 题意：给一个无线图，具有树的特性，有n个节点0到n-1 问在任何节点都可以认为是root的情况下，
 * 以哪个节点当root的树的高度最低，如果有几个这样的节点，都要打印出来
 * */
public class MinimumHeightTrees0310 {
    /** time O(v + e) space O(v + e) 方法：
     * 思路：建图之后，依次删除最外面的叶子节点，知道节点剩下小于3个的时候停止。所以说，
     * 这个最低高度的节点，最多也就只能有2个，
     * 优化：
     * */
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        if (n == 1) {
            return Collections.singletonList(0);
        }
        List<Set<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; ++i) {
            graph.add(new HashSet<>());
        }
        for (int[] edge : edges) {
            graph.get(edge[0]).add(edge[1]);
            graph.get(edge[1]).add(edge[0]);
        }
        List<Integer> leaves = new ArrayList<>();
        for (int i = 0; i < n; ++i) {
            if (graph.get(i).size() == 1) {
                leaves.add(i);
            }
        }
        while (n > 2) {
            n -= leaves.size();
            List<Integer> newLeaves = new ArrayList<>();
            for (int leave : leaves) {
                int curr = graph.get(leave).iterator().next();
                graph.get(curr).remove(leave);
                if (graph.get(curr).size() == 1) {
                    newLeaves.add(curr);
                }
            }
            leaves = newLeaves;
        }
        return leaves;
    }
}

```

### 有向图

#### 332. Reconstruct Itinerary

![lc332](ReconstructItinerary.jpg)
```java
/**
 * 题意: 给一个list的机票，用一对String 数组来表示，【出发，到达】 而且起始必须从jfk开始
 * 所有的机票必须都要用，而且，肯定至少会有一个合法的组合，如果有多个，那么返回字典序最小的
 * */
public class ReconstructItinerary0332 {
    /** time O(n + klogk) space O(n) 方法：贪心 + dfs + 优先队列 + 后序遍历
     * 思路：如果假设肯定有一个合法组合，那么其实可以想到贪心，有点和加油站的题微微类似
     * 比如这种情况， [[JFK, ABC],[JFK, XYZ],[XYZ, JFK]] 如果按字典序，那么会先走 jfk-abc
     * 但abc并不是一个起始点，不在map里面，所以不会被选到
     * 所以，这就需要再选择到达地的时候，要先判断下，是否还有以这个到达地出发的机票，如果没有就不能选
     * 优化：
     * */
    public List<String> findItinerary(String[][] tickets) {
        Map<String, Queue<String>> graph = new HashMap<>();
        List<String> path = new LinkedList<>();
        for (String[] ticket : tickets) {
            if (!graph.containsKey(ticket[0])) {
                graph.put(ticket[0], new PriorityQueue<>());
            }
            graph.get(ticket[0]).add(ticket[1]);
        }
        postDFS("JFK", graph, path);
        return path;
    }
    private void postDFS(String city, Map<String, Queue<String>> graph, List<String> path) {
        while (graph.containsKey(city) && !graph.get(city).isEmpty()) {
            postDFS(graph.get(city).poll(), graph, path);
        }
        path.add(0, city);
    }
}
```
### 克隆图

#### 138. Copy List with Random Pointer

```java
/**
 * 题意：克隆一个list，这个list除了有next节点，还有一个random节点，可以指向任何其他节点*/
public class CopyListwithRandomPointer0138 {
    /** time O(n) space O(n) 方法：hashmap
     * 思路：首先先克隆所有节点，用map建立新旧节点的一一映射，然后，再次遍历，克隆节点之间的关系
     * follow up：不用hashmap怎么做
     * */
    public RandomListNode copyRandomList(RandomListNode head) {
        if (head == null) {
            return null;
        }
        Map<RandomListNode, RandomListNode> map = new HashMap<>();
        for (RandomListNode root = head; root != null; root = root.next) {
            RandomListNode clone = new RandomListNode(root.label);
            map.put(root, clone);
        }
        for (RandomListNode root = head; root != null; root = root.next) {
            map.get(root).next = map.get(root.next);
            map.get(root).random = map.get(root.random);
        }
        return map.get(head);
    }
}
```

#### 133. Clone Graph

```java
/**
 * 题意：克隆一个无向图，图的每个节点包括一个label和一个list of neighbors
 *        1
         / \
        /   \
       0 --- 2
            / \
            \_/
 * */
public class CloneGraph0133 {
    /** time O(n) space O(n) 方法：dfs + hashmap 或者 bfs + queue + hashmap
     * 思路：克隆图的题，原则就是先遍历克隆每个节点，每次遇到一个新的节点，用queue来检查
     * 然后遍历该节点的邻居，当邻居不在map里面，那么就把邻居加到queue里面，同时做map映射
     * 但无论邻居在不在map里面，都应该把这个邻居和克隆节点的关系 复制
     * 优化：bfs和dfs都需要会做
     * */
    public UndirectedGraphNode cloneGraph(UndirectedGraphNode node) {
        if (node == null) {
            return null;
        }
        Map<UndirectedGraphNode, UndirectedGraphNode> map = new HashMap<>();
        map.put(node, new UndirectedGraphNode(node.label));
        Deque<UndirectedGraphNode> queue = new ArrayDeque<>();
        queue.offer(node);
        while (!queue.isEmpty()) {
            UndirectedGraphNode head = queue.poll();
            for (UndirectedGraphNode neighbor: head.neighbors) {
                if (!map.containsKey(neighbor)) {
                    queue.offer(neighbor);
                    map.put(neighbor, new UndirectedGraphNode(neighbor.label));
                }
                map.get(head).neighbors.add(map.get(neighbor));
            }
        }
        return map.get(node);
    }
    class UndirectedGraphNode {
        int label;
        List<UndirectedGraphNode> neighbors;
        UndirectedGraphNode(int x) { label = x; neighbors = new ArrayList<UndirectedGraphNode>(); }
    }
}
    /** dfs solution
    public UndirectedGraphNode cloneGraph(UndirectedGraphNode node) {
        if (node == null) {
            return node;
        }
        Map<UndirectedGraphNode, UndirectedGraphNode> map = new HashMap<>();
        UndirectedGraphNode root = new UndirectedGraphNode(node.label);
        map.put(node, root);
        buildMap(node, map);
        return root;
    }
    private void buildMap(UndirectedGraphNode node, Map<UndirectedGraphNode, UndirectedGraphNode> map) {
        for (UndirectedGraphNode n : node.neighbors) {
            if (!map.containsKey(n)) {
                UndirectedGraphNode newNode = new UndirectedGraphNode(n.label);
                map.put(n, newNode);
                buildMap(n, map);
            }
            map.get(node).neighbors.add(map.get(n));
        }
    }
     */

```

### 杂题 图遍历

#### 785. Is Graph Bipartite?
```java
/**
 * 题意：给一组边，组成一个 无向图，求，可不可把这个图里的定点分成2部分，
 * 然后单独每个部分里面的点，都不能是已有的一条边，
 * 也可以说，任何一条已有的边都必须需要从2个部分里各拿一个定点
 * */
public class IsGraphBipartite0785 {
    /** time O(n方) space O(n) 方法：dfs
     * 思路：建图之后，遍历图，意思就是相邻的2个顶点肯定不能在同一组里面，
     * 所以，可以把当前顶点标记成1，然后这当前顶点能到所有其他顶点都标记成2
     * 这样，如果未来有所谓的其他顶点和将要标记的数字不一直，说明错了
     * 优化：这是一道自己做出来了，但有点不知道为啥的题。。。。
     * */
    public boolean isBipartite(int[][] graph) {
        int[] colors = new int[graph.length];
        for (int i = 0; i < colors.length; ++i) {
            if (colors[i] == 0) {
                colors[i] = 1;
                if(!explore(graph, i, 1, colors)) {
                    return false;
                }
            }
        }
        return true;
    }
    private boolean explore(int[][] graph, int index, int color, int[] colors) {
        for (int v : graph[index]) {
            if (colors[v] == 0) {
                colors[v] = -color;
                if (!explore(graph, v, -color, colors)) {
                    return false;
                }
            }
            if (colors[v] == color) {
                return false;
            }
        }
        return true;
    }
}

```