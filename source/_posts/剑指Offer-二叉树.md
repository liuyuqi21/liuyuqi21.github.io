---
title: 剑指Offer-二叉树相关
date: 2018-09-11 09:40:18
tags: 剑指Offer
categories: 算法
---
## [7. 重建二叉树](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/)
[leetcode 105](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
```java
public TreeNode reConstructBinaryTree(int[] pre, int[] in) {
    if (pre == null || in == null) {
        return null;
    }
    return func(pre, 0, pre.length - 1, in, 0, in.length - 1);
}
public TreeNode func(int[] pre, int startPre, int endPre, int[] in, int startIn, int endIn) {
    if (startPre > endPre || startIn > endIn)
        return null;
    TreeNode node = new TreeNode(pre[startPre]);
    for (int i = startIn; i <= endIn; i++) {
        if (in[i] == pre[startPre]) {
            node.left = func(pre, startPre + 1, i - startIn + startPre, in, startIn, i - 1);
            node.right = func(pre, i - startIn + startPre + 1, endPre, in, i + 1, endIn);
            break;
        }
    }
    return node;
}
```

## 8. 二叉树的下一个结点
[牛客](https://www.nowcoder.com/practice/9023a0c988684a53960365b889ceaf5e?tpId=13&tqId=11210&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)
```java
public TreeLinkNode GetNext(TreeLinkNode pNode) {
    if (pNode == null) {
        return null;
    }
    if (pNode.right != null) {
        pNode = pNode.right;
        while (pNode.left != null) {
            pNode = pNode.left;
        }
        return pNode;
    }
    while (pNode.next != null && pNode.next.left != pNode) {
        pNode = pNode.next;
    }
    return pNode.next;
}
```

## [26. 树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)
[leetcode 572](https://leetcode-cn.com/problems/subtree-of-another-tree/)
```java
public boolean isSubtree(TreeNode s, TreeNode t) {
    if (s == null || t == null) {
        return false;
    }
    return helper(s, t) || isSubtree(s.left, t) || isSubtree(s.right, t);
}
public boolean helper(TreeNode s, TreeNode t) {
    if (t == null && s == null) {
        return true;
    }
    if (t == null || s == null) {
        return false;
    }
    if (s.val != t.val) {
        return false;
    }
    return helper(s.left, t.left) && helper(s.right, t.right);
}
```

## [27. 二叉树的镜像](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/)
[leetcode 226](https://leetcode-cn.com/problems/invert-binary-tree/)
```java
public TreeNode invertTree(TreeNode root) {
    if (root == null) {
        return null;
    }
    if (root.left != null) {
        invertTree(root.left);
    }
    if (root.right != null) {
        invertTree(root.right);
    }
    TreeNode node = root.left;
    root.left = root.right;
    root.right = node;
    return root;
}
```

## [28. 对称的二叉树](https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/)
[leetcode 101](https://leetcode-cn.com/problems/symmetric-tree/)
```java
public boolean isSymmetric(TreeNode root) {
    if (root == null) {
        return true;
    }
    return helper(root.left, root.right);
}
public boolean helper(TreeNode l, TreeNode r) {
    if (r == null) {
        return l == r;
    }
    if (l == null) {
        return false;
    }
    if (l.val != r.val) {
        return false;
    }
    return helper(l.left, r.right) && helper(l.right, r.left);
}
```

## [32.1 从上往下打印二叉树](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)
```java
public List<Integer> PrintFromTopToBottom(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    if (root == null) {
        return res;
    }
    Deque<TreeNode> nodes = new LinkedList<>();
    nodes.add(root);
    while (!nodes.isEmpty()) {
        TreeNode node = nodes.pop();
        res.add(node.val);
        if (node.left != null) {
            nodes.add(node.left);
        }
        if (node.right != null) {
            nodes.add(node.right);
        }
    }
    return res;
}
```

## [32.2 分行从上到下打印二叉树](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)
[leetcode 102](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> res = new ArrayList<>();
    if (root == null) {
        return res;
    }
    LinkedList<TreeNode> nodes = new LinkedList<>();
    nodes.add(root);
    while (!nodes.isEmpty()) {
        int size = nodes.size();
        List<Integer> line = new ArrayList<>();
        for (int i = 0; i < size; i++) {
            TreeNode node = nodes.pop();
            line.add(node.val);
            if (node.left != null) {
                nodes.add(node.left);
            }
            if (node.right != null) {
                nodes.add(node.right);
            }
        }
        res.add(line);
    }
    return res;
}
```

## [32.3 之字形打印二叉树](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)
[leetcode 103](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)
```java
public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
    List<List<Integer>> ret = new LinkedList<>();
    if (root == null)
        return ret;
    LinkedList<TreeNode> queue = new LinkedList<>();
    queue.push(root);
    boolean startLeft = true;
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new LinkedList<>();
        for (int i = 0; i < size; i++) {
            TreeNode n = queue.removeLast();
            if (n.left != null)
                queue.push(n.left);
            if (n.right != null)
                queue.push(n.right);
            if (startLeft)
                level.add(n.val);
            else
                level.add(0, n.val);
        }
        ret.add(level);
        startLeft = !startLeft;
    }
    return ret;
}
```

## [33. 二叉搜索树的后序遍历序列](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)
```java
public boolean VerifySquenceOfBST(int[] sequence) {
    if (sequence == null || sequence.length == 0) {
        return false;
    }
    if (sequence.length == 1) {
        return true;
    }
    return Verify(sequence, 0, sequence.length - 1);
}
public boolean Verify(int[] sequence, int l, int r) {
    if (l >= r) {
        return true;
    }
    int i = l;
    while (sequence[i] < sequence[r]) {
        i++;
    }
    for (int j = i; j < r; j++) {
        if (sequence[j] < sequence[r]) {
            return false;
        }
    }
    return Verify(sequence, l, i - 1) && Verify(sequence, i, r - 1);
}
```

## [34. 二叉树中和为某一值的路径](https://leetcode-cn.com/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)
[leetcode 113](https://leetcode-cn.com/problems/path-sum-ii)
```java
List<List<Integer>> res = new LinkedList<>();
LinkedList<Integer> path = new LinkedList<>();
public List<List<Integer>> pathSum(TreeNode root, int sum) {
    helper(root, sum);
    return res;
}
public void helper(TreeNode root, int sum) {
    if (root == null) {
        return;
    }
    sum -= root.val;
    path.add(root.val);
    if (sum == 0 && root.left == null && root.right == null) {
        res.add(new LinkedList<>(path));
    } else {
        helper(root.left, sum);
        helper(root.right, sum);
    }
    path.removeLast();
}
```
相关题目：[leetcode 437](https://leetcode-cn.com/problems/path-sum-iii/)

## [36. 二叉搜索树与双向链表](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)
```java
public TreeNode last = null;
public TreeNode Convert(TreeNode pRootOfTree) {
    if (pRootOfTree == null) {
        return null;
    }
    helper(pRootOfTree);
    while (last != null && last.left != null) {
        last = last.left;
    }
    return last;
}
public void helper(TreeNode root) {
    if (root == null) {
        return;
    }
    helper(root.left);
    root.left = last;
    if (last != null) {
        last.right = root;
    }
    last = root;
    helper(root.right);
}
```
相关题目：
- 有序链表转换二叉搜索树 [leetcode 109](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/)
- 二叉树展开为链表 [leetcode 114](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

## [37. 序列化二叉树](https://leetcode-cn.com/problems/xu-lie-hua-er-cha-shu-lcof/)
[leetcode 297](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)
```java
public String serialize(TreeNode root) {
    StringBuilder res = strHelp(root, new StringBuilder());
    return res.toString();
}
public StringBuilder strHelp(TreeNode root, StringBuilder str) {
    if (root == null) {
        str.append("#,");
        return str;
    }
    str.append(root.val);
    str.append(",");
    strHelp(root.left, str);
    strHelp(root.right, str);
    return str;
}
public TreeNode deserialize(String data) {
    String[] strWord = data.split(",");
    Deque<String> listWord = new LinkedList<>(Arrays.asList(strWord));
    return deserHelp(listWord);
}
public TreeNode deserHelp(Deque<String> deque) {
    if (deque.peek().equals("#")) {
        deque.pop();
        return null;
    }
    TreeNode res = new TreeNode(Integer.valueOf(deque.pop()));
    res.left = deserHelp(deque);
    res.right = deserHelp(deque);
    return res;
}
```
相关题目：
- 先序遍历构造二叉树 [leetcode 1008](https://leetcode-cn.com/problems/construct-binary-search-tree-from-preorder-traversal/)
- 序列化和反序列化二叉搜索树 [leetcode 449](https://leetcode-cn.com/problems/serialize-and-deserialize-bst/)

## [54. 二叉搜索树的第K大节点](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)
[leetcode 230](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst)
```java
int res, cnt;
public int kthSmallest(TreeNode root, int k) {
    cnt = k;
    helper(root);
    return res;
}
public void helper(TreeNode root) {
    if (root == null) {
        return;
    }
    helper(root.left);
    cnt--;
    if (cnt == 0) {
        res = root.val;
        return;
    }
    helper(root.right);
}
```

## [55.1 二叉树的深度](https://leetcode-cn.com/problems/er-cha-shu-de-shen-du-lcof/)
[leetcode 104](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)
```java
public int maxDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }
    int left = maxDepth(root.left);
    int right = maxDepth(root.right);
    return left > right ? left + 1 : right + 1;
}
```

## [55.2 平衡二叉树](https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/)
[leetcode 110](https://leetcode-cn.com/problems/balanced-binary-tree/)
```java
boolean res = true;
public boolean isBalanced(TreeNode root) {
    helper(root);
    return res;
}
public int helper(TreeNode root) {
    if (root == null) {
        return 0;
    }
    int left = helper(root.left);
    int right = helper(root.right);
    int diff = left - right;
    if (diff > 1 || diff < -1) {
        res = false;
    }
    return left > right ? left + 1 : right + 1;
}
```

## [69.1 二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/)
[leetocde 235](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)
```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || p == root || q == root) {
        return root;
    }
    while (root != null) {
        if (root.val > p.val && root.val > q.val) {
            root = root.left;
        } else if (root.val < p.val && root.val < q.val) {
            root = root.right;
        } else {
            return root;
        }
    }
    return null;
}
```

## [69.2 二叉树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/)
[leetcode 236](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

### 递归
```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || p == root || q == root) {
        return root;
    }
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    if (left != null && right != null) {
        return root;
    }
    if (left != null) {
        return left;
    }
    if (right != null) {
        return right;
    }
    return null;
}
```

### 求出根节点到指定节点的路径再求出路径的相交点
```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null) {
        return null;
    }
    if (root == p || root == q || (root.left == p && root.right == q)) {
        return root;
    }
    LinkedList<TreeNode> pathP = new LinkedList<>();
    LinkedList<TreeNode> pathQ = new LinkedList<>();
    getPath(root, p, pathP);
    getPath(root, q, pathQ);
    TreeNode last = null;
    for (int i = 0; i < pathP.size() && i < pathQ.size(); i++) {
        if (pathP.get(i) == pathQ.get(i)) {
            last = pathP.get(i);
        } else {
            break;
        }
    }
    return last;
}
public boolean getPath(TreeNode root, TreeNode p, LinkedList<TreeNode> path) {
    if (root == null) {
        return false;
    }
    path.add(root);
    if (root == p || getPath(root.left, p, path) || getPath(root.right, p, path)) {
        return true;
    }
    path.removeLast();
    return false;
}
```
