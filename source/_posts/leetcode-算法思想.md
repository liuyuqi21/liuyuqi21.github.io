---
title: leetcode 算法思想
date: 2020-02-12 15:21:53
tags: leetcode
categories: 算法
---

# 动态规划

## [152. 乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)

```java
public int maxProduct(int[] nums) {
    int res = Integer.MIN_VALUE, max = 1, min = 1;
    for (int num : nums) {
        if (num < 0) {
            int temp = max;
            max = min;
            min = temp;
        }
        max = Math.max(max * num, num);
        min = Math.min(min * num, num);
        res = Math.max(max, res);
    }
    return res;
}
```

## [198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/)

```java
public int rob(int[] nums) {
    int pre = 0, cur = 0, ppre = 0;
    for (int num : nums) {
        cur = Math.max(pre, ppre + num);
        ppre = pre;
        pre = cur;
    }
    return cur;
}
```

## [279. 完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

```java
public int numSquares(int n) {
    int[] dp = new int[n + 1];
    for (int i = 0; i < n + 1; i++) {
        dp[i] = i;
        for (int j = 1; i - j * j >= 0; j++) {
            dp[i] = Math.min(dp[i], dp[i - j * j] + 1);
        }
    }
    return dp[n];
}
```

## [416. 分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/)（0-1背包问题）

```java
public boolean canPartition(int[] nums) {
    if (nums == null || nums.length == 0) {
        return false;
    }
    int sum = 0;
    for (int num : nums) {
        sum += num;
    }
    if (sum % 2 == 1) {
        return false;
    }
    int target = sum / 2;
    for (int num : nums) {
        if (num > target) {
            return false;
        }
    }
    boolean[] dp = new boolean[target + 1];
    dp[0] = true;
    dp[nums[0]] = true;
    for (int i = 1; i < nums.length; i++) {
        if (dp[target]) {
            return true;
        }
        for (int j = target; nums[i] <= j; j--) {
            dp[j] = dp[j] || dp[j - nums[i]];
        }
    }
    return dp[target];
}
```

## [1049. 最后一块石头的重量 II](https://leetcode-cn.com/problems/last-stone-weight-ii/)（0-1背包问题）

```java
public int lastStoneWeightII(int[] stones) {
    if (stones == null || stones.length == 0) {
        return 0;
    }
    int sum = 0;
    for (int num : stones) {
        sum += num;
    }
    int target = sum / 2;
    int[] dp = new int[target + 1];
    for (int i = 0; i < stones.length; i++) {
        int cur = stones[i];
        for (int j = target; j >= cur; j--) {
            dp[j] = Math.max(dp[j], dp[j - cur] + cur);
        }
        if (dp[target] == target) {
            break;
        }
    }
    return sum - 2 * dp[target];
}
```

## [322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)

### 贪心 + DFS

```java
int ans = Integer.MAX_VALUE;
public int coinChange(int[] coins, int amount) {
    Arrays.sort(coins);
    dfs(coins, coins.length - 1, amount, 0);
    return ans == Integer.MAX_VALUE ? -1 : ans;
}
public void dfs(int[] coins, int index, int amount, int cnt) {
    if (index < 0) {
        return;
    }
    for (int c = amount / coins[index]; c >= 0; c--) {
        int na = amount - c * coins[index];
        int ncnt = cnt + c;
        if (na == 0) {
            ans = Math.min(ans, ncnt);
            break;//剪枝1
        }
        if (ncnt + 1 >= ans) {
            break; //剪枝2
        }
        dfs(coins, index - 1, na, ncnt);
    }
}
```

### DP

```java
public int coinChange(int[] coins, int amount) {
    if (amount == 0) {
        return 0;
    }
    int[] dp = new int[amount + 1];
    Arrays.fill(dp, amount + 1);
    dp[0] = 0;
    for (int i = 1; i < amount + 1; i++) {
        for (int coin : coins) {
            if (i >= coin) {
                dp[i] = Math.min(dp[i], dp[i - coin] + 1);
            }
        }
    }
    return dp[amount] == amount + 1 ? -1 : dp[amount];
}
```

