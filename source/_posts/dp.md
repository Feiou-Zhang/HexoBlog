---
title: 动态规划
date: 2018-02-13 03:49:54
tags: [dp]
categories: [Algorithm]
---

### House Robber 系列

#### 198. House Robber

```java
/**
 * 题意：一个robber要抢劫一条街的房子，但不能抢劫连续的2个房子，不然会触动警报
 * 求可以抢到的最大者
 * */
public class HouseRobber0198 {
    /** time O(n) space O(1) 方法：dp
     * 思路：遍历数组，用2个状态 rob 和notrob 来表示当前的最好结果
     * 如果抢当前的，那么 最好结果肯定就是 当前value + 上一次不抢的最好结果
     * 如果不抢当前的，那么 最好的结果就是上次抢和不抢中更好的结果
     * 优化：就是空间从n到1的优化，但其实也可以直接用1
     * */
    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int rob = nums[0];
        int notRob = 0;
        for (int i = 1; i < nums.length; ++i) {
            int tmp = rob;
            rob = nums[i] + notRob;
            notRob = Math.max(notRob, tmp);
        }
        return Math.max(rob, notRob);
    }
}

```
#### House Robber II
```java
/**
 * 题意：follow up，如果这条街的房子，收尾相连，那么最大值又是多少
 * */
public class HouseRobberII0213 {
    /** time O(n) space O(1) 方法：dp
     * 思路：那么其实问题就可以转化为，从1到n-1，以及从2到n 这2个的最大值
     * 注意，最好特殊处理一下 只有一个房子的情况，不然递归里面还需要检查start越界的问题
     * 优化：
     * */
    public int rob(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        if (nums.length == 1) {
            return nums[0];
        }
        return Math.max(robHelper(nums, 0, nums.length - 1), robHelper(nums, 1, nums.length));
    }
    private int robHelper(int[] nums, int start, int end) {
        int rob = nums[start];
        int notRob = 0;
        for (int i = start + 1; i < end; ++i) {
            int tmp = rob;
            rob = nums[i] + notRob;
            notRob = Math.max(notRob, tmp);
        }
        return Math.max(rob, notRob);
    }
}

```
#### House Robber III

```java
/**
 * 题意：follow up，这次这条街类似二叉树的形状，要求同一条path的房子，不能连续被抢
 * */
public class HouseRobberIII0337 {
    /** time O(n) space O(n) 方法：后序遍历 + dp
     * 思路：后序遍历树，更新抢和不抢2个状态的最好结果
     * 递归返回一个数组，index 0表示不抢，1表示抢
     * 优化：
     * */
    public int rob(TreeNode root) {
        int[] res = postTraveral(root);
        return Math.max(res[0], res[1]);
    }
    private int[] postTraveral(TreeNode node) {
        if (node == null) {
            return new int[] {0, 0};
        }
        int[] left = postTraveral(node.left);
        int[] right = postTraveral(node.right);
        int notRob = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);
        int rob = node.val + left[0] + right[0];
        return new int[] {notRob, rob};
    }
}

```

### 股票系列

#### 121. Best Time to Buy and Sell Stock

```java
/**
 * 题意：给一个数组，元素表示每天的股票价格，问在只买卖一次的情况下，最大收益
 * */
public class BestTimetoBuyandSellStock0121 {
    /** time O(n) space O(1) 方法：dp
     * 思路：记录2个状态，买入价格，和最大收益，当发现更小值得时候，更新买入价格
     * 然后不断更新最大收益
     * 优化：
     * */
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length < 2) {
            return 0;
        }
        int maxProfit = 0;
        int buy = prices[0];
        for (int i = 1; i < prices.length; ++i) {
            maxProfit = Math.max(maxProfit, prices[i] - buy);
            buy = Math.min(prices[i], buy);
        }
        return maxProfit;
    }
}

```
#### 122. Best Time to Buy and Sell Stock II

