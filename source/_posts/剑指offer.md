---
title: 剑指Offer-二叉树相关
date: 2019-09-16 19:33:06
tags: 剑指Offer
categories: 算法
---

# 剑指offer

## 3.1 数组中重复的数字
```cpp
bool duplicate(int numbers[], int length, int* duplication) {
	if (numbers == nullptr || length <= 0) {
		return false;
	}
	for (int i = 0; i < length; i++) {
		if (numbers[i] != i) {
			int temp = numbers[i];
			if (numbers[temp] == temp) {
				*duplication = temp;
				return true;
			}
			numbers[i] = numbers[temp];
			numbers[temp] = temp;
		}
	}
	return false;
}
```

## 3.2 不修改数组找出重复的数字
```cpp
bool duplicate(int numbers[], int length, int* duplication) {
	if (numbers == nullptr || length <= 0) {
		return false;
	}
	int start = 1;
	int end = length - 1;
	while (start <= end) {
		int middle = (end - start) / 2 + start;
		int count = 0;
		count = countRange(numbers, length, start, middle);
		if (start == end) {
			if (count > 1) {
				*duplication = start;
				return true;
			}
			else {
				break;
			}
		}
		if (count > (middle - start + 1)) {
			end = middle;
		}
		else {
			start = middle + 1;
		}
	}
	return false;
}
int countRange(int numbers[], int length, int start, int end) {
	int count = 0;
	for (int i = 0; i<length; i++) {
		if (numbers[i] <= end&&numbers[i] >= start) {
			count++;
		}
	}
	return count;
}
```

## 4. 二维数组中的查找
```cpp
bool Find(int target, vector<vector<int>> array) {
	int rows = array.size();
	int cols = array[0].size();
	int row = 0;
	int col = cols - 1;
	while (col >= 0 && row < rows)
		if (array[row][col] > target) {
			col--;
		}
		else if (array[row][col] < target) {
			row++;
		}
		else {
			return true;
		}
		return false;
}
```

## 5.替换空格
```cpp
void replaceSpace(char str[], int length) {
	if (str == nullptr || length <= 0) {
		return;
	}
	int blank = 0;
	int len = 0;
	while (str[len] != '\0') {
		if (str[len] == ' ') {
			blank++;
		}
		len++;
	}
	int newLen = len + blank * 2;
	if (newLen > length) {
		return;
	}
	while (length > 0 && newLen > len)
	{
		if (str[len] == ' ') {
			str[newLen--] = '0';
			str[newLen--] = '2';
			str[newLen--] = '%';
			len--;
		}
		else {
			str[newLen--] = str[len--];
		}
	}
}
```

## 6. 从尾到头打印链表

### 使用栈
```cpp
vector<int> printListFromTailToHead(ListNode *head)
{
    vector<int> list;
    stack<ListNode *> s;
    while (head != nullptr)
    {
        s.push(head);
        head = head->next;
    }
    while (!s.empty())
    {
        ListNode *node;
        node = s.top();
        list.push_back(node->val);
        s.pop();
    }
    return list;
}
```

### 递归
```cpp
vector<int> printListFromTailToHead(ListNode *head)
{
    vector<int> list;
    if (head != nullptr)
    {
        list = printListFromTailToHead(head->next);
        list.push_back(head->val);
    }

    return list;
}
```

## 7. 重建二叉树
```cpp
 public TreeNode reConstructBinaryTree(int [] pre,int [] in) {
        if(pre==null||in==null){
            return null;
        }
        return func(pre,0,pre.length-1,in,0,in.length-1);
    }
    public TreeNode func(int []pre,int startPre,int endPre,int []in,int startIn,int endIn){
        if(startPre>endPre||startIn>endIn)
            return null;
        TreeNode node=new TreeNode(pre[startPre]);
        for(int i=startIn;i<=endIn;i++){
            if(in[i]==pre[startPre]){
                node.left=func(pre,startPre+1,i-startIn+startPre,in,startIn,i-1);
                node.right=func(pre,i+1-startIn+startPre,endPre,in,i+1,endIn);
                break;
            }
        }
        
       return node;
    }
```

