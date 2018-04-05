---
title: 二维矩阵
date: 2018-03-08 15:55:14
tags: [Matrix]
categories: [Algorithm]
---

### 二分查超
#### 302. Smallest Rectangle Enclosing Black Pixels
```java
/**
 * 题意：一个由二进制矩阵表示的图，0表示白色像素点，1表示黑色像素点。黑色像素是联通的，即只有一块
 * 像素是水平和竖直连接的，给一个黑色像素点的坐标 (x, y) ，返回囊括所有黑色像素点的矩阵的最小面积。
 * */
public class SmallestRectangleEnclosingBlackPixels0302 {
    /** timetime O(nlogm + mlogn) 方法：dfs 最优方法 二分
     * 思路：4个方向dfs，用一个tuple类记录最上，下，左，右的极限值
     * 同时把访问过的1标记为2，（最后可以在换回去，或者新建一个2D布尔数组）
     * 优化：用二分来找4个边界，可以在继续优化 加flag 来复用函数 
     * */
    public int minArea(char[][] img, int x, int y) {
        if (img == null || img.length == 0 || img[0] == null || img[0].length == 0) {
            return 0;
        }
        return (mostDowm(img, x) - mostUp(img, x) + 1) *
                (mostRight(img, y) - mostLeft(img, y) + 1);
    }
    private int mostUp(char[][] image, int right) {
        int left = 0;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (checkRow(image, mid)) {
                right = mid;
            } else {
                left = mid;
            }
        }
        return checkRow(image, left) ? left : right;
    }
    private int mostDowm(char[][] image, int left) {
        int right = image.length - 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (checkRow(image, mid)) {
                left = mid;
            } else {
                right = mid;
            }
        }
        return checkRow(image, right) ? right : left;
    }
    private int mostLeft(char[][] image, int right) {
        int left = 0;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (checkCol(image, mid)) {
                right = mid;
            } else {
                left = mid;
            }
        }
        return checkCol(image, left) ? left : right;
    }
    private int mostRight(char[][] image, int left) {
        int right = image[0].length - 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
            if (checkCol(image, mid)) {
                left = mid;
            } else {
                right = mid;
            }
        }
        return checkCol(image, right) ? right : left;
    }
    private boolean checkRow(char[][] image, int row) {
        for (int i = 0; i < image[0].length; ++i) {
            if (image[row][i] == '1') {
                return true;
            }
        }
        return false;
    }
    private boolean checkCol(char[][] image, int col) {
        for (int i = 0; i < image.length; ++i) {
            if (image[i][col] == '1') {
                return true;
            }
        }
        return false;
    }
}

```
### 最短路径
#### 317. Shortest Distance from All Buildings
```java
/**
 * 题意：给一个二维矩阵，0代表空地，1代表房子，2代表障碍，求一个空地到所有楼最短距离
 * */
public class ShortestDistancefromAllBuildings0317 {
    /** time O(kmn) k为building的数量 space O(mn) 方法：最短路径肯定用bfs
     * 思路：从每个building开始4个方向的dfs，空地都update成到building的距离，
     * 另在helper函数里面用一个visited布尔数组来标记每个元素的访问状态，
     * dfs完所有的楼之后，数字最小的空地就是答案，这里需要注意的一种情况就是，有些空地并不能到达所有空地
     * 所以要用一个helper数组记录connected，当前空地是不是和所有的building都相连，在每次遍历到空地的时候，
     * connected 加1，最后找结果的时候，要看当前元素是否为空地，distance是否大于0，以及是否可以连通所有楼
     * 优化：可以在递归函数中检查，从当前building是否能连通所有其他的building，如果不能直接返回-1
     * */
    public int shortestDistance(int[][] grid) {
        int[][] distance = new int[grid.length][grid[0].length];
        int[][] connected = new int[grid.length][grid[0].length];
        int buildings = 0;
        for (int i = 0; i < grid.length; ++i) {
            for (int j = 0; j < grid[0].length; ++j) {
                if (grid[i][j] == 1) {
                    ++buildings;
                }
            }
        }
        for (int i = 0; i < grid.length; ++i) {
            for (int j = 0; j < grid[0].length; ++j) {
                if (grid[i][j] == 1) {
                    if (!bfs(grid, distance, connected, i, j, buildings)) {
                        return -1;
                    }
                }
            }
        }
        int min = Integer.MAX_VALUE;
        for (int i = 0; i < grid.length; ++i) {
            for (int j = 0; j < grid[0].length; ++j) {
                if (grid[i][j] == 0 && distance[i][j] > 0 && connected[i][j] == buildings) {
                    min = Math.min(min, distance[i][j]);
                }
            }
        }
        return min == Integer.MAX_VALUE ? -1 : min;
    }
    private boolean bfs(int[][] grid, int[][] distance, int[][] connected, int x, int y, int buildings) {
        boolean[][] visited = new boolean[grid.length][grid[0].length];
        int[][] directions = new int[][] {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        int dis = 0;
        int findBuildings = 1;
        visited[x][y] = true;
        Deque<int[]> queue = new ArrayDeque<>();
        queue.offer(new int[] {x, y});
        while (!queue.isEmpty()) {
            ++dis;
            int size = queue.size();
            while (--size >= 0) {
                int[] curr = queue.poll();
                for (int[] direction : directions) {
                    int x1 = curr[0] + direction[0];
                    int y1 = curr[1] + direction[1];
                    if(x1 < 0 || x1 == grid.length || y1 < 0 || y1 == grid[0].length){
                        continue;
                    }
                    if (!visited[x1][y1] && grid[x1][y1] == 1) {
                        ++findBuildings;
                    }
                    if (!visited[x1][y1] && grid[x1][y1] == 0) {
                        distance[x1][y1] += dis;
                        ++connected[x1][y1];
                        queue.offer(new int[] {x1, y1});
                    }
                    visited[x1][y1] = true;
                }
            }
        }
        return findBuildings == buildings;
    }
}
```

