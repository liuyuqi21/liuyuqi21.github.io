---
title: leetcode 数据结构
date: 2020-04-11 00:14:33
tags: leetcode
categories: 算法
---

# 数字

## [7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)

```java
public int reverse(int x) {
    int res = 0;
    while (x != 0) {
        int pop = x % 10;
        x /= 10;
        if (res > Integer.MAX_VALUE / 10 || res == Integer.MAX_VALUE / 10 && pop > 7) {
            return 0;
        }
        if (res < Integer.MIN_VALUE / 10 || res == Integer.MIN_VALUE / 10 && pop < -8) {
            return 0;
        }
        res = res * 10 + pop;
    }
    return res;
}
```

## [9. 回文数](https://leetcode-cn.com/problems/palindrome-number/)

```java
public boolean isPalindrome(int x) {
    if (x < 0 || x % 10 == 0 && x != 0) {
        return false;
    }
    int rev = 0;
    while (x > rev) {
        rev = rev * 10 + x % 10;
        x = x / 10;
    }
    return rev == x || x == rev / 10;
}
```

## [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

```java
public List<List<Integer>> threeSum(int[] nums) {
    List<List<Integer>> res = new ArrayList<>();
    if (nums == null || nums.length < 3) {
        return res;
    }
    Arrays.sort(nums);
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] > 0) {
            break;
        }
        if (i > 0 && nums[i] == nums[i - 1]) {
            continue;
        }
        int l = i + 1, r = nums.length - 1;
        while (l < r) {
            int sum = nums[l] + nums[r] + nums[i];
            if (sum == 0) {
                res.add(Arrays.asList(nums[i], nums[l], nums[r]));
                while (l < r && nums[l] == nums[l + 1]) {
                    l++;
                }
                while (l < r && nums[r] == nums[r - 1]) {
                    r--;
                }
                l++;
                r--;
            } else if (sum < 0) {
                l++;
            } else {
                r--;
            }
        }
    }
    return res;
}
```

## [31. 下一个排列](https://leetcode-cn.com/problems/next-permutation/)

```java
public void nextPermutation(int[] nums) {
    int i = nums.length - 2;
    while (i >= 0 && nums[i + 1] <= nums[i]) {
        i--;
    }
    if (i >= 0) {
        int j = nums.length - 1;
        while (j > i && nums[j] <= nums[i]) {
            j--;
        }
        swap(nums, i, j);
    }
    reverse(nums, i + 1);
}
private void reverse(int[] nums, int start) {
    int i = start, j = nums.length - 1;
    while (i < j) {
        swap(nums, i, j);
        i++;
        j--;
    }
}
private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

## [382. 链表随机节点](https://leetcode-cn.com/problems/linked-list-random-node/)（蓄水池采样）

```java
class Solution {
    public ListNode head;

    public Solution(ListNode head) {
        this.head = head;
    }

    public int getRandom() {
        int res = head.val;
        ListNode node = head.next;
        int i = 2;
        Random random = new Random();
        while (node != null) {
            if (random.nextInt(i) == 0) {
                res = node.val;
            }
            i++;
            node = node.next;
        }
        return res;
    }
}

```

## [398. 随机数索引](https://leetcode-cn.com/problems/random-pick-index/)(蓄水池采样)

```java
class Solution {
    public int[] nums;

    public Solution(int[] nums) {
        this.nums = nums;
    }