## 8. 二叉树的下一个结点
```cpp
 TreeLinkNode* GetNext(TreeLinkNode* pNode) {
        if (pNode == nullptr) {
            return nullptr;
        }
        if (pNode->right) {
            pNode = pNode->right;
            while (pNode->left) {
                pNode = pNode->left;
            }
            return pNode;
        }
        else {
            while (pNode->next) {
                if (pNode->next->left == pNode) {
                    return pNode->next;
                }	
                pNode = pNode->next;
            }
            return nullptr;
        }
    }
```

## 9. 用两个栈实现队列

## 10 斐波那契数列
```cpp
int Fibonacci(int n) {
	if (n < 2) {
		return n;
	}
	int one = 1;
	int two = 0;
	int res = 0;
	for (int i = 2; i <= n; i++) {
		res = one + two;
		two = one;
		one = res;
	}
	return res;
}
```

### 青蛙跳台阶问题
```cpp
int jumpFloor(int number) {
	if (number < 4) {
		return number;
	}
	int one = 3;
	int two = 2;
	int n = 0;
	for (int i = 4; i <= number; i++) {
		n = one + two;
		two = one;
		one = n;
	}
	return n;
}
```

## 11. 旋转数组的最小数字
```cpp
int minNumberInRotateArray(vector<int> rotateArray)
{
	int length = rotateArray.size();
	if (length == 0)
	{
		return 0;
	}
	int left = 0;
	int right = length - 1;
	int mid = left;
	while (rotateArray[right] <= rotateArray[left])
	{
		if (right - left == 1)
		{
			mid = right;
			break;
		}
		mid = (left + right) / 2;
		if (rotateArray[mid] >= rotateArray[left])
			left = mid;
		else
			right = mid;
	}
	return rotateArray[mid];
}
```

## 12. 矩阵中的路径

## 13. 机器人的运动范围

## 14. 剪绳子
### 动态规划
```cpp
int cutRope(int number) {
	if (number == 0)
	{
		return 0;
	}
	if (number <= 2)
	{
		return 1;
	}
	if (number == 3)
	{
		return 2;
	}
	int* dp = new int[number + 1];
	dp[0] = 0;
	dp[1] = 1;
	dp[2] = 2;
	dp[3] = 3;
	int max = 0;
	for (int i = 4; i <= number; i++) {
		for (int j = 1; j <= number / 2; j++) {
			int n = dp[j] * dp[i - j];
			if (n > max)
				max = n;
		}
		dp[i] = max;
	}
	return dp[number];
}
```
### 贪心
```cpp
int cutRope(int number) {
	if (number == 0)
	{
		return 0;
	}
	if (number <= 2)
	{
		return 1;
	}
	if (number == 3)
	{
		return 2;
	}
	int cntThree = number / 3;
	int max;
	if (number % 3 == 1) {
		cntThree--;
		max = pow(3, cntThree) * 4;
	}
	else if (number % 3 == 2) {
		max = pow(3, cntThree) * 2;
	}
	else {
		max = pow(3, cntThree);
	}
	return max;
}
```

## 15. 二进制中 1 的个数
```cpp
int  NumberOf1(int n) {
	int count = 0;
	while (n) {
		count++;
		n = (n - 1) & n;
	}
	return count;
}
```

## 16. 数值的整数次方

## 17. 打印从 1 到 最大的 n 位数

## 18.1 删除链表的节点
```cpp
void DeleteNode(ListNode **pListHead, ListNode *pToBeDeleted) {
       if (pListHead == nullptr || pToBeDeleted == nullptr) {
              return;
       }
       if (pToBeDeleted->next != nullptr) {
              pToBeDeleted->val = pToBeDeleted->next->val;
              pToBeDeleted->next = pToBeDeleted->next->next;
       }
       else if (*pListHead==pToBeDeleted) {
              delete pToBeDeleted;
              pToBeDeleted = nullptr;
              *pListHead = nullptr;
       }
       else {
              ListNode *pNode = *pListHead;
              while (pNode->next != pToBeDeleted) {
                     pNode = pNode->next;
              }
              pNode->next = nullptr;
              delete pToBeDeleted;
              pToBeDeleted = nullptr;
       }
}
```