#### 286. Walls and Gates

```java

/**
 * 题意：给一个二维矩阵，由-1，0， INF 组成，-1代表障碍，0代表门，INF代表空房
 * */
public class WallsandGates0286 {
    /** time O(mn) space O() 方法：从每个gate开始bfs
     * 思路：依次从每个gate开始做4个方向的bfs，先检查越界问题，然后检查，当前position是否是障碍或门，
     * 以及是否已经有比现在更短的距离。换成代码就是查看[x][y] 是否大于0，以及是否大于[i][j] + 1，
     * 如果都满足条件，就更新[x][y]，然后继续bfs
     * 优化：
     * */
    public void wallsAndGates(int[][] rooms) {
        if (rooms == null || rooms.length == 0 || rooms[0] == null || rooms[0].length == 0) {
            return;
        }
        int[][] directions = new int[][] {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        for (int i = 0; i < rooms.length; ++i) {
            for (int j = 0; j < rooms[0].length; ++j) {
                if (rooms[i][j] == 0) {
                    bfs(directions, rooms, i, j);
                }
            }
        }
    }
    private void bfs(int[][] directions, int[][] rooms, int i, int j) {
        for (int[] direction : directions) {
            int x = i + direction[0];
            int y = j + direction[1];
            if (isValid(rooms, x, y) && rooms[x][y] > rooms[i][j] + 1) {
                rooms[x][y] = rooms[i][j] + 1;
                bfs(directions, rooms, x, y);
            }
        }
    }
    private boolean isValid(int[][] rooms, int x, int y) {
        return x >= 0 && x < rooms.length && y >= 0 && y < rooms[0].length;
    }
}

```
#### 296. Best Meeting Point
```java
/**
 * 题意：给一个二维矩阵，1代表某人的家，0代表没有房子，求在图中找到任何一个点，
 * 离所有人家的距离最近
 * */
public class BestMeetingPoint0296 {
    /** time O(mn) space O(m+n) 方法：
     * 思路：原则上，2点之间的最短距离，就是2点之间的任意点，3点之间的最短距离，肯定是中间的点
     * 而这个题，可以先假设，所有点都是同一排，或者同一列，最后加起来就是答案
     * 优化：
     * */
    public int minTotalDistance(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0] == null || grid[0].length == 0) {
            return 0;
        }
        List<Integer> rows = new ArrayList<>();
        List<Integer> cols = new ArrayList<>();
        for (int i = 0; i < grid.length; ++i) {
            for (int j = 0; j < grid[0].length; ++j) {
                if (grid[i][j] == 1) {
                    rows.add(i);
                }
            }
        }
        for (int i = 0; i < grid[0].length; ++i) {
            for (int j = 0; j < grid.length; ++j) {
                if (grid[j][i] == 1) {
                    cols.add(i);
                }
            }
        }
        return getDistance(rows) + getDistance(cols);
    }
    private int getDistance(List<Integer> list) {
        if (list.size() < 2) {
            return 0;
        }
        int distance = 0;
        int mid = list.get(list.size() / 2);
        for (int l : list) {
            distance += Math.abs(l - mid);
        }
        return distance;
    }
}
```
#### flood