    public int pick(int target) {
        Random r = new Random();
        int n = 0, index = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == target) {
                n++;
                if (r.nextInt() % n == 0) {
                    index = i;
                }
            }
        }
        return index;
    }
}
```

## [739. 每日温度](https://leetcode-cn.com/problems/daily-temperatures/)

使用单调栈

```java
public int[] dailyTemperatures(int[] T) {
    if (T == null || T.length == 0) {
        return new int[]{};
    }
    int[] res = new int[T.length];
    Deque<Integer> list = new LinkedList<>();
    for (int i = 0; i < T.length; i++) {
        while (!list.isEmpty() && T[i] > T[list.peek()]) {
            int pre = list.pop();
            res[pre] = i - pre;
        }
        list.push(i);
    }
    return res;
}
```

# 字符串

## [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

### 中心扩展法

```java
public String longestPalindrome(String s) {
    if (s == null || s.length() == 0) {
        return "";
    }
    int start = 0, end = 0;
    for (int i = 0; i < s.length() - 1; i++) {
        int len1 = helper(s, i, i);
        int len2 = helper(s, i, i + 1);
        int len = Math.max(len1, len2);
        if (len > end - start) {
            start = i - (len - 1) / 2;
            end = i + len / 2;
        }
    }
    return s.substring(start, end + 1);
}
public int helper(String s, int l, int r) {
    while (l >= 0 && r < s.length() && s.charAt(l) == s.charAt(r)) {
        l--;
        r++;
    }
    return r - l - 1;
}
```

### 动态规划

### Manacher's Algorithm

## [32. 最长有效括号](https://leetcode-cn.com/problems/longest-valid-parentheses/)

```java
public int longestValidParentheses(String s) 
    if (s == null || s.length() == 0) {
        return 0;
    }
    int left = 0, right = 0, max = 0;
    for (int i = 0; i < s.length(); i++) {
        if (s.charAt(i) == '(') {
            left++;
        } else {
            right++;
        }
        if (left == right) {
            max = Math.max(max, right + left)
        }
        if (right > left) {
            left = 0;
            right = 0;
        }
    }
    left = 0;
    right = 0;
    for (int i = s.length() - 1; i >= 0; i--)
        if (s.charAt(i) == ')') {
            right++;
        } else {
            left++;
        }
        if (left == right) {
            max = Math.max(max, right + left)
        }
        if (left > right) {
            left = 0;
            right = 0;
        }
    }
    return max;
}
```

## [678. 有效的括号字符串](https://leetcode-cn.com/problems/valid-parenthesis-string/)

```java
public boolean checkValidString(String s) {
    if (s == null) {
        return false;
    }
    if (s.length() == 0) {
        return true;
    }
    Deque<Integer> left = new LinkedList<>();
    Deque<Integer> star = new LinkedList<>();
    for (int i = 0; i < s.length(); i++) {
        char c = s.charAt(i);
        if (c == '(') {
            left.push(i);
        } else if (c == ')') {
            if (left.isEmpty() && star.isEmpty()) {
                return false;
            }
            if (!left.isEmpty()) {
                left.pop();
            } else {
                star.pop();
            }
        } else {
            star.push(i);
        }
    }
    while (!left.isEmpty() && !star.isEmpty()) {
        if (left.peek() > star.peek()) {
            return false;
        }
        left.pop();
        star.pop();
    }
    return left.isEmpty();
}
```

## [796. 旋转字符串](https://leetcode-cn.com/problems/rotate-string/)

```java
public boolean rotateString(String A, String B) {
    return A.length() == B.length() && (A + A).contains(B);
}
```

# 链表

## [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode newHead = new ListNode(0);
    ListNode p1 = l1, p2 = l2, curr = newHead;
    int carry = 0;
    while (p1 != null || p2 != null) {
        int x = (p1 != null) ? p1.val : 0;
        int y = (p2 != null) ? p2.val : 0;
        int sum = x + y + carry;
        carry = sum / 10;
        curr.next = new ListNode(sum % 10);
        curr = curr.next;
        if (p1 != null)
            p1 = p1.next;
        if (p2 != null)
            p2 = p2.next;
    }
    if (carry > 0) {
        curr.next = new ListNode(carry);
    }
    return newHead.next;
}
```

## [23. 合并K个排序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

### 优先队列

```java
public ListNode mergeKLists(ListNode[] lists) {
    if (lists == null || lists.length == 0) {
        return null;
    }
    PriorityQueue<ListNode> queue = new PriorityQueue<>(new Comparator<ListNode>() {
        public int compare(ListNode o1, ListNode o2) {
            return o1.val - o2.val;
        }
    });
    ListNode dummy = new ListNode(0);
    ListNode p = dummy;
    for (ListNode node : lists) {
        if (node != null) {
            queue.add(node);
        }
    }
    while (!queue.isEmpty()) {
        ListNode temp = queue.poll();
        p.next = temp;
        p = p.next;
        if (temp.next != null) {
            queue.add(temp.next);
        }
    }
    return dummy.next;
}
```

### 分治

```java
public ListNode mergeKLists(ListNode[] lists) {
    if (lists == null || lists.length == 0) {
        return null;
    }
    return hepler(lists, 0, lists.length - 1);
}
public ListNode hepler(ListNode[] lists, int l, int r) {
    if (l == r) {
        return lists[l];
    }
    int m = (l + r) / 2;
    ListNode a = hepler(lists, l, m);
    ListNode b = hepler(lists, m + 1, r);
    return merge(a, b);
}
public ListNode merge(ListNode l1, ListNode l2) {
    if (l1 == null) {
        return l2;
    }
    if (l2 == null) {
        return l1;
    }
    ListNode head = null;
    if (l1.val < l2.val) {
        head = l1;
        head.next = merge(l1.next, l2);
    } else {
        head = l2;
        head.next = merge(l1, l2.next);
    }
    return head;
}
```

## [25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

```java
public ListNode reverseKGroup(ListNode head, int k) {
    if (head == null) {
        return null;
    }
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode pre = dummy;
    ListNode end = dummy;
    while (end.next != null) {
        for (int i = 0; i < k && end != null; i++) {
            end = end.next;
        }
        if (end == null) {
            break;
        }
        ListNode start = pre.next;
        ListNode next = end.next;
        end.next = null;
        pre.next = reverse(start);
        start.next = next;
        pre = start;
        end = pre;
    }
    return dummy.next;
}
public ListNode reverse(ListNode head) {
    ListNode pre = null;
    while (head != null) {
        ListNode next = head.next;
        head.next = pre;
        pre = head;
        head = next;
    }
    return pre;
}
```