```java
/**
 * follow up: 如果可以交易若干次，但同一天只能买卖一次的情况下，求最大收益*/
public class BestTimetoBuyandSellStockII0122 {
    /** time O(n) space O(1) 方法：dp 贪心
     * 思路2：记录2个状态，买和卖
     * 卖的状态 就是上次买完后的钱 加上当前股票的价格
     * 买的状态 就是上次卖完后的钱 减去当前股票的价格
     * 优化：
     * */
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length < 2) {
            return 0;
        }
        int hold = Integer.MIN_VALUE;
        int sell = 0;
        for (int price : prices) {
            sell = Math.max(sell, hold + price);
            hold = Math.max(hold, sell - price);
        }
        return sell;
    }
    public int maxProfit1(int[] prices) {
        /**
         * 思路1：记录2个状态，买入价格，卖出价格，和总收益，
         * 只要当前价格比上一个小，之前的就应该卖出，然后再用当前价格买入
         * 如果当前价格比上一个大的话，那么可以继续持有(也就是什么都不用做)
         * 注意：如果最后一部分 一直是一个上升趋势的话，单纯返回totalprofit会miss一部分收益
         * 所以，应该返回totalprofit + prices[prices.length - 1] - prices[buy];*/
        if (prices == null || prices.length < 2) {
            return 0;
        }
        int totalProfit = 0;
        int buy = 0;
        for (int sell = 1; sell < prices.length; ++sell) {
            if (prices[sell] < prices[sell - 1]) {
                totalProfit += prices[sell - 1] - prices[buy];
                buy = sell;
            }
        }
        return totalProfit + prices[prices.length - 1] - prices[buy];
    }
}


```

#### 123. Best Time to Buy and Sell Stock III

```java
/**
 * follow up: 如果最多可以交易2次，求最大收益
 * */
public class BestTimetoBuyandSellStockIII0123 {
    /** time O(n) space O(1) 方法：dp
     * 错误思路，并不能挑top2的最大收益加起来，
     * 正确做法有2种思路
     * 思路1：可以把数组分为不重叠的2部分，然后每部分求最大收益
     * 思路2：更好更通用的方式：记录4个状态，sell2， hold2， sell1， hold1
     * sell2的最大收益，就是hold2 +当前股票价格，以及上一次sell2 中的最大值
     * hold2的最好结果，就是上一次sell1之后的钱减去当前股票的价格，和上一次hold2中最大值
     * sell1的最大收益，就是hold1加当前股票价格，以及上一次sell1，2者中最大值
     * hold1的最好结果就是买入当前股票的价格，和上一次hold1，2者中最大值
     * 优化：思路1需要n的空间复杂度
     * */
    public int maxProfit1(int[] prices) {
        if (prices == null || prices.length < 2) {
            return 0;
        }
        int sell2 = 0;
        int sell1 = 0;
        int hold2 = Integer.MIN_VALUE;
        int hold1 = Integer.MIN_VALUE;
        for (int price : prices) {
            sell2 = Math.max(sell2, hold2 + price);
            hold2 = Math.max(hold2, sell1 - price);
            sell1 = Math.max(sell1, hold1 + price);
            hold1 = Math.max(hold1, 0 - price);
        }
        return sell2;
    }

    public int maxProfit2(int[] prices) {
        if (prices == null || prices.length < 2) {
            return 0;
        }
        int n = prices.length;
        int[] first = new int[n];
        int[] second = new int[n];
        for (int buy = prices[0], sell = 1; sell < n; ++sell) {
            first[sell] = Math.max(first[sell - 1], prices[sell] - buy);
            buy = Math.min(buy, prices[sell]);
        }
        for (int buy = n - 2, sell = prices[n - 1]; buy >= 0; --buy) {
            second[buy] = Math.max(second[buy + 1], sell - prices[buy]);
            sell = Math.max(sell, prices[buy]);
        }
        int maxProfit = second[0];
        for (int i = 0; i < n - 1; ++i) {
            maxProfit = Math.max(maxProfit, first[i] + second[i + 1]);
        }
        return maxProfit;
    }
}

```