## 18.2 删除链表中重复的节点

### 递归
```cpp
ListNode* deleteDuplication(ListNode* pHead){
     if(pHead==nullptr||pHead->next==nullptr){
            return pHead;
        }
        ListNode* next=pHead->next;
        if(pHead->val==next->val){
            while(next!=nullptr&&pHead->val==next->val){
                 next=next->next;
            }
            return deleteDuplication(next);
        }else{
            pHead->next=deleteDuplication(pHead->next);
            return pHead;
        }
}
```

### 非递归
```cpp
 ListNode* deleteDuplication(ListNode* pHead)
    {
        if (pHead == nullptr || pHead->next == nullptr) {
            return pHead;
        }
        ListNode* newHead = new ListNode(-1);
        newHead->next = pHead;
        ListNode* pNode = pHead;
        ListNode* preNode = newHead;
        while (pNode != nullptr) {
            ListNode* pNext = pNode->next;
            if (pNext != nullptr&&pNext->val == pNode->val) {
                int value = pNode->val;
                while (pNode != nullptr&&pNode->val == value) {
                    pNode = pNode->next;
                }
                preNode->next=pNode;
            }	
            else {
                preNode = pNode;
                pNode = pNode->next;
            }
        }
        return newHead->next;
    }
```

## 19. 正则表达式匹配

## 20. 表达数值的字符串

## 21.1 调整数组的顺序使奇数位于偶数前面
```cpp
void reOrderArray(vector<int> &array) {
	if (array.size() <= 1) {
		return;
	}
	int begin = 0;
	int end = array.size() - 1;
	while (begin < end) {
		while (begin < end && (array[begin] & 1) != 0) {
			begin++;
		}
		while (begin < end && (array[end] & 1) == 0) {
			end--;
		}
		if (begin < end) {
			int temp = array[begin];
			array[begin] = array[end];
			array[end] = temp;
		}
	}
}
```

## 21.2 保证元素相对位置不变
```cpp
void reOrderArray(vector<int> &array) {
	if (array.size() <= 1) {
		return;
	}
	int oddCnt = 0;
	for (int i = 0; i < array.size(); i++) {
		if ((array[i] & 1) != 0) {
			oddCnt++;
		}
	}
	int i = 0;
	vector<int> copy(array);
	for (int num : copy) {
		if ((num & 1) != 0) {
			array[i++] = num;
		}
		else {
			array[oddCnt++] = num;
		}
	}	
}
```

## 22. 链表中倒数第k个节点
```cpp
ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
	if (pListHead == nullptr || k == 0) {
		return nullptr;
	}
	ListNode* first = pListHead;
	ListNode* second = pListHead;
	for (int i = 0; i < k - 1; i++) {
		if (second->next != nullptr) {
			second = second->next;
		}
		else {
			return nullptr;
		}
	}
	while (second->next != nullptr) {
		first = first->next;
		second = second->next;
	}
	return first;
}
```

## 23. 链表中环的入口节点
```cpp
if (pHead == nullptr || pHead->next == nullptr || pHead->next->next == nullptr)
              return nullptr;
       ListNode* fast = pHead->next->next;
       ListNode* slow = pHead->next;
       while (fast != slow) {
              if (fast->next != nullptr&& fast->next->next != nullptr) {
                     fast = fast->next->next;
                     slow = slow->next;
              }
              else {
                     return nullptr;
              }
       }
       fast = pHead;
       while (fast != slow) {
              fast = fast->next;
              slow = slow->next;
       }
       return slow;
```

## 24. 反转链表
```cpp
ListNode* ReverseList(ListNode* pHead) {
       if (pHead == nullptr) {
              return nullptr;
       }
       ListNode* pre = nullptr;
       ListNode* next;
       ListNode* pNode = pHead;
       while (pNode!=nullptr) {
              next = pNode->next;
              pNode->next = pre;
              pre = pNode;
              pNode = next;
       }
       return pre;
}
```

## 25. 合并两个排序链表