## [518. 零钱兑换 II](https://leetcode-cn.com/problems/coin-change-2/)（完全背包问题）

```java
public int change(int amount, int[] coins) {
    int[] dp = new int[amount + 1];
    dp[0] = 1;
    for (int coin : coins) {
        for (int i = 0; i < amount + 1; i++) {
            if (i >= coin) {
                dp[i] = dp[i] + dp[i - coin];
            }
        }
    }
    return dp[amount];
}
```

# 排序

## [56. 合并区间](https://leetcode-cn.com/problems/merge-intervals/)

```java
public int[][] merge(int[][] intervals) {
    Arrays.sort(intervals, new Comparator<int[]>() {
        @Override
        public int compare(int[] o1, int[] o2) {
            return o1[0] - o2[0];
        }
    });
    List<int[]> list = new ArrayList<>();
    int i = 0;
    while (i < intervals.length) {
        int[] temp = new int[2];
        temp[0] = intervals[i][0];
        int max = intervals[i][1];
        while (i < intervals.length - 1 && max >= intervals[i + 1][0]) {
            max = Math.max(max, intervals[i + 1][1]);
            i++;
        }
        temp[1] = max;
        list.add(temp);
        i++;
    }
    return list.toArray(new int[list.size()][2]);
}
```

## [252. 会议室](https://leetcode-cn.com/problems/meeting-rooms/)

```java
public boolean canAttendMeetings(int[][] intervals) {
    Arrays.sort(intervals, new Comparator<int[]>() {
        @Override
        public int compare(int[] o1, int[] o2) {
            return o1[0] - o2[0];
        }
    });
    for (int i = 0; i < intervals.length - 1; i++) {
        if (intervals[i][1] > intervals[i + 1][0])
            return false;
    }
    return true;
}
```

## [253. 会议室 II](https://leetcode-cn.com/problems/meeting-rooms-ii/)

```java
public int minMeetingRooms(int[][] intervals) {
    if (intervals == null || intervals.length == 0) {
        return 0;
    }
    int[] start = new int[intervals.length];
    int[] end = new int[intervals.length];
    for (int i = 0; i < intervals.length; i++) {
        start[i] = intervals[i][0];
        end[i] = intervals[i][1];
    }
    Arrays.sort(start);
    Arrays.sort(end);
    int res = 1;
    int j = 0;
    for (int i = 1; i < start.length; i++) {
        if (start[i] >= end[j]) {
            j++;
        } else {
            res++;
        }
    }
    return res;
}
```

# 双指针

## [11. 盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

```java
public int maxArea(int[] height) {
    if (height == null || height.length == 0) {
        return 0;
    }
    int l = 0;
    int r = height.length - 1;
    int res = 0, num = 0;
    while (l < r) {
        if (height[l] < height[r]) {
            num = height[l];
            l++;
        } else {
            num = height[r];
            r--;
        }
        if (num * (r - l + 1) > res) {
            res = num * (r - l + 1);
        }
    }
    return res;
}
```

## [33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

```java
public int search(int[] nums, int target) {
    if (nums == null || nums.length == 0) {
        return -1;
    }
    int len = nums.length;
    int l = 0, r = len - 1;
    while (l <= r) {
        int mid = (l + r) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] >= nums[l]) {
            if (nums[l] == target) {
                return l;
            }
            if (nums[l] <= target && nums[mid] > target) {
                r = mid - 1;
            } else {
                l = mid + 1;
            }
        } else {
            if (nums[r] == target) {
                return r;
            }
            if (nums[r] >= target && nums[mid] < target) {
                l = mid + 1;java
            } else {
                r = mid - 1;
            }
        }
    }
    return -1;
}
```

## [42. 接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

### 双指针

```java
public int trap(int[] height) {
    int left = 0, right = height.length - 1;
    int left_max = 0, right_max = 0;
    int res = 0;
    while (left < right) {
        if (height[left] < height[right]) {
            if (height[left] < left_max) {
                res += left_max - height[left];
            } else {
                left_max = height[left];
            }
            left++;
        } else {
            if (height[right] < right_max) {
                res += right_max - height[right];
            } else {
                right_max = height[right];
            }
            right--;
        }
    }
    return res;
}
```