#### 188. Best Time to Buy and Sell Stock IV
```java
/**
 * follow up: 如果最多可以k次交易，同样，同一天只能有一个操作，买或卖,那么求最大收益*/
public class BestTimetoBuyandSellStockIV0188 {
    /** time O(n * k) space O(k) 方法：dp 状态
     * 思路：延续上一题，那么这个就简单了，无非就是维护k个状态，用一个k大小的数组来记录
     * 注意：创建买卖状态数组的时候，要初始化size为k+1， hold初始为 int_min
     * 原因是，我留状态[0]当做初始状态，也就是无交易状态的时候，也就是有0资金的时候
     * 而且，无论如何要循环k次，所以，初始要 k+1， 然后skip 当index 0的时候
     * 优化：小优化，如果k大于等于 二分之n，那么这个题就相当于可以无限次交易，
     * */
    public int maxProfit(int k, int[] prices) {
        if (prices == null || prices.length < 2 || k < 1) {
            return 0;
        }
        if (k >= prices.length / 2) {
            return maxProfitForUnlimitTimes(prices);
        }
        int[] sell = new int[k + 1];
        int[] hold = new int[k + 1];
        Arrays.fill(hold, Integer.MIN_VALUE);
        for (int price : prices) {
            for (int i = k; i > 0; --i){
                sell[i] = Math.max(sell[i], hold[i] + price);
                hold[i] = Math.max(hold[i], sell[i - 1] - price);
            }
        }
        return sell[k];

    }
    private int maxProfitForUnlimitTimes(int[] prices) {
        int hold = Integer.MIN_VALUE;
        int sell = 0;
        for (int price : prices) {
            sell = Math.max(sell, hold + price);
            hold = Math.max(hold, sell - price);
        }
        return sell;
    }
}
```
#### 309. Best Time to Buy and Sell Stock with Cooldown

```java
/**
 * follow up: 可以多次交易，还是不能再同一天，而且多了一个条件
 * 卖完之后，第二天不能有交易操作，也就是不能买
 * */
public class BestTimetoBuyandSellStockwithCooldown0309 {
    /** time O(n) space O(1) 方法：还是dp，状态机
     * 思路：维护3个状态，买，卖，休息。 ** 最大收益将会是 卖和休息中更大的一个
     * 重点：注意：
     * 卖完之后，必须休息，所有休息的状态，手上是没有股票的，如果手上有钱，就是赚的钱
     *
     * 那么买股票的时候，必须是从休息的状态来的，所以，买这个状态的的最大值就是
     * 上次买股票后的总额，以及，从休息状态带来的钱减去当前股票总额，二者最大值
     *
     * 那么卖股票的时候，就必须是买完这个状态来的，因为休息的时候，手里是没有股票的
     * 所以，卖这个状态的最大值就是 买的状态 加上当前股票的钱。
     * 注意。这里是不用和上次卖的状态比的，因为，之后休息的状态会比较，
     * 所以这里比较也是重复，
     *
     * 那么休息这个状态，肯定是之前卖完 或者是之前也是休息的状态过来的。
     * 那么休息状态的最大值，就是如果今天不交易，上次卖完之后的总额，和上次休息的余额
     * 二者最大值，
     *
     * 还有一个问题就是 是什么顺序来更新这3个状态呢？这个可以细想想，
     * 我目前觉得还是比较晕的，但就这道题而言，只要休息在最后面，买卖的顺序无所谓
     * 优化：空间O(N) 到 1 的优化
     * */
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length < 2) {
            return 0;
        }
        int sell = 0;
        int rest = 0;
        int hold = Integer.MIN_VALUE;
        for (int price : prices) {
            int lastSell = sell;
            sell = hold + price;
            hold = Math.max(rest - price, hold);
            rest = Math.max(lastSell, rest);
        }
        return Math.max(sell, rest);
    }
}

```
#### 714. Best Time to Buy and Sell Stock with Transaction Fee
```java
/**
 * follow up:可以多次交易，不在同一天，但每次交易（买加卖），有个费用，
 * */
public class BestTimetoBuyandSellStockwithTransactionFee0714 {
    /** time O() space O() 方法：dp
     * 思路：维护2个状态，买和卖，但卖的时候，要减去一个交易费
     * 注意，如果再买的时候减去交易费的话，如果交易费大于第一支股票价格话。
     * 就会overflow，所以需要把 买卖的数组类型改成long
     * 或者，这道题里面，因为股票价格和手续费，都不会超过50000，
     * 那么就可以在买的时候减去手续费。
     * 优化：
     * */
    public int maxProfit(int[] prices, int fee) {
        if (prices == null || prices.length < 2) {
            return 0;
        }
        long hold = Integer.MIN_VALUE;
        long sell = 0;
        for (int price : prices) {
            sell = Math.max(sell, hold + price - fee);
            hold = Math.max(hold, sell - price);
        }
        return (int)sell;
    }
    public int maxProfit2(int[] prices, int fee) {
        if (prices == null || prices.length < 2) {
            return 0;
        }
        int hold = Integer.MIN_VALUE;
        int sell = 0;
        for (int price : prices) {
            sell = Math.max(sell, hold + price);
            hold = Math.max(hold, sell - price - fee);
        }
        return sell;
    }
}

```
### 区间类