## [86. 分隔链表](https://leetcode-cn.com/problems/partition-list/)

```java
public ListNode partition(ListNode head, int x) {
    if (head == null) {
        return null;
    }
    ListNode l_head = new ListNode(0);
    ListNode r_head = new ListNode(0);
    ListNode l = l_head;
    ListNode r = r_head;
    while (head != null) {
        if (head.val < x) {
            l.next = head;
            l = l.next;
        } else {
            r.next = head;
            r = r.next;
        }
        head = head.next;
    }
    // 避免生成环形链表
    r.next = null;
    l.next = r_head.next;
    return l_head.next;
}
```

## [146. LRU 缓存机制](https://leetcode-cn.com/problems/lru-cache/)

### 基于 LinkedHashMap 实现

```java
import java.util.LinkedHashMap;
import java.util.Map;

public class LRUCache {
    private int capacity;
    private Map<Integer, Integer> cache;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.cache = new LinkedHashMap<Integer, Integer>(capacity, 0.75f, true) {
            protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
                return size() > capacity;
            }
        };
    }

    public int get(int key){
        if(cache.containsKey(key)){
            return cache.get(key);
        }else{
            return -1;
        }
    }

    public void put(int key, int value) {
        cache.put(key, value);
    }
}
```

### 基于 HashMap 和双向链表的实现

```java
class LRUCache {
    class Node {
        Node pre;
        Node next;
        Integer key;
        Integer val;

        Node(Integer k, Integer v) {
            key = k;
            val = v;
        }
    }

    Map<Integer, Node> map = new HashMap<>();
    Node head;
    Node tail;
    Integer capacity;

    LRUCache(int capacity) {
        this.capacity = capacity;
        head = new Node(null, null);
        tail = new Node(null, null);
        head.next = tail;
        tail.pre = head;
    }

    public int get(int k) {
        Node n = map.get(k);
        if (n != null) {
            remove(n);
            add(n);
            return n.val;
        }
        return -1;
    }

    public void put(int k, int v) {
        Node n = map.get(k);
        if (n != null) {
            n.val = v;
            map.put(k, n);
            remove(n);
            add(n);
        } else {
            n = new Node(k, v);
            add(n);
            map.put(k, n);
        }
        if (map.size() > capacity) {
            Node temp = tail.pre;
            remove(temp);
            map.remove(temp.key);
        }
    }

    private void add(Node n) {
        n.pre = head;
        n.next = head.next;
        head.next.pre = n;
        head.next = n;
    }

    private void remove(Node n) {
        n.pre.next = n.next;
        n.next.pre = n.pre;
    }
}

```

# 二叉树

## [199. 二叉树的右视图](https://leetcode-cn.com/problems/binary-tree-right-side-view/)

### 递归

```java
List<Integer> res = new ArrayList<>();
public List<Integer> rightSideView(TreeNode root) {
    if (root == null) {
        return res;
    }
    helper(root, 0);
    return res;
}
public void helper(TreeNode root, int now) {
    if (root == null) {
        return;
    }
    if (now == res.size()) {
        res.add(root.val);
    }
    helper(root.right, now + 1);
    helper(root.left, now + 1);
}
```

### BFS

```java
public List<Integer> rightSideView(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    if (root == null) {
        return res;
    }
    Deque<TreeNode> queue = new LinkedList<>();
    queue.add(root);
    while (!queue.isEmpty()) {
        int n = queue.size();
        for (int i = 0; i < n; i++) {
            TreeNode node = queue.pop();
            if (i == n - 1) {
                res.add(node.val);
            }
            if (node.left != null) {
                queue.add(node.left);
            }
            if (node.right != null) {
                queue.add(node.right);
            }
        }
    }
    return res;
}
```

## [94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

### 递归

```java
List<Integer> res = new ArrayList<>();
public List<Integer> inorderTraversal(TreeNode root) {
    if (root == null) {
        return res;
    }
    helper(root);
    return res;
}
public void helper(TreeNode root) {
    if (root == null) {
        return;
    }
    if (root.left != null) {
        helper(root.left);
    }
    res.add(root.val);
    if (root.right != null) {
        helper(root.right);
    }
}
```

### 迭代

```java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    if (root == null) {
        return res;
    }
    Deque<TreeNode> stack = new LinkedList<>();
    while (root != null || !stack.isEmpty()) {
        while (root != null) {
            stack.push(root);
            root = root.left;
        }
        root = stack.pop();
        res.add(root.val);
        root = root.right;
    }
    return res;
}
```