### 递归
```cpp
ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
{
       if (!pHead1) {
              return pHead2;
       }else if (!pHead2) {
              return pHead1;
       }
       ListNode* pHead = nullptr;
       if (pHead1->val < pHead2->val) {
              pHead = pHead1;
              pHead->next = Merge(pHead1->next, pHead2);
       }
       else {
              pHead = pHead2;
              pHead->next = Merge(pHead1, pHead2->next);
       }
       return pHead;
}
```

### 非递归
```cpp
ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
{
	if (pHead1 == nullptr) {
		return pHead2;
	}
	if (pHead2 == nullptr) {
		return pHead1;
	}
	ListNode* newHead;
	if (pHead1->val < pHead2->val) {
		newHead = pHead1;
		pHead1 = pHead1->next;
	}
	else {
		newHead = pHead2;
		pHead2 = pHead2->next;
	}
	ListNode* node = newHead;
	while (pHead1 != nullptr && pHead2 != nullptr) {
		if (pHead1->val < pHead2->val) {
			node->next = pHead1;
			pHead1 = pHead1->next;
		}
		else {
			node->next = pHead2;
			pHead2 = pHead2->next;
		}
		node = node->next;
	}
	if (pHead1 != nullptr) {
		node->next = pHead1;
	}
	if (pHead2 != nullptr) {
		node->next = pHead2;
	}
	return newHead;
}
```

## 26. 树的子结构
```cpp
bool HasSubtree(TreeNode* pRoot1, TreeNode* pRoot2)
{
	if (pRoot1 == nullptr || pRoot2 == nullptr) {
		return false;
	}
	bool result = false;

	if (pRoot1->val == pRoot2->val) {
		result = isSubTree(pRoot1, pRoot2);
	}
	if (!result) {
		result = HasSubtree(pRoot1->left, pRoot2);
	}
	if (!result) {
		result = HasSubtree(pRoot1->right, pRoot2);
	}
	return result;
}
bool isSubTree(TreeNode* pRoot1, TreeNode* pRoot2) {
	if (pRoot2 == nullptr) {
		return true;
	}
	if (pRoot1 == nullptr) {
		return false;
	}
	if (pRoot1->val == pRoot2->val) {
		return isSubTree(pRoot1->left, pRoot2->left) && isSubTree(pRoot1->right, pRoot2->right);
	}
	else {
		return false;
	}
}
```

## 27. 二叉树的镜像
```cpp
 void Mirror(TreeNode *pRoot) {
        if (pRoot == nullptr) {
            return;
        }
        if (pRoot->left) {
            Mirror(pRoot->left);
        }
        if (pRoot->right) {
            Mirror(pRoot->right);
        }
        TreeNode* p = pRoot->left;
        pRoot->left = pRoot->right;
        pRoot->right = p;
    }
```

## 28. 对称的二叉树
```cpp
bool isSymmetrical(TreeNode* pRoot) {
       if (pRoot == nullptr) {
              return true;
       }
       return isSymmetrical(pRoot->left, pRoot->right);
}
bool isSymmetrical(TreeNode* p1, TreeNode* p2) {
       if (p1 == nullptr)
              return p1 == p2;
       if (p2 == nullptr)
              return false;
       if (p1->val != p2->val)
              return false;
       return isSymmetrical(p1->left, p2->right) && isSymmetrical(p1->right, 
p2->left);
}
```

## 29. 顺时针打印矩阵

## 30. 包含 min 函数的栈
```cpp
stack<int> m_data, m_min;
void push(int value) {
	m_data.push(value);
	if (m_min.empty()||value<m_min.top()) {
		m_min.push(value);
	}
	else {
		m_min.push(m_min.top());
	}
}
void pop() {
	m_data.pop();
	m_min.pop();
}
int top() {
	return m_data.top();
}
int min() {
	return m_min.top();
}
```

## 31. 栈的压入、弹出序列