#### 315. Burst Balloons
```java
/**
 * 题意：有一些气球，每个气球代表一定的分数，你的得分规则是，扎破的气球乘以左右2边气球的分数
 * 求最大分数，假设最左最右2边界的分数是1
 * Given [3, 1, 5, 8]

 Return 167

 nums = [3,1,5,8] --> [3,5,8] -->   [3,8]   -->  [8]  --> []
 coins =  3*1*5      +  3*5*8    +  1*3*8      + 1*8*1   = 167
 * */
public class BurstBalloons0315 {
    /** time O(n^2) space O(n^2) 有n方个子问题，方法：首先这个题是用dp
     * 思路：首先可以先把0都删掉，因为0渣0不会有任何分数，所以，放到后面肯定分低
     * 其次，首先可以重新构建一下input数组，加入收尾的1分，顺便删0
     * 然后，建一个二维dp数组，逻辑是要用3个循环来做。
     * 第一个循环，先拆分成子问题，把整个数组拆成若干个len=2，len=3...len = n-1的subarray
     * 然后，第二个循环来标记 子数组的 2个边界，left 和right，这里用exclusive
     * 第三个循环，i，是从left + 1 到right -1 这个范围循环，它的意思就是，如果i是最后一个被扎破
     * 那么当前dp[left][right]的最好结果是怎样，
     * 转移方程就是 dp[left][right] = max(dp[l][r], c[l]*c[i]*c[r]+dp[l][i]+dp[i][r]
     * 优化：小优化就可以是删零，大优化就是 记忆化dp（还不会目前 03/01/2018)
     * */
    public int maxCoins(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int[] coins = new int[nums.length + 2];
        int n = 1;
        for (int num : nums) {
            if (num != 0) {
                coins[n++] = num;
            }
        }
        coins[0] = coins[n++] = 1;
        int[][] dp = new int[n][n];
        for (int len = 2; len < n; ++len) {
            for (int left = 0; left < n - len; ++left) {
                int right = left + len;
                for (int k = left + 1; k < right; ++k) {
                    dp[left][right] = Math.max(dp[left][right],
                            coins[left] * coins[k] * coins[right] + dp[left][k] + dp[k][right]);
                }
            }
        }
        return dp[0][n - 1];
    }
}

```
### 双序列

