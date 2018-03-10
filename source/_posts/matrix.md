---
title: 二维矩阵
date: 2018-03-08 15:55:14
tags: [Matrix]
categories: [Algorithm]
---

### 二分查超
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