## 32.1 从上往下打印二叉树
```cpp
vector<int> PrintFromTopToBottom(TreeNode* root) {
        vector<int> list;
        if (root==nullptr) {
            return list;
        }
        queue<TreeNode*> nodes;
        nodes.push(root);
        while (nodes.size()) {
            TreeNode* p = nodes.front();
            list.push_back(p->val);
            nodes.pop();
            if (p->left) {
                nodes.push(p->left);
            }
            if (p->right) {
                nodes.push(p->right);
            }
        }
        return list;
    }
```

## 32.2 分行从上到下打印二叉树
```cpp
vector<vector<int> > Print(TreeNode* pRoot) {
        vector<vector<int>> print;
        if (pRoot == nullptr) {
            return print;
        }
        vector<int> line;
        queue<TreeNode*> nodes;
        nodes.push(pRoot);
        while (nodes.size()) {
            int size = nodes.size();
            while (size != 0) {
                TreeNode* p = nodes.front();
                line.push_back(p->val);
                nodes.pop();
                size--;
                if (p->left) {
                    nodes.push(p->left);
                }
                if (p->right) {
                    nodes.push(p->right);
                }
            }
            print.push_back(line);
            line.clear();
        }
        return print;
    }
```
### 用两个队列
```cpp
vector<vector<int> > Print(TreeNode* pRoot) {
	vector<vector<int> > res;
	if (pRoot == nullptr) {
		return res;
	}
	queue<TreeNode*> q1;
	queue<TreeNode*> q2;
	vector<int> line;
	q1.push(pRoot);
	while (q1.size() || q2.size()) {
		while (q1.size()) {
			TreeNode* top = q1.front();
			if (top->left) {
				q2.push(top->left);
			}
			if (top->right) {
				q2.push(top->right);
			}
			line.push_back(top->val);
			q1.pop();
		}
		if (line.size())
			res.push_back(line);
		line.clear();
		while (q2.size()) {
			TreeNode* top = q2.front();
			if (top->left) {
				q1.push(top->left);
			}
			if (top->right) {
				q1.push(top->right);
			}
			line.push_back(top->val);
			q2.pop();
		}
		if (line.size())
			res.push_back(line);
		line.clear();
	}
	return res;
}
```

## 32.3 之字形打印二叉树
```cpp
vector<vector<int> > Print(TreeNode* pRoot) {
	vector<vector<int>> res;
	if (pRoot == nullptr) {
		return res;
	}
	stack<TreeNode*> s1;
	stack<TreeNode*> s2;
	vector<int> line;
	s1.push(pRoot);
	while (s1.size() || s2.size()) {

		while (s1.size()) {
			TreeNode* p = s1.top();
			if (p->left) {
				s2.push(p->left);
			}
			if (p->right) {
				s2.push(p->right);
			}
			line.push_back(p->val);
			s1.pop();
		}
		if (line.size());
		res.push_back(line);
		line.clear();
		while (s2.size()) {
			TreeNode* p = s2.top();
			if (p->right) {
				s1.push(p->right);
			}
			if (p->left) {
				s1.push(p->left);
			}
			line.push_back(p->val);
			s2.pop();
		}
		if (line.size())
			res.push_back(line);
		line.clear();
	}
	return res;
}
```
### 33. 二叉搜索树的后序遍历序列
```cpp
bool VerifySquenceOfBST(vector<int> sequence) {
        if(sequence.size() == 0){
            return false;
        }
        if(sequence.size() == 1){
            return true;
        }
        return Verify(sequence,0,sequence.size()-1);
    }
    bool Verify(vector<int> sequence,int l,int r){
        if(l >= r){
            return true;
        }
        int i = l;
        while(sequence[i] < sequence[r]){
            i++;
        }
        for(int j=i;j<r;j++){
            if(sequence[j] < sequence[r]){
                return false;
            }
        }
        return Verify(sequence,l,i-1) && Verify(sequence,i,r-1);
    }
```

## 34. 二叉树中和为某一值的路径
```cpp
vector<vector<int>> ret;
vector<int> trace;
vector<vector<int> > FindPath(TreeNode* root,int expectNumber) {
    if(root){
        path(root,expectNumber);
    }
    return ret;
}
void path(TreeNode* root, int expectNumber) {
    trace.push_back(root->val);
    if (root->val == expectNumber&&root->left == nullptr&root->right == nullptr) {
        ret.push_back(trace);
    }
    else {
        if (root->left) {
            path(root->left, expectNumber - root->val);
        }
        if (root->right) {
            path(root->right, expectNumber - root->val);
        }
    }
    trace.pop_back();
}
```