```java
/**
 * 题意：看 string pattern和stirng s 是否等价
 * pattern 中 有。和*， 。可以代表任何单个字母
 * *可以代表任意或者0个之前的字母 比如 a* 可以代表n个a 或者空
 * */
public class RegularExpressionMatching0010 {
    /** time O(n方) space O(n方) 方法：dp
     * 思路：首先要排除几种情况，比如，*不能再第一位，*和*不能相连。没意义
     * 其次，就是初始化的时候，要注意，a*b* 对应s为空的时候应该是 true
     * 转移方程：p是. 或者p，s字符相同 就看左上角，相当于，假设没有这两个字符的情况
     * 如果是p是*，那么就有2种情况，1，有0个*前面的字母，2.有n个*前面的字母
     * 有0个的话，那么就看dp[i][j-2], 如果有n个话，要先看*前面的字母和当前s是否一样
     * 如果一样，就看dp[i-1][j]
     *                   0个*前面字母              那个*前面字母
         a . c * b           a a *                   b a c *
       T F F F F F         T F F F                 T F F F F
     a F T F F F F       a F T F T               b F T F F F
     a F F T F F F                               a F F T F F
     c F F F T T F                               c F F F T T
     c F F F F T F                               c F F F F T
     b F F F F F T                               c F F F F T
     * 优化：
     * */
    public boolean isMatch(String s, String p) {
        if (s == null || p == null) {
            return false;
        }
        int slen = s.length();
        int plen = p.length();
        if (slen == 0 && plen == 0) {
            return true;
        }
        if (!isValid(p)) {
            return false;
        }
        boolean[][] isMatch = new boolean[slen + 1][plen + 1];
        isMatch[0][0] = true;
        for (int i = 2; i <= plen && p.charAt(i - 1) == '*'; i += 2) {
            isMatch[0][i] = true;
        }
        for (int i = 1; i <= slen; ++i) {
            for (int j = 1; j <= plen; ++j) {
                if (p.charAt(j - 1) == '.' || p.charAt(j - 1) == s.charAt(i - 1)) {
                    isMatch[i][j] = isMatch[i - 1][j - 1];
                }
                if (p.charAt(j - 1) == '*') {
                    isMatch[i][j] = isMatch[i][j - 2] || isMatch[i - 1][j] &&
                            ((p.charAt(j - 2) == '.' || p.charAt(j - 2) == s.charAt(i - 1)));
                }
            }
        }
        return isMatch[slen][plen];
    }
    private boolean isValid(String p) {
        if (p == null || p.length() == 0 || p.charAt(0) == '*') {
            return false;
        }
        for (int i = 1; i < p.length(); ++i) {
            if (p.charAt(i) == '*' && p.charAt(i - 1) == '*') {
                return false;
            }
        }
        return true;
    }
}

```
### Paint 系列

#### 256. Paint House
```java
/**
 * 题意：给一个二维数组，每排代表一个房子，每列代表一个颜色的cost,共有3总颜色
 * 求最小cost
 * */
public class PaintHouse0256 {
    /** time O(n) space O(1) 方法：dp
     * 思路：用3个遍历 记录上一次3个颜色的 最有结果，然后不断更新3个颜色的最优
     * 优化：
     * */
    public int minCost(int[][] costs) {
        if (costs == null || costs.length == 0) {
            return 0;
        }
        int lastRed = costs[0][0];
        int lastBlue = costs[0][1];
        int lastGreen = costs[0][2];
        for (int i = 1; i < costs.length; ++i) {
            int red = costs[i][0] + Math.min(lastBlue, lastGreen);
            int blue = costs[i][1] + Math.min(lastRed, lastGreen);
            int green = costs[i][2] + Math.min(lastRed, lastBlue);
            lastRed = red;
            lastBlue = blue;
            lastGreen = green;
        }
        return Math.min(lastRed, Math.min(lastGreen, lastBlue));
    }
}
```

#### 265. Paint House II