```java
import java.util.ArrayList;
import java.util.List;

/**
 * 题意：给一个m x n 二维矩阵，有非负整数组成，假设矩阵上面和左边被太平洋包围，
 * 下面有右边被大西洋包围，求哪些cell的水既可以流向太平洋，又可以流向大西洋
 * */
public class PacificAtlanticWaterFlow0417 {
    /** time O((m+n)*(m+n)) space O(m*n)，dfs函数的复杂度应该是m+n，然后主函数的两个for loop一个是m一个是n
     * 因此，复杂度应该是 (m+n)*(m+n)， 方法就是 dfs
     * 思路：对于每个海洋分别做dfs，比如从太平洋的2条边开始，如果遇到的边大于等于自己，说明水可以留过来，
     * 用2组 二维布尔数组来记录，最后查看，如果2个海洋的某个点，都是true，说明它可以流向2个海洋
     * 优化：
     * */
    public List<int[]> pacificAtlantic(int[][] matrix) {
        List<int[]> res = new ArrayList<>();
        if (matrix == null || matrix.length == 0 || matrix[0] == null || matrix[0].length == 0) {
            return res;
        }
        int[][] directions = new int[][] {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        boolean[][] pacific = new boolean[matrix.length][matrix[0].length];
        boolean[][] atlantic = new boolean[matrix.length][matrix[0].length];
        for (int i = 0; i < matrix.length; ++i) {
            dfs(directions, pacific, matrix, i, 0);
            dfs(directions, atlantic, matrix, i, matrix[0].length - 1);
        }
        for (int i = 0; i < matrix[0].length; ++i) {
            dfs(directions, pacific, matrix, 0, i);
            dfs(directions, atlantic, matrix, matrix.length - 1, i);
        }
        for (int i = 0; i < matrix.length; ++i) {
            for (int j = 0; j < matrix[0].length; ++j) {
                if (pacific[i][j] && atlantic[i][j]) {
                    res.add(new int[] {i, j});
                }
            }
        }
        return res;
    }
    private void dfs(int[][] directions,  boolean[][] visited, int[][] matrix, int i, int j) {
        visited[i][j] = true;
        for (int[] direction : directions) {
            int x = i + direction[0];
            int y = j + direction[1];
            if (x >= 0 && x < matrix.length && y >= 0 && y < matrix[0].length &&
                    !visited[x][y] && matrix[x][y] >= matrix[i][j]) {
                dfs(directions, visited, matrix, x, y);
            }
        }
    }
}

```