## 35. 复杂链表的复制
```cpp
RandomListNode* Clone(RandomListNode* pHead)
    {
        if (pHead == nullptr)
            return nullptr;
        //插入新节点
        RandomListNode* cur = pHead;
        while (cur != nullptr) {
            RandomListNode* clone = new RandomListNode(cur->label);
            clone->next = cur->next;
            cur->next = clone;
            cur = clone->next;
        }
        //建立 random 链接
        cur = pHead;
        while (cur != nullptr) {
            RandomListNode* clone = cur->next;
            if (cur->random != nullptr)
                clone->random = cur->random->next;
            cur = clone->next;
        }
        //拆分
        cur = pHead;
        RandomListNode* pCloneHead = pHead->next;
        while (cur->next != nullptr) {
            RandomListNode* next = cur->next;
            cur->next = next->next;
            cur = next;
        }
        return pCloneHead;
    }
```

## 36. 二叉搜索树与双向链表
```cpp
 TreeNode* last = nullptr;
    TreeNode* Convert(TreeNode* pRootOfTree) {
        if (pRootOfTree == nullptr) {
            return nullptr;
        }
        convertHelper(pRootOfTree);
        while (last != nullptr&&last->left != nullptr) {
            last = last->left;
        }
        return last;
    }
    void convertHelper(TreeNode* pNode) {
        if (pNode == nullptr) {
            return;
        }
        if (pNode->left) {
            convertHelper(pNode->left);
        }
        pNode->left = last;
        if (last != nullptr) {
            last->right = pNode;
        }
        last = pNode;
        if (pNode->right) {
            convertHelper(pNode->right);
        }
    }
```

## 37. 序列化二叉树
```cpp

```

## 38. 字符串的排列

## 39. 数组中出现的次数超过一半的数字
```cpp
int MoreThanHalfNum_Solution(vector<int> numbers) {
	if (numbers.size() == 1) {
		return numbers[0];
	}
	int number = numbers[0];
	int times = 1;
	for (int i = 1; i < numbers.size(); i++) {
		if (numbers[i] == number) {
			times++;
		}
		else {
			times--;
		}
		if (times == 0) {
			number = numbers[i];
			times = 1;
		}
	}
	if (times < 2 && (number == numbers[numbers.size() - 1])) {
		number = 0;
	}
	return number;
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
## 40. 最小的k个数

### 基于堆的解法
```cpp
typedef multiset<int, greater<int>> intSet;
typedef multiset<int, greater<int>>::iterator setIterator;
vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        if(k<1||input.size()<k){
            return vector<int>();
        }
       //multiset
        vector<int>::const_iterator iter=input.begin();
        for(;iter!=input.end();iter++){
            if(intSet.size()<k){
                intSet.insert(*iter);
            }else{
                setIterator=intSet.begin();
                if(*iter<*(intSet.begin())){
                    intSet.erase(setIterator);
                    intSet.insert(*iter);
                }
            }
        }
        
        return vector<int>(intSet.begin(),intSet.end());
         
        //优先队列
        priority_queue<int> p;
        for(int i=0;i<input.size();i++){
            p.push(input[i]);
            if(p.size()>k){
                p.pop();
            }
        }
        vector<int> res;
        for(int i=0;i<k;i++){
            res.push_back(p.top());
            p.pop();
        }
        return res; 