```java
/**
 * 题意：follow up: 这次有k个颜色 要求用 O(nk)时间
 * */
public class PaintHouseII0265 {
    /** time O(nk) space O(1) 方法：dp
     * 思路：其实，我们只要保证，当前房子的颜色和前面一个房子的颜色不一样就行，
     * 所以，我们可以维护3个变量，之前的最优价格，最优价格的index，之前的次优价格
     * 这样，只要当前房子的index和之前最优价格index不一样，就可以选之前最优价格，否则选次优价格
     * 然后再循环里面不断的跟新这三个变量就可以
     * 注意：当更新最优 和次优 的时候，应该先检查，当前价格是否低于最优，然后检查是否低于次优
     * 优化：
     * */
    public int minCostII(int[][] costs) {
        if (costs == null || costs.length == 0) {
            return 0;
        }
        int preMin = 0;
        int preMinIdx = 0;
        int preSecMin = 0;
        for (int[] cost : costs) {
            int currMin = Integer.MAX_VALUE;
            int currMinIdx = 0;
            int currSecMin = Integer.MAX_VALUE;
            for (int i = 0; i < costs[0].length; ++i) {
                int curr = (preMinIdx == i ? preSecMin : preMin) + cost[i];
                if (curr < currMin) {
                    currSecMin = currMin;
                    currMin = curr;
                    currMinIdx = i;
                } else if (curr < currSecMin) {
                    currSecMin = curr;
                }
            }
            preMin = currMin;
            preMinIdx = currMinIdx;
            preSecMin = currSecMin;
        }
        return preMin;
    }
}

```
#### 276. Paint Fence
```java

/**
 * 题意：给篱笆刷漆，要求，最多可以连续的2个篱笆有同样的颜色，求一共有多少种刷法？
 * */
public class PaintFence0276 {
    /** time O(n) space O(1) 方法：dp
     * 思路：比如ABC三个房子，k种颜色，A有k种，B有k*k种，C的种类数，可以分成2种情况来看
     * BC 不一样的的种类（diff) 有 上一次AB （diff + same）* （k - 1)，
     * 这里diff + same 是B房子的总种类数，
     * 那么BC一样的种类(same) 其实就是上一次 AB不一样的种类数，因为，AB 如果不一样，
     * 这时候，C可以随意用任何颜色，所以，对于每一种AB的情况，会有k种情况，
     * 并且会有且只有一种让BC 一样的情况
     * 优化：
     * https://segmentfault.com/a/1190000005740990
     * */
    public int numWays(int n, int k) {
        if (n < 1 || k < 1 || n > 2 && k == 1) {
            return 0;
        }
        if (n == 1) {
            return k;
        }
        if (n == 2) {
            return k * k;
        }
        int same = k;
        int diff = k * k - k;
        for (int i = 2; i < n; ++i) {
            int tem = diff;
            diff = (diff + same) * (k - 1);
            same = tem;
        }
        return diff + same;
    }
}

```

### 不好分类

#### 357. Count Numbers with Unique Digits

```java
/**
 * 题意：求n位数内有多少个数是没有重复数字的，
 * */
public class CountNumberswithUniqueDigits0357 {
    /** time O(1) space O(1) 方法：dp
     首先单独任何一位有10种可能，但n大于1的时候，不能有前置0，所有后面都最多只能有9位
     当n等于1的时候，肯定是10种, 但n等于2的时候，如果leftmost位不能是0，那么最多有9种
     后面本来可能放0到9一共10个数字，但这10个数字里面每次都有1个和前面的重复，
     所有不重复的情况就是 9*9，n==3的时候，如果前2位不重复的有9*9种，
     那么后面的10个数字种，每次都有2个会和前面的重复，所以3位不重复就有f(2) * 8
     但这算出的仅仅是每轮的结果，还要每次把每轮的结果加入到res里，最后return res
     */
    public int countNumbersWithUniqueDigits(int n) {
        if (n < 1) {
            return 1;
        }
        int res = 10;
        int unique = 9;
        for (int i = 1; i < 10 && i < n; ++i) {
            unique *= (10 - i);
            res += unique;
        }
        return res;
    }
}

```