### 单调栈

```java
public int trap(int[] height) {
    int res = 0;
    Deque<Integer> stack = new LinkedList<>();
    for (int i = 0; i < height.length; i++) {
        while (!stack.isEmpty() && height[stack.peek()] < height[i]) {
            int h = stack.pop();
            if (!stack.isEmpty()) {
                int distance = i - stack.peek() - 1;
                int min = Math.min(height[i], height[stack.peek()]);
                res += (min - height[h]) * distance;
            }
        }
        stack.push(i);
    }
    return res;
}
```

# 回溯

## [17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

```java
Map<Character, String> phone = new HashMap<Character, String>() {{
    put('2', "abc");
    put('3', "def");
    put('4', "ghi");
    put('5', "jkl");
    put('6', "mno");
    put('7', "pqrs");
    put('8', "tuv");
    put('9', "wxyz");
}};
List<String> res = new ArrayList<String>();
public List<String> letterCombinations(String digits) {
    if (digits.length() != 0) {
        helper("", digits);
    }
    return res;
}
public void helper(String str, String digits) {
    if (digits.length() == 0) {
        res.add(str);
    } else {
        String letters = phone.get(digits.charAt(0));
        for (int i = 0; i < letters.length(); i++) {
            String letter = letters.substring(i, i + 1);
            helper(str + letter, digits.substring(1));
        }
    }
}
```

## [22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

```java
int n;
List<String> res = new ArrayList<>();
public List<String> generateParenthesis(int n) {
    if (n == 0) {
        return res;
    }
    this.n = n;
    dfs("", 0, 0);
    return res;
}
public void dfs(String str, int left, int right) {
    if (left == n && right == n) {
        res.add(str);
    }
    if (left < right) {
        return;
    }
    if (left < n) {
        dfs(str + '(', left + 1, right);
    }
    if (right < n) {
        dfs(str + ')', left, right + 1);
    }
}
```

## [79. 单词搜索](https://leetcode-cn.com/problems/word-search/)

```java
boolean[][] flag;
char[][] board;
String word;
public boolean exist(char[][] board, String word) {
    flag = new boolean[board.length][board[0].length];
    this.board = board;
    this.word = word;
    for (int i = 0; i < board.length; i++) {
        for (int j = 0; j < board[i].length; j++) {
            if (board[i][j] == word.charAt(0)) {
                if (dfs(i, j, 0)) {
                    return true;
                }
            }
        }
    }
    return false;
}
public boolean dfs(int row, int col, int index) {
    if (index == word.length()) {
        return true;
    }
    if (row < 0 || row >= board.length || col < 0 || col >= board[0].length) {
        return false;
    }
    if (flag[row][col] || board[row][col] != word.charAt(index)) {
        return false;
    }
    flag[row][col] = true;
    index++;
    boolean res = dfs(row - 1, col, index) || dfs(row, col - 1, index) || dfs(row + 1, col, index) || dfs(row, col + 1, index);
    flag[row][col] = false;
    return res;
}
```

# 并查集

## [200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

### DFS

```java
public int numIslands(char[][] grid) {
    if (grid == null || grid.length == 0) {
        return 0;
    }
    int res = 0;
    for (int r = 0; r < grid.length; ++r) {
        for (int c = 0; c < grid[0].length; ++c) {
            if (grid[r][c] == '1') {
                res++;
                dfs(grid, r, c);
            }
        }
    }
    return res;
}
public void dfs(char[][] grid, int r, int c) {
    if (r < 0 || c < 0 || r >= grid.length || c >= grid[0].length || grid[r][c] == '0') {
        return;
    }
    grid[r][c] = '0';
    dfs(grid, r - 1, c);
    dfs(grid, r + 1, c);
    dfs(grid, r, c - 1);
    dfs(grid, r, c + 1);
}
```

### 并查集