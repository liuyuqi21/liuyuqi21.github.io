---
title: 剑指Offer-字符串、数组相关
date: 2019-06-10 16:03:03
tags: 剑指Offer
categories: 算法
---
# 字符串

## [5. 替换空格](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)
```java
public String replaceSpace(String s) {
    int blank = 0;
    for (int i = 0; i < s.length(); i++) {
        if (s.charAt(i) == ' ')
            blank++;
    }
    int len = s.length();
    int newLen = len + blank * 2;
    char[] newStr = new char[newLen];
    newLen--;
    len--;
    while (len >= 0 && newLen >= len) {
        char c = s.charAt(len--);
        if (c == ' ') {
            newStr[newLen--] = '0';
            newStr[newLen--] = '2';
            newStr[newLen--] = '%';
        } else {
            newStr[newLen--] = c;
        }
    }
    return String.valueOf(newStr);
}
```

## [19. 正则表达式匹配](https://leetcode-cn.com/problems/zheng-ze-biao-da-shi-pi-pei-lcof/)
[leetcode 10](https://leetcode-cn.com/problems/regular-expression-matching/)

## [20. 表示数值的字符串](https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/)
[leetcode 65](https://leetcode-cn.com/problems/valid-number/)

## [38. 字符串的排列](https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/)
```java
List<List<Integer>> res = new ArrayList<>();
Deque<Integer> path = new LinkedList<>();
public List<List<Integer>> permuteUnique(int[] nums) {
    if (nums.length == 0) {
        return res;
    }
    Arrays.sort(nums);
    boolean[] used = new boolean[nums.length];
    dfs(nums, 0, used);
    return res;
}
private void dfs(int[] nums, int depth, boolean[] used) 
    if (depth == nums.length) {
        res.add(new ArrayList(path));
        return;
    }
    int pre = nums[0] - 1;
    for (int i = 0; i < nums.length; i++) {
        if (!used[i] && pre != nums[i]) {
            used[i] = true;
            path.add(nums[i]);
            dfs(nums, depth + 1, used);
            path.removeLast();
            used[i] = false;
            pre = nums[i];
        }
    }
}
```
- 相关题目：[leetcode 47](https://leetcode-cn.com/problems/permutations-ii/)

## [46. 把数字翻译成字符串](https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)
```java
public int numDecodings(String s) {
    if (s == null || s.length() == 0) {
        return 0;
    }
    int len = s.length();
    int[] dp = new int[len + 1];
    dp[len] = 1;
    if (s.charAt(len - 1) == '0') {
        dp[len - 1] = 0;
    } else {
        dp[len - 1] = 1;
    }
    for (int i = len - 2; i >= 0; i--) {
        if (s.charAt(i) == '0') {
            dp[i] = 0;
        }else if ((s.charAt(i) - '0') * 10 + (s.charAt(i + 1) - '0') <= 26) {
            dp[i] = dp[i + 1] + dp[i + 2];
        } else {
            dp[i] = dp[i + 1];
        }
    }
    return dp[0];
}
```
- 相关题目：[leetcide 91](https://leetcode-cn.com/problems/decode-ways/)

## [48. 最长不含重复字符的子字符串](https://leetcode-cn.com/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/)
[leetcode 3](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)
```java
public int lengthOfLongestSubstring(String s) {
    int res = 0;
    int[] hash = new int[256];
    for (int i = 0, j = 0; i < s.length(); i++) {
        if (hash[s.charAt(i)] != 0) {
            j = Math.max(hash[s.charAt(i)], j);
        }
        res = Math.max(res, i - j + 1);
        hash[s.charAt(i)] = i + 1;
    }
    return res;
}
```

## [50. 第一个只出现一次的字符位置](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)
```java
public char firstUniqChar(String s) {
    if (s == null || s.length() == 0) {
        return ' ';
    }
    if (s.length() == 1) {
        return s.charAt(0);
    }
    int[] c = new int[256];
    for (int i = 0; i < s.length(); i++) {
        c[s.charAt(i)]++;
    }
    for (int i = 0; i < s.length(); i++) {
        if (c[s.charAt(i)] == 1) {
            return s.charAt(i);
        }
    }
    return ' ';
}
```
- 相关题目：[leetcode 387](https://leetcode-cn.com/problems/first-unique-character-in-a-string/)

## [58.1 翻转单词顺序列](https://leetcode-cn.com/problems/fan-zhuan-dan-ci-shun-xu-lcof/)
[leetcode 151](https://leetcode-cn.com/problems/reverse-words-in-a-string/)
```java
public String reverseWords(String s) {
    if (s == null) {
        return null;
    }
    char[] arr = s.toCharArray();
    int length = s.length();
    reverse(arr, 0, length - 1);
    int start = 0, end = 0;
    // 翻转单词
    while (end < length) {
        // 找到第一个字母
        while (start < length && arr[start] == ' ') {
            start++;
        }
        end = start;
        while (end < length && arr[end] != ' ') {
            end++;
        }
        reverse(arr, start, end - 1);
        start = end;
    }
    // 清除空格
    int i = 0, j = 0;
    while (j < length) {
        while (j < length && arr[j] == ' ') {
            j++;
        }
        while (j < length && arr[j] != ' ') {
            arr[i++] = arr[j++];
        }
        while (j < length && arr[j] == ' ') {
            j++;
        }
        if (j < length) {
            arr[i++] = ' ';
        }
    }
    return new String(arr).substring(0, i);
}
public void reverse(char[] c, int start, int end) {
    while (start < end) {
        char temp = c[start];
        c[start++] = c[end];
        c[end--] = temp;
    }
}
```
## [58.2 左旋转字符串](https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)
```java
public String LeftRotateString(String str, int n) {
    if (str == null || str.length() == 0 || n <= 0) {
        return str;
    }
    char[] c = str.toCharArray();
    reverse(c, 0, n - 1);
    reverse(c, n, str.length() - 1);
    //只能最后翻转整体
    reverse(c, 0, str.length() - 1);
    return new String(c);
}
public void reverse(char[] c, int start, int end) {
    while (start < end) {
        char temp = c[start];
        c[start] = c[end];
        c[end] = temp;
        start++;
        end--;
    }
}
```

# 数组

## [12. 矩阵中的路径](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)
[leetcode 79](https://leetcode-cn.com/problems/word-search/)
```java
boolean[][] flag;
public boolean exist(char[][] board, String word) {
    flag = new boolean[board.length][board[0].length];
    for (int i = 0; i < board.length; i++) {
        for (int j = 0; j < board[i].length; j++) {
            if (board[i][j] == word.charAt(0)) {
                if (dfs(board, word, i, j, 0)) {
                    return true;
                }
            }
        }
    }
    return false;
}
public boolean dfs(char[][] board, String word, int i, int j, int index) {
    if (index == word.length()) {
        return true;
    }
    if (i < 0 || i >= board.length || j < 0 || j >= board[0].length) {
        return false;
    }
    if (flag[i][j] || board[i][j] != word.charAt(index)) {
        return false;
    }
    flag[i][j] = true;
    index++;
    boolean res = dfs(board, word, i - 1, j, index) || dfs(board, word, i, j - 1, index)
            || dfs(board, word, i + 1, j, index) || dfs(board, word, i, j + 1, index);
    flag[i][j] = false;
    return res;
}
```

## [13. 机器人的运动范围](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)
```java
boolean[][] flag;
int count = 0;
public int movingCount(int m, int n, int k) {
    if (m <= 0 || n <= 0 || k < 0) {
        return 0;
    }
    flag = new boolean[m][n];
    dfs(0, 0, m, n, k);
    return count;
}
public void dfs(int i, int j, int m, int n, int k) {
    if (i < 0 || j < 0 || i >= m || j >= n || flag[i][j]) {
        return;
    }
    flag[i][j] = true;
    if (sum(i) + sum(j) > k) {
        return;
    }
    count++;
    dfs(i - 1, j, m, n, k);
    dfs(i + 1, j, m, n, k);
    dfs(i, j - 1, m, n, k);
    dfs(i, j + 1, m, n, k);
}
public int sum(int n) {
    int res = 0;
    while (n != 0) {
        res += n % 10;
        n /= 10;
    }
    return res;
}
```

## [29. 顺时针打印矩阵](https://leetcode-cn.com/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/)
[leetcode 54](https://leetcode-cn.com/problems/spiral-matrix/)

## [47. 礼物的最大价值](https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/)
```java
public int getMaxValue(int[][] grid) {
    if (grid == null || grid.length == 0 || grid[0].length == 0) {
        return 0;
    }
    int n = grid[0].length;
    int[] dp = new int[n];
    for (int[] nums : grid) {
        dp[0] += nums[0];
        for (int i = 1; i < n; i++) {
            dp[i] = Math.max(dp[i], dp[i - 1]) + nums[i];
        }
    }
    return dp[n - 1];
}
```

## [66. 构建乘积数组](https://leetcode-cn.com/problems/gou-jian-cheng-ji-shu-zu-lcof/)