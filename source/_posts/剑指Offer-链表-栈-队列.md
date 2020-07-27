---
title: 剑指Offer-链表相关
date: 2018-06-10 20:19:32
tags: 剑指Offer
categories: 算法
---
# 链表

## [6. 从尾到头打印链表](https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)
```java
public int[] reversePrint(ListNode head) {
    if (head == null) {
        return new int[0];
    }
    LinkedList<Integer> stack = new LinkedList<>();
    while (head != null) {
        stack.push(head.val);
        head = head.next;
    }
    int size = stack.size();
    int[] res = new int[size];
    for (int i = 0; i < size; i++) {
        res[i] = stack.pop();
    }
    return res;
}
```

## [18.1 删除链表的节点](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)
```java
public ListNode deleteNode(ListNode head, int val) {
    if (head == null) {
        return null;
    }
    ListNode res = head;
    if (head.val == val) {
        return head.next;
    }
    while (head != null && head.next != null) {
        if (head.next.val == val) {
            head.next = head.next.next;
        }
        head = head.next;
    }
    return res;
}
```

### 原题
```java
public ListNode deleteNode(ListNode head, ListNode tobeDelete) {
    if (head == null || tobeDelete == null)
        return null;
    if (tobeDelete.next != null) {
        ListNode next = tobeDelete.next;
        tobeDelete.val = next.val;
        tobeDelete.next = next.next;
    } else {
        if (head == tobeDelete)
            head = null;
        else {
            ListNode cur = head;
            while (cur.next != tobeDelete)
                cur = cur.next;
            cur.next = null;
        }
    }
    return head;
}
```
- 相似题目：[leetcode 237](https://leetcode-cn.com/problems/delete-node-in-a-linked-list)

## 18.2 删除链表中重复的节点
[leetcode 82](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii)

### 递归
```java
public ListNode deleteDuplicates(ListNode head) {
    if (head == null || head.next == null) {
        return head;
    }
    ListNode next = head.next;
    if (head.val == next.val) {
        while (next != null && head.val == next.val) {
            next = next.next;
        }
        return deleteDuplicates(next);
    } else {
        head.next = deleteDuplicates(head.next);
        return head;
    }
}
```

### 非递归
```java
public ListNode deleteDuplicates(ListNode head) {
    if (head == null) {
        return null;
    }
    ListNode node = head;
    ListNode pre = new ListNode(0);
    pre.next = head;
    ListNode newHead = pre;
    while (node != null) {
        if (node.next != null && node.val == node.next.val) {
            int val = node.val;
            while (node != null && node.val == val) {
                node = node.next;
            }
            pre.next = node;
        } else {
            pre = node;
            node = node.next;
        }
    }
    return newHead.next;
}
```

## [22. 链表中倒数第k个节点](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)
```java
public ListNode FindKthToTail(ListNode head, int k) {
    if (head == null || k <= 0) {
        return null;
    }
    ListNode node = head;
    for (int i = 0; i < k - 1; i++) {
        node = node.next;
        if (node == null) {
            return null;
        }
    }
    ListNode res = head;
    while (node.next != null) {
        res = res.next;
        node = node.next;
    }
    return res;
}
```

## 23. 链表中环的入口节点
[leetcode 142](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
```java
public ListNode detectCycle(ListNode head) {
    if (head == null || head.next == null || head.next == null) {
        return null;
    }
    ListNode one = head.next;
    ListNode two = head.next.next;
    while (one != two) {
        if (two == null || two.next == null) {
            return null;
        }
        one = one.next;
        two = two.next.next;
    }
    one = head;
    while (one != two) {
        one = one.next;
        two = two.next;
    }
    return one;
}
```
- 相关题目：[leetcode 287](https://leetcode-cn.com/problems/find-the-duplicate-number/)

## [24. 反转链表](https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/)
[leetcode 206](https://leetcode-cn.com/problems/reverse-linked-list)
```java
public ListNode reverseList(ListNode head) {
    if (head == null) {
        return null;
    }
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

## [25. 合并两个排序链表](https://leetcode-cn.com/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/)
[leetcode 21](https://leetcode-cn.com/problems/merge-two-sorted-lists)

### 递归
```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    if (l1 == null) {
        return l2;
    }
    if (l2 == null) {
        return l1;
    }
    ListNode head = null;
    if (l1.val < l2.val) {
        head = l1;
        head.next = mergeTwoLists(l1.next, l2);
    } else {
        head = l2;
        head.next = mergeTwoLists(l1, l2.next);
    }
    return head;
}
```

### 非递归
```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    if (l1 == null) {
        return l2;
    }
    if (l2 == null) {
        return l1;
    }
    ListNode head = new ListNode(0);
    ListNode res = head;
    while (l1 != null && l2 != null) {
        if (l1.val < l2.val) {
            head.next = l1;
            l1 = l1.next;
        } else {
            head.next = l2;
            l2 = l2.next;
        }
        head = head.next;
    }
    if (l1 != null) {
        head.next = l1;
    }
    if (l2 != null) {
        head.next = l2;
    }
    return res.next;
}
```

## [35. 复杂链表的复制](https://leetcode-cn.com/problems/fu-za-lian-biao-de-fu-zhi-lcof/)
[leetcode 138](https://leetcode-cn.com/problems/copy-list-with-random-pointer)

### 使用 map
```java
public Node copyRandomList(Node head) {
    if (head == null) {
        return null;
    }
    Node node = head;
    Map<Node, Node> map = new HashMap<>();
    while (node != null) {
        Node clone = new Node(node.val, null, null);
        map.put(node, clone);
        node = node.next;
    }
    node = head;
    while (node != null) {
        map.get(node).next = map.get(node.next);
        map.get(node).random = map.get(node.random);
        node = node.next;
    }
    return map.get(head);
}
```

## 原地复制
```java
public Node copyRandomList(Node head) {
    if (head == null) {
        return null;
    }
    Node node = head;
    while (node != null) {
        Node copy = new Node(node.val);
        copy.next = node.next;
        node.next = copy;
        node = copy.next;
    }
    node = head;
    while (node != null) {
        if (node.random != null) {
            node.next.random = node.random.next;
        }
        node = node.next.next;
    }
    node = head;
    Node copy = head.next;
    while (node.next != null) {
        Node temp = node.next;
        node.next = temp.next;
        node = temp;
    }
    return copy;
}
```

## [52. 两个链表的第一个公共节点](https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)
[leetcode 160](https://leetcode-cn.com/problems/intersection-of-two-linked-lists)
```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    if (headA == null || headB == null) {
        return null;
    }
    int lenA = getLen(headA);
    int lenB = getLen(headB);
    if (lenA > lenB) {
        int diff = lenA - lenB;
        for (int i = 0; i < diff; i++) {
            headA = headA.next;
        }
    } else {
        int diff = lenB - lenA;
        for (int i = 0; i < diff; i++) {
            headB = headB.next;
        }
    }
    while (headA != headB) {
        headA = headA.next;
        headB = headB.next;
    }
    return headA;
}
public int getLen(ListNode node) {
    int n = 0;
    while (node != null) {
        n++;
        node = node.next;
    }
    return n;
}
```

# 栈和队列

## [9. 用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)
[leetcode 232](https://leetcode-cn.com/problems/implement-queue-using-stacks/)
```java
class MyQueue {
    LinkedList<Integer> s1;
    LinkedList<Integer> s2;
    public MyQueue() {
        s1 = new LinkedList<>();
        s2 = new LinkedList<>();
    }

    public void push(int x) {
        s2.push(x);
    }

    public int pop() {
        if (s1.isEmpty()) {
            while (!s2.isEmpty()) {
                s1.push(s2.pop());
            }
        }
        return s1.pop();
    }

    public int peek() {
        if (s1.isEmpty()) {
            while (!s2.isEmpty()) {
                s1.push(s2.pop());
            }
        }
        return s1.peek();
    }

    public boolean empty() {
        return s1.isEmpty() && s2.isEmpty();
    }
}
```

## [30. 包含 min 函数的栈](https://leetcode-cn.com/problems/bao-han-minhan-shu-de-zhan-lcof/)
[leetcode 115](https://leetcode-cn.com/problems/min-stack/)
```java
class MinStack {
    LinkedList<Integer> s1;
    LinkedList<Integer> s2;
    
    public MinStack() {
        s1 = new LinkedList<>();
        s2 = new LinkedList<>();
    }

    public void push(int x) {
        s1.push(x);
        if (s2.isEmpty() || x < s2.peek()) {
            s2.push(x);
        } else {
            s2.push(s2.peek());
        }
    }

    public void pop() {
        s1.pop();
        s2.pop();
    }

    public int top() {
        return s1.peek();
    }

    public int getMin() {
        return s2.peek();
    }
}
```

## [31. 栈的压入、弹出序列](https://leetcode-cn.com/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/)
[leetcode 946](https://leetcode-cn.com/problems/validate-stack-sequences/)
```java
public boolean validateStackSequences(int[] pushed, int[] popped) {
    if (pushed == null || popped == null || pushed.length != popped.length) {
        return false;
    }
    LinkedList<Integer> stack = new LinkedList<>();
    int j = 0;
    for (int i = 0; i < pushed.length; i++) {
        stack.push(pushed[i]);
        while (!stack.isEmpty() && stack.peek() == popped[j]) {
            stack.pop();
            j++;
        }
    }
    return stack.isEmpty();
}
```