```

### 基于 partition 的解法
```cpp
int partition(vector<int>& nums, int start, int end) {
	int pivot = a[start];
	int p = start + 1;
	int q = end;
	while (p <= q) {
		while (a[p] < pivot && p <= q) p++;
		while (a[q] >= pivot && p <= q) q--;
		if (p < q) {
			swap(a[p], a[q]);
		}
	}
	swap(a[start], a[q]);
	return q;
}
vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
	if (k < 1 || input.size() < k) {
		return vector<int>();
	}
	int start = 0;
	int end = input.size() - 1;
	int index = partition(input, start, end);
	while (index != k - 1) {
		if (index > k - 1) {
			end = index - 1;
			index = partition(input, start, end);
		}
		else {
			start = index + 1;
			index = partition(input, start, end);
		}
	}
	vector<int> res;
	for (int i = 0; i < k; i++) {
		res.push_back(input[i]);
	}
	return res;
}
```

## 41. 数据流中的中位数

## 42. 连续子数组的最大和
```cpp
int FindGreatestSumOfSubArray(vector<int> array) {
	if (array.size() == 0) {
		return 0;
	}
	int result = array[0];
	int max = result;
	for (int i = 1; i < array.size(); i++) {
		if (result < 0) {
			result = array[i];
		}
		else {
			result = array[i] + result;
		}
		if (result > max) {	
			max = result;
		}
	}
	return max;
}
```

## 43. 1 ~ n 整数中 1 出现的次数

## 44. 数字序列中某一位的数字

## 45. 把数组排成最小的数
```cpp
 string PrintMinNumber(vector<int> numbers) {
        sort(numbers.begin(),numbers.end(),[](const int& a,const int& b){
        return to_string(a)+to_string(b)<to_string(b)+to_string(a);});
        string res;
        for (auto c:numbers)   
            res+=to_string(c);
        return res;
    }
```

## 46. 把数字翻译成字符串

## 47. 礼物的最大值

## 48. 最长不含重复字符的子字符串

## 49. 丑数

## 50. 第一次只出现一个的字符

## 51. 数组中的逆序对

## 52. 两个链表的第一个公共节点
```cpp
ListNode* FindFirstCommonNode(ListNode* pHead1, ListNode* pHead2) {
       if (pHead1 == nullptr || pHead2 == nullptr) {
              return nullptr;
       }
       int l1 = getLength(pHead1);
       int l2 = getLength(pHead2);
       int diff;
       if (l1 > l2) {
              diff = l1 - l2;
              for (int i = 0; i < diff; i++) {
                     pHead1 = pHead1->next;
              }      
       }
       else {
              diff = l2 - l1;
              for (int i = 0; i < diff; i++) {
                     pHead2 = pHead2->next;
              }
       }
       while (pHead1 != nullptr&&pHead2 != nullptr&&pHead1->val != pHead2->val) {
              pHead1 = pHead1->next;
              pHead2 = pHead2->next;
       }
       return pHead1;
}
int getLength(ListNode* pHead) {
       int count = 0;
       while (pHead != nullptr) {
              count++;
              pHead = pHead->next;
       }
       return count;
}
```

## 53.1 数字在排序数组中出现的次数
```cpp
int GetNumberOfK(vector<int> data, int k) {
	if (data.size() == 0 || k <= 0) {
		return 0;
	}
	int number = 0;
	int l = 0;
	int r = data.size() - 1;
	int first = getFirstK(data, k, l, r);
	int last = getLastK(data, k, l, r);
	if (first > -1 && last > -1) {
		number = last - first + 1;
	}
	return number;
}
int getFirstK(vector<int> data, int k, int l, int r) {
	if (l > r) {
		return -1;
	}
	int m = (l + r) / 2;
	if (data[m] == k && data[m - 1] != k) {
		return m;
	}
	else if (data[m] >= k) {
		r = m - 1;
	}
	else {
		l = m + 1;
	}
	return getFirstK(data, k, l, r);
}
int getLastK(vector<int> data, int k, int l, int r) {
	if (l > r) {
		return -1;
	}
	int m = (l + r) / 2;
	if (data[m] == k && data[m + 1] != k) {
		return m;
	}
	else if (data[m] <= k) {
		l = m + 1;
	}
	else {
		r = m - 1;
	}
	return getLastK(data, k, l, r);
}
```

## 53.2 0~n-1 中缺失的数字
```cpp
int GetMissingNumber(vector<int> numbers) {
	if (numbers.size() <= 0)
		return -1;
	int left = 0;
	int right = numbers.size() - 1;
	while (left <= right) {
		int middle = (right + left) / 2;
		if (numbers[middle] != middle) {
			if (middle == 0 || numbers[middle - 1] == middle - 1)
				return middle;
			right = middle - 1;
		}
		else 
			left = middle + 1;
	}
	if (left == numbers.size()) {
		return left;
	}
	return -1;
}
```

## 54. 二叉搜索树的第K大节点

### 递归
```cpp
    TreeNode* ret = nullptr;
    int cnt = 0;
    TreeNode* KthNode(TreeNode* pRoot, int k)
    {
        inOrder(pRoot, k);
        return ret;
    }
    void inOrder(TreeNode* pRoot, int k) {
        if (pRoot == nullptr || k <= 0) {
            return;
        }
        inOrder(pRoot->left, k);
        cnt++;
        if (cnt == k)
            ret = pRoot;
        inOrder(pRoot->right, k);
    }
```

## 55.1 二叉树的深度
```cpp
int TreeDepth(TreeNode* pRoot) {
        if (pRoot == nullptr) {
            return 0;
        }
        int left = TreeDepth(pRoot->left);
        int right = TreeDepth(pRoot->right);
        return (left > right) ? (left + 1) : (right + 1);
    }
```

## 55.2 平衡二叉树
````cpp

```

## 56.1 数组中只出现一次的两个数字
```cpp
void FindNumsAppearOnce(vector<int> data,int* num1,int *num2) {
       if(data.size()<2){
            return;
        }     
       unordered_map<int, int> map;
        for (int i = 0; i < data.size(); i++)
            map[data[i]]++;
        vector<int>v;
        for (int i = 0; i < data.size(); i++)
            if (map[data[i]]== 1)
                v.push_back(data[i]);
        *num1 = v[0]; *num2 = v[1];
    }
```

## 57.1 和为 s 的数字
```cpp
vector<int> FindNumbersWithSum(vector<int> array, int sum) {
	vector<int> result;
	int length = array.size();
	int left = 0;
	int right = length - 1;
	while (left < right) {
		if (array[left] + array[right] == sum) {
			result.push_back(array[left]);
			result.push_back(array[right]);
			return result;
		}
		else if (array[left] + array[right] < sum) {
			left++;
		}
		else {
			right--;
		}
	}
	return result;
}
```

### 57.2 和为 s 的连续正数序列
```cpp
vector<vector<int> > FindContinuousSequence(int sum) {
	vector<vector<int>> res;
	if (sum < 3) {
		return res;
	}
	int start = 1;
	int end = 2;
	int middle = (1 + sum) / 2;
	int count = start + end;
	while (start < middle) {
		while (count < sum) {
			end++;
			count = count + end;
		}
		if (count == sum) {
			vector<int> list;
			for (int i = start; i <= end; i++) {
				list.push_back(i);
			}
			res.push_back(list);
		}
		count = count - start;
		start++;
	}
	return res;
}
```

## 58. 翻转字符串

## 59. 队列的最大值

## 60. n 个骰子的点数

## 61. 扑克牌中的顺子

## 62. 圆圈中最后剩下的数字

### 数学公式法
```cpp
int LastRemaining_Solution(int n, int m)
{
	if (n < 1 || m < 1) {
		return -1;
	}
	int last = 0;
	for (int i = 2; i <= n; i++) {
		last = (last + m) % i;
	}
	return last;
}
```

### 环形链表模拟圆圈
```cpp

```


## 63. 股票的最大利润
```cpp
int maxProfit(vector<int> &prices)
{
    if (prices.size() == 0)
    {
        return 0;
    }
    int min = prices[0];
    int diff = 0;
    int max = 0;
    for (int price : prices)
    {
        if (price < min)
        {
            min = price;
            diff = price - min;
            if (diff > max)
            {
                max = diff;
            }
        }
        return max;
    }
}
```


## 64. 求 1+2+...+n

## 65. 不用加减乘除做加法

## 66. 构建乘积数组

## 67. 把字符串转换成整数

## 69. 树中两个节点的最低公共祖先