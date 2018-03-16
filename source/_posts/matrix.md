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