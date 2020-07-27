---
title: 剑指Offer-数字相关
date: 2018-09-12 09:43:40
tags: 剑指Offer
categories: 算法
---
上个月好不容易在 leetcode 上找了这么多链接，这个月就出了剑指 Offer了，而且有的一样的题目都是全部新弄的，那就再整理一遍好啦哈哈，顺便把牛客的全部换掉。
## [3.1 数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

### 将元素放进对应下标
```java
public int findDuplicate(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        while (nums[i] != i) {
            int temp = nums[i];
            if (temp == nums[temp]) {
                return temp;
            }
            nums[i] = nums[temp];
            nums[temp] = temp;
        }
    }
    return 0;
}
```

## 3.2 不修改数组找出重复的数字
[leetcode 287](https://leetcode-cn.com/problems/find-the-duplicate-number/)

### 使用快慢指针抽象成环形链表问题
```java
public int findDuplicate(int[] nums) {
    int slow = nums[0];
    int fast = nums[0];
    do {
        slow = nums[slow];
        fast = nums[nums[fast]];
    } while (slow != fast);
    slow = nums[0];
    while (slow != fast) {
        slow = nums[slow];
        fast = nums[fast];
    }
    return slow;
}
```

### 二分查找
```java
public int findDuplicate(int[] nums) {
    int start = 1;
    int end = nums.length - 1;
    while (start <= end) {
        int middle = (start + end) / 2;
        int count = countRange(nums, start, middle);
        if (start == end) {
            if (count > 1) {
                return start;
            } else {
                break;
            }
        }
        if (count > (middle - start + 1)) {
            end = middle;
        } else {
            start = middle + 1;
        }
    }
    return 0;
}
public int countRange(int[] nums, int start, int end) {
    int count = 0;
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] <= end && nums[i] >= start) {
            count++;
        }
    }
    return count;
}
```

## [4. 二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)
[leetcode 240](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)
```java
public boolean searchMatrix(int[][] matrix, int target) {
    if (matrix == null || matrix.length == 0) {
        return false;
    }
    int row = 0;
    int col = matrix[0].length - 1;
    while (row < matrix.length && col >= 0) {
        if (matrix[row][col] < target) {
            row++;
        } else if (matrix[row][col] > target) {
            col--;
        } else {
            return true;
        }
    }
    return false;
}
```
- 相似题目：[leetcode 74](https://leetcode-cn.com/problems/search-a-2d-matrix/)

## [10. 斐波那契数列](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)
[leetcode 509](https://leetcode-cn.com/problems/fibonacci-number/)
```java
public int fib(int N) {
    if (N < 2) {
        return N;
    }
    int a = 1;
    int b = 0;
    int res = 0;
    for (int i = 2; i <= N; i++) {
        res = a + b;
        b = a;
        a = res;
    }
    return res;
}
```

## [11. 旋转数组的最小数字](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)
[leetcode 154](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)
```java
public int findMin(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int l = 0, r = nums.length - 1;
    if (nums.length == 1) {
        return nums[0];
    }
    if (nums[l] < nums[r]) {
        return nums[l];
    }
    while (l < r) {
        int mid = (l + r) / 2;
        if (nums[mid] < nums[r]) {
            if (nums[mid - 1] > nums[mid]) {
                return nums[mid];
            }
            r = mid - 1;
        } else if (nums[mid] > nums[r]) {
            if (nums[mid] > nums[mid + 1]) {
                return nums[mid + 1];
            }
            l = mid + 1;
        } else {
            r = r - 1;
        }
    }
    return nums[l];
}
```

## [14. 剪绳子](https://leetcode-cn.com/problems/jian-sheng-zi-lcof/)
[leetcode 343](https://leetcode-cn.com/problems/integer-break/description/)
### 贪心
```java
 public int integerBreak(int n) {
     if (n == 0) {
         return 0;
     }
     if (n <= 2) {
         return 1;
     }
     if (n == 3) {
         return 2;
     }
     int cntThree = n / 3;
     double max;
     if (n % 3 == 1) {
         cntThree--;
         max = Math.pow(3, cntThree) * 4;
     } else if (n % 3 == 2) {
         max = Math.pow(3, cntThree) * 2;
     } else {
         max = Math.pow(3, cntThree);
     }
     return (int) max;
 }
```

### 动态规划
```java
public int integerBreak(int n) {
    if (n == 0) {
        return 0;
    }
    if (n <= 2) {
        return 1;
    }
    if (n == 3) {
        return 2;
    }
    int cntThree = n / 3;
    int[] dp = new int[n + 1];
    dp[1] = 1;
    for (int i = 2; i <= n; i++) {
        for (int j = 1; j < i; j++) {
            dp[i] = Math.max(dp[i], Math.max(j * (i - j), dp[j] * (i - j)));
        }
    }
    return dp[n];
}
```

## [15. 二进制中 1 的个数](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)
[leetcode 191](https://leetcode-cn.com/problems/number-of-1-bits/)
```java
public int hammingWeight(int n) {
    int res = 0;
    while (n != 0) {
        res++;
        n = n & (n - 1);
    }
    return res;
}
```

## [16. 数值的整数次方](https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/)
[leetcode 50](https://leetcode-cn.com/problems/powx-n/)
```java
public double myPow(double x, int n) {
    long N = n;
    if (N < 0) {
        x = 1 / x;
        N = -N;
    }
    double res = 1;
    for (long i = N; i > 0; i /= 2) {
        if (i % 2 == 1) {
            res = res * x;
        }
        x *= x;
    }
    return res;
}
```

## [17. 打印从1到最大的n位数](https://leetcode-cn.com/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof/)
```java
public int[] printNumbers(int n) {
    int max = (int) Math.pow(10, n);
    int[] res = new int[max - 1];
    for (int i = 1; i < max; i++) {
        res[i - 1] = i;
    }
    return res;
}
```

## [21.1 调整数组的顺序使奇数位于偶数前面](https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)
[leetcode 905](https://leetcode-cn.com/problems/sort-array-by-parity/)
```java
public int[] sortArrayByParity(int[] A) {
    if (A == null || A.length == 0) {
        return A;
    }
    int i = 0, j = A.length - 1;
    while (i < j) {
        while (i < j && A[i] % 2 == 0) {
            i++;
        }
        while (i < j && A[j] % 2 == 1) {
            j--;
        }
        int temp = A[i];
        A[i] = A[j];
        A[j] = temp;
    }
    return A;
}
```

## 21.2 保证元素相对位置不变
```java
public int[] sortArrayByParity(int[] A) {
    if (A == null || A.length < 2) {
        return A;
    }
    int count = 0;
    for (int num : A) {
        if (num % 2 == 0) {
            count++;
        }
    }
    int[] copy = A.clone();
    int i = 0;
    for (int num : copy) {
        if (num % 2 == 0) {
            A[i++] = num;
        } else {
            A[count++] = num;
        }
    }
    return A;
}
```

## [39. 数组中出现的次数超过一半的数字](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/)
[leetcode 169](https://leetcode-cn.com/problems/majority-element/)
```java
public int majorityElement(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int count = 0, index = nums[0];
    for (int i = 1; i < nums.length; i++) {
        if (nums[i] == index) {
            count++;
        } else {
            count--;
        }
        if (count < 0) {
            count = 0;
            index = nums[i];
        }
    }
    return index;
}
```

### 基于 partition 解法
```cpp
int MoreThanHalfNum_Solution(vector<int> numbers) {
	if (numbers.size() == 1) {
		return numbers[0];
	}
	int len = numbers.size();
	int mid = len / 2;
	int start = 0;
	int end = len - 1;
	int index = partition(numbers, start, end);
	while (index != mid) {
		if (index > mid) {
			end = index - 1;
			index = partition(numbers, start, end);
		}
		else {
			start = index + 1;
			index = partition(numbers, start, end);
		}
	}

	int times = 0;
	for (int i = 0; i < len; ++i) {
		if (numbers[i] == numbers[index])
			times++;
	}
	if (times * 2 <= len) {
		return 0;
	}
	else {
		return numbers[index];
	}
}
int partition(vector<int> &nums, int l, int r) {
	int pivot = nums[l];
	while (l<r) {
		while (l<r&&nums[r] >= pivot) {
			r--;
		}
		nums[l] = nums[r];
		while (l<r&&nums[l] <= pivot) {
			l++;
		}
		nums[r] = nums[l];
	}
	nums[l] = pivot;
	return l;
}
```

## [40. 最小的k个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)

### 基于堆的解法
```java
public int[] getLeastNumbers(int[] nums, int k) {
    PriorityQueue<Integer> queue = new PriorityQueue<>((a, b) -> (b - a));
    for (int num : nums) {
        queue.add(num);
        if (queue.size() > k) {
            queue.poll();
        }
    }
    return queue.stream().mapToInt(Integer::intValue).toArray();
}
```

### 基于 partition 的解法
```java
public int[] getLeastNumbers(int[] nums, int k) {
    if (nums == null || nums.length == 0 || k <= 0) {
        return new int[] {};
    }
    int start = 0;
    int end = nums.length - 1;
    int index = partition(nums, start, end);
    while (index != k - 1) {
        if (index < k - 1) {
            start = index + 1;
        } else {
            end = index - 1;
        }
        index = partition(nums, start, end);
    }
    return Arrays.copyOf(nums, index + 1);
}
public int partition(int[] nums, int l, int r) {
    int pivot = nums[l];
    while (l < r) {
        while (l < r && nums[r] >= pivot) {
            r--;
        }
        nums[l] = nums[r];
        while (l < r && nums[l] <= pivot) {
            l++;
        }
        nums[r] = nums[l];
    }
    nums[l] = pivot;
    return l;
}
```
- 相似题目：[leetcode 215](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

## [41. 数据流中的中位数](https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)
[leetcode 295](https://leetcode-cn.com/problems/find-median-from-data-stream/solution/shu-ju-liu-de-zhong-wei-shu-by-leetcode/)
```java
PriorityQueue<Integer> left;
PriorityQueue<Integer> right;
boolean isOdd=false;
public MedianFinder() {
    left = new PriorityQueue<>((o1, o2) -> o2 - o1);
    right = new PriorityQueue<>();
}
public void addNum(int num) {
    if(isOdd){
        right.add(num);
        left.add(right.poll());
    }else{
        left.add(num);
        right.add(left.poll());
    }
    isOdd=!isOdd;
}
public double findMedian() {
    if(isOdd){
        return right.peek();
    }else{
        return (left.peek()+right.peek())/2.0;
    }
}
```

## [42. 连续子数组的最大和](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)
[leetcode 53](https://leetcode-cn.com/problems/maximum-subarray/)
```java
public int maxSubArray(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int res = nums[0], max = res;
    for (int i = 1; i < nums.length; i++) {
        if (max > 0) {
            max += nums[i];
        } else {
            max = nums[i];
        }
        if (max > res) {
            res = max;
        }
    }
    return res;
}
```

## [43. 1～n整数中1出现的次数](https://leetcode-cn.com/problems/1nzheng-shu-zhong-1chu-xian-de-ci-shu-lcof/)
[leetcode 233](https://leetcode-cn.com/problems/number-of-digit-one/)
```java
public int countDigitOne(int n) {
    if (n <= 0) {
        return 0;
    }
    String nums = String.valueOf(n);
    int high = nums.charAt(0) - '0';
    int pow = (int) Math.pow(10, nums.length() - 1);
    int last = n - high * pow;
    int count = countDigitOne(last) + high * countDigitOne(pow - 1);
    if (high == 1) {
        return count + last + 1;
    } else {
        return count + pow;
    }
}
```

## [44. 数字序列中某一位的数字](https://leetcode-cn.com/problems/shu-zi-xu-lie-zhong-mou-yi-wei-de-shu-zi-lcof/)
[leetcode 400](https://leetcode-cn.com/problems/nth-digit/)

## [45. 把数组排成最小的数](https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)
[leetcode 179](https://leetcode-cn.com/problems/largest-number/)
```java
public String largestNumber(int[] nums) {
    if (nums == null || nums.length == 0) {
        return "";
    }
    String[] arr = new String[nums.length];
    for (int i = 0; i < nums.length; i++) {
        arr[i] = String.valueOf(nums[i]);
    }
    Arrays.sort(arr, (o1, o2) -> (o2 + o1).compareTo(o1 + o2));
    if (arr[0].equals("0")) {
        return "0";
    }
    String res = new String();
    for (String str : arr) {
        res += str;
    }
    return res;
}
```

## [49.丑数](https://leetcode-cn.com/problems/chou-shu-lcof/)
[leetcode 264](https://leetcode-cn.com/problems/ugly-number-ii/)
```java
public int nthUglyNumber(int n) {
    if (n <= 6) {
        return n;
    }
    int i2 = 0, i3 = 0, i5 = 0;
    int[] dp = new int[n];
    dp[0] = 1;
    for (int i = 1; i < n; i++) {
        int next2 = dp[i2] * 2, next3 = dp[i3] * 3, next5 = dp[i5] * 5;
        dp[i] = Math.min(next2, Math.min(next3, next5));
        if (dp[i] == next2) {
            i2++;
        }
        if (dp[i] == next3) {
            i3++;
        }
        if (dp[i] == next5) {
            i5++;
        }
    }
    return dp[n - 1];
}
```

## [51. 数组中的逆序对](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

```java
private int cnt = 0;
int[] tmp;
public int reversePairs(int[] nums) {
    if (nums.length <= 1) {
        return 0;
    }
    tmp = new int[nums.length];
    mergeSort(nums, 0, nums.length - 1);
    return cnt;
}
void mergeSort(int[] data, int start, int end) {
    if (start >= end) {
        return;
    }
    int mid = (start + end) / 2;
    mergeSort(data, start, mid);
    mergeSort(data, mid + 1, end);
    merge(data, start, mid, end);
}
void merge(int[] data, int start, int mid, int end) {
    int l = mid, r = end, index = end;
    //排序并统计个数
    while (l >= start && r >= mid + 1) {
        if (data[l] > data[r]) {
            tmp[index--] = data[l--];
            cnt += r - mid;
        } else {
            tmp[index--] = data[r--];
        }
    }
    //处理左边剩余数据
    while (l >= start) {
        tmp[index--] = data[l--];
    }
    //处理右边剩余数据
    while (r >= mid + 1) {
        tmp[index--] = data[r--];
    }
    //将数据复制到原数组
    for (int i = start; i <= end; i++) {
        data[i] = tmp[i];
    }
}
```

## [53.1 数字在排序数组中出现的次数](https://leetcode-cn.com/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)
[leetcode 34](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)
```java
public int[] searchRange(int[] nums, int target) {
    if (nums == null || nums.length == 0) {
        return new int[]{-1, -1};
    }
    return new int[]{getFirst(nums, target), getLast(nums, target)};
}
private int getFirst(int[] a, int v) {
    int l = 0;
    int r = a.length - 1;
    int res = -1;
    while (l <= r) {
        int mid = l + ((r - l) >> 1);
        if (a[mid] > v) {
            r = mid - 1;
        } else if (a[mid] < v) {
            l = mid + 1;
        } else {
            res = mid;
            r = mid - 1;
        }
    }
    return res;
}
private int getLast(int[] a, int v) {
    int l = 0;
    int r = a.length - 1;
    int res = -1;
    while (l <= r) {
        int mid = l + ((r - l) >> 1);
        if (a[mid] < v) {
            l = mid + 1;
        } else if (a[mid] > v) {
            r = mid - 1;
        } else {
            res = mid;
            l = mid + 1;
        }
    }
    return res;
}
```

## [53.2 0~n-1 中缺失的数字](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)
[leetcode 268](https://leetcode-cn.com/problems/missing-number/)
### 排序
```java
public int missingNumber(int[] nums) {
    Arrays.sort(nums);
    // 判断 n 是否出现在末位
    if (nums[nums.length - 1] != nums.length) {
        return nums.length;
    }
    // 判断 0 是否出现在首位
    else if (nums[0] != 0) {
        return 0;
    }
    for (int i = 0; i < nums.length; i++) {
        if (i != nums[i]) {
            return i;
        }
    }
    return -1;
}
```

### 位运算
```java
public int missingNumber(int[] nums) {
    int res = nums.length;
    for (int i = 0; i < nums.length; i++) {
        res ^= nums[i] ^ i;
    }
    return res;
}
```

## [56.1 数组中只出现一次的两个数字](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)
[leetcode 260](https://leetcode-cn.com/problems/single-number-iii/)

### 使用哈希表
```java
public int[] singleNumber(int[] nums) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int num : nums) {
        map.put(num, map.getOrDefault(num, 0) + 1);
    }
    int[] res = new int[2];
    int i = 0;
    for (Map.Entry<Integer, Integer> item : map.entrySet()) {
        if (item.getValue() == 1) {
            res[i++] = item.getKey();
        }
    }
    return res;
}
```
### 使用异或
```java
public int[] singleNumber(int[] nums) {
    int bitmask = 0;
    for (int num : nums) {
        bitmask ^= num;
    }
    int diff = bitmask & (-bitmask);
    int x = 0, y = 0;
    for (int num : nums) {
        if ((num & diff) == 0) {
            x ^= num;
        } else {
            y ^= num;
        }
    }
    return new int[]{x, y};
}
```

## [56.2 数组中唯一只出现一次的数字](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/)
[leetcode 137](https://leetcode-cn.com/problems/single-number-ii/)
```java
public int singleNumber(int[] nums) {
    int a = 0, b = 0;
    for (int num : nums) {
        b = (b ^ num) & ~a;
        a = (a ^ num) & ~b;
    }
    return b;
}
```

## [57.1 和为 s 的两个数字](https://leetcode-cn.com/problems/he-wei-sde-liang-ge-shu-zi-lcof/)
[leetcode 167](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/)
```java
public int[] twoSum(int[] numbers, int target) {
    if (numbers == null || numbers.length == 0) {
        return new int[]{};
    }
    int l = 0, r = numbers.length - 1;
    while (l < r) {
        if (numbers[l] + numbers[r] < target) {
            l++;
        } else if (numbers[l] + numbers[r] > target) {
            r--;
        } else {
            break;
        }
    }
    return new int[]{l + 1, r + 1};
}
```

## [57.2 和为 s 的连续正数序列](https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/)
```java
public List<List<Integer>> FindContinuousSequence(int sum) {
    int small = 1, big = 2, count = 3;
    List<List<Integer>> res = new ArrayList<>();
    while (small <= sum / 2) {
        while (count < sum) {
            big++;
            count += big;
        }
        if (count == sum) {
            List<Integer> list = new ArrayList<>();
            for (int i = small; i <= big; i++) {
                list.add(i);
            }
            res.add(list);
        }
        count -= small;
        small++;
    }
    return res;
}
```

## [59 滑动窗口的最大值](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)
[leetcode 239](https://leetcode-cn.com/problems/sliding-window-maximum/)

### 使用队列
```java
public int[] maxSlidingWindow(int[] nums, int k) {
    if (nums == null || nums.length <= 1 || k <= 1) {
        return nums;
    }
    int[] res = new int[nums.length - k + 1];
    //双向队列，保存数组下标
    Deque<Integer> deque = new LinkedList<>();
    for (int i = 0; i < nums.length; i++) {
        //如果前面的数更小，弹出
        while (!deque.isEmpty() && nums[i] >= nums[deque.getLast()]) {
            deque.removeLast();
        }
        deque.add(i);
        //移除不在当前窗口的数字
        if (!deque.isEmpty() && (i - deque.peek() >= k)) {
            deque.pop();
        }
        //当窗口长度为k，保存当前窗口最前面的值
        if (i - k + 1 >= 0) {
            res[i - k + 1] = nums[deque.peek()];
        }
    }
    return res;
}
```

### 使用堆
```java
public int[] maxSlidingWindow(int[] nums, int k) {
    if (nums == null || nums.length <= 1 || k <= 1) {
        return nums;
    }
    int[] res = new int[nums.length - k + 1];
    PriorityQueue<Integer> heap = new PriorityQueue<>((o1, o2) -> o2 - o1);
    for (int i = 0; i < k; i++) {
        heap.add(nums[i]);
    }
    res[0] = heap.peek();
    for (int i = 0; (i + k) < nums.length; i++) {
        heap.add(nums[i + k]);
        heap.remove(nums[i]);
        res[i + 1] = heap.peek();
    }
    return res;
}
```

## [60. n个骰子的点数](https://leetcode-cn.com/problems/nge-tou-zi-de-dian-shu-lcof/)

## [61. 扑克牌顺子](https://leetcode-cn.com/problems/bu-ke-pai-zhong-de-shun-zi-lcof/)
```java
public boolean isContinuous(int[] numbers) {
    if (numbers == null || numbers.length != 5) {
        return false;
    }
    Arrays.sort(numbers);
    int count = 0;
    for (int num : numbers) {
        if (num == 0) {
            count++;
        }
    }
    for (int i = count; i < numbers.length - 1; i++) {
        if (numbers[i + 1] == numbers[i]) {
            return false;
        }
        count -= numbers[i + 1] - numbers[i] - 1;
    }
    return count >= 0;
}
```

## [62. 圆圈中最后剩下的数字](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/)

### 使用环形链表模拟
```java
public int LastRemaining(int n, int m) {
    if (n < 1 || m < 1) {
        return -1;
    }
    Deque<Integer> deque = new LinkedList<>();
    for (int i = 0; i < n; i++) {
        deque.add(i);
    }
    int res = 0;
    while (!deque.isEmpty()) {
        for (int i = 0; i < m - 1; i++) {
            deque.add(deque.pop());
        }
        res = deque.pop();
    }
    return res;
}
```

### 公式推导
```java
public int LastRemaining_Solution(int n, int m) {
    if (n < 1 || m < 1) {
        return -1;
    }
    int res = 0;
    for (int i = 2; i <= n; i++) {
        res = (res + m) % i;
    }
    return res;
}
```

## [63. 股票的最大利润](https://leetcode-cn.com/problems/gu-piao-de-zui-da-li-run-lcof/)
[leetcode 121](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)
```java
public int maxProfit(int[] prices) {
    if (prices == null || prices.length == 0) {
        return 0;
    }
    int res = 0, min = prices[0];
    for (int i = 1; i < prices.length; i++) {
        if (prices[i] < min) {
            min = prices[i];
        }
        if (prices[i] - min > res) {
            res = prices[i] - min;
        }
    }
    return res;
}
```

## [64. 求1+2+…+n](https://leetcode-cn.com/problems/qiu-12n-lcof/)

## [65. 不用加减乘除做加法](https://leetcode-cn.com/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/)

## [67. 把字符串转换成整数](https://leetcode-cn.com/problems/ba-zi-fu-chuan-zhuan-huan-cheng-zheng-shu-lcof/)
[leetcode 8](https://leetcode-cn.com/problems/string-to-integer-atoi/)
```java
public int myAtoi(String str) {
    if (str == null || str.length() == 0) {
        return 0;
    }
    int res = 0;
    boolean flag = true;
    int i = 0;
    while (i < str.length() && str.charAt(i) == ' ') {
        i++;
    }
    if (i == str.length()) {
        return 0;
    }
    if (str.charAt(i) == '-') {
        flag = false;
    } else if (str.charAt(i) == '+') {
        flag = true;
    } else if (str.charAt(i) < '0' || str.charAt(i) > '9') {
        return res;
    } else {
        res = str.charAt(i) - '0';
    }
    i++;
    for (; i < str.length(); i++) {
        if (str.charAt(i) < '0' || str.charAt(i) > '9') {
            break;
        }
        int x = str.charAt(i) - '0';
        if (flag) {
            if (res > Integer.MAX_VALUE / 10 || res == Integer.MAX_VALUE / 10 && x > 7) {
                return Integer.MAX_VALUE;
            }
        } else {
            if (-res < Integer.MIN_VALUE / 10 || -res == Integer.MIN_VALUE / 10 && x > 8) {
                return Integer.MIN_VALUE;
            }
        }
        res = res * 10 + x;
    }
    if (!flag) {
        res = -res;
    }
    return res;
}
```