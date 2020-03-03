[TOC]

## 100. Same Tree

#### 1. Recursion (my solution)

**Remember to consider NULL**

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if(p == NULL && q == NULL) return true;
        if(p == NULL || q == NULL) return false;
        if(p->val != q->val) return false;
        else return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    }
};
```

Time consuming : O(n) (every node access one time)



#### 2. Iteration

Using queue to implement.

```c++
bool isSameTree(TreeNode* p, TreeNode* q) {
        queue<TreeNode*> p1;
        queue<TreeNode*> p2;
        p1.push(p);
        p2.push(q);
        while(!p1.empty() && !p2.empty()) {
            TreeNode *cur1 = p1.front();
            TreeNode *cur2 = p2.front();
            p1.pop(); p2.pop();
            if(cur1 == NULL && cur2 == NULL) continue;
            if(cur1 == NULL || cur2 == NULL) return false;
            if(cur1->val != cur2->val) return false;
            p1.push(cur1->left);
            p1.push(cur1->right);
            p2.push(cur2->left);
            p2.push(cur2->right);
        }
        return true;
    }
```

## 101. Symmetric Tree

#### 1.  Iteration

Using queue to implement.

```c++
bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        queue<TreeNode*> qLeft;
        queue<TreeNode*> qRight;
        qLeft.push(root->left);
        qRight.push(root->right);
        while(!qLeft.empty() && !qRight.empty()){
            TreeNode *left = qLeft.front();
            TreeNode *right = qRight.front();
            qLeft.pop(); qRight.pop();
            if(left == NULL && right == NULL) continue;
            if(left == NULL || right == NULL) return false;
            if(left->val != right->val) return false;
            qLeft.push(left->left);
            qLeft.push(left->right);
            qRight.push(right->right);
            qRight.push(right->left);
        }
        return true;
    }
```

Time consuming O(n)

#### 2. Recursion

```c++
bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        return IsSymmetricLeftRight(root->left, root->right);
    }
    bool IsSymmetricLeftRight(TreeNode* left, TreeNode* right){
        if(left == NULL && right == NULL) return true;
        if(left == NULL || right == NULL) return false;
        if(left->val != right->val) return false;
        return IsSymmetricLeftRight(left->left, right->right) && IsSymmetricLeftRight(left->right, right->left);
    }
```

## 104. Max Depth

#### 1. Recursion

```c++
int maxDepth(TreeNode* root) {
        if(root == NULL) return 0;
        else return 1 + max(maxDepth(root->left), maxDepth(root->right));
    }
```

Time consuming O(n).

## 107. 层次遍历Hierarchical traversal

#### 1. Add separator(my solution)

```c++
vector<vector<int>> levelOrderBottom(TreeNode* root) {
        queue<TreeNode*> q;
    	// better judge if root is NULL
        q.push(root);
        q.push(NULL);
        vector<vector<int>> result;
        vector<int> layer;
        while(!q.empty()){
            TreeNode *cur = q.front();
            q.pop();
            if(cur == NULL){
                if(!layer.empty())
                    result.insert(result.begin(), layer);
                layer.clear();
                continue;
            }
            layer.push_back(cur->val);
            if(cur->left)
                q.push(cur->left);
            if(cur->right)
                q.push(cur->right);
            if(q.front() == NULL) q.push(NULL);
        }
        return result;
    }
```

Time consuming is O(n).



**Attention Please:**

1. If want to empty a vector, the function is clear(), not empty().
2. For a tree, its child can be NULL, so if you want to using NULL as a separator, you need to consider if the node is NULL.
3. For hierarchical traversal, need a separator to tell the layer.



#### 2. record the queue length before push new node into it

```c++
vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> result;
        vector<int> layer;
        if(!root) return result;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            int length = q.size();
            for(int i = 0;i < length;i++){
                TreeNode *cur = q.front();
                q.pop();
                layer.push_back(cur->val);
                if(cur->left)
                    q.push(cur->left);
                if(cur->right)
                    q.push(cur->right);
            }
            result.insert(result.begin(), layer);
            layer.clear();
        }
        return result;
    }
```

## 108. Sorted array to AVL tree

```c++
TreeNode* sortedArrayToBST(vector<int>& nums) {
    return SortedArrayToBST(nums, 0, nums.size());
}
TreeNode* SortedArrayToBST(vector<int>& nums, int begin, int length){
    if(length - begin <= 0) return NULL;
    int mid = begin + ((length - begin) / 2);
    TreeNode *root = new TreeNode(nums[mid]);
    // Attention: here should be mid, rather than mid - 1,
    // Because the function defination, is not end, should be length
    root->left = SortedArrayToBST(nums, begin, mid);
    root->right = SortedArrayToBST(nums, mid + 1, length);
    return root;
}
```

## 109. Sorted List convert to height balanced BST

#### 1. Simple idea, convert list to array (change to 108)

#### 2. [ATTENTION] using slow&fast pointer to do

```c++
TreeNode* sortedListToBST(ListNode* head) {
        if(head == NULL) return NULL;
    	// ATTENTION: this judge can't be omitted,
    	// Because if there only one element,
    	// using recursion will unterminal
        if(head->next == NULL) {
            TreeNode *root = new TreeNode(head->val);
            return root;
        }
        ListNode *prev, *slow, *fast;
        prev = slow = fast = head;
        while(fast != NULL && fast->next == NULL){
            prev = slow;
            slow = slow->next;
            fast = fast->next->next;
        }
        TreeNode *root = new TreeNode(slow->val);
        prev->next = NULL;
        root->left = sortedListToBST(head);
        root->right = sortedListToBST(slow->next);
        return root;
    }
```

## 110. Balanced Binary Tree

#### 1. Recursion using height(Top to Down)

```c++
bool isBalanced(TreeNode* root) {
        if(root == NULL) return true;
        return isBalanced(root->left) && isBalanced(root->right) 
                && (abs(Height(root->left) - Height(root->right)) <= 1);
    }
    int Height(TreeNode* root){
        if(root == NULL) return 0;
        return 1 + max(Height(root->left), Height(root->right));
    }
```

But I think this solution isn't effective, because obviously, there are repeated operation in the Height calculation.

And the time consuming is $O(n^2)$

#### 2. Modify 1 (Down to Top)

```c++
bool isBalanced(TreeNode* root) {
        if(root == NULL) return true;
        return Height(root) != -1;
    }
    int Height(TreeNode* root){
        // From down to top
        if(root == NULL) return 0;
        // leaf nodes
        if(root->left == NULL && root->right == NULL) return 1;

        int leftHeight = Height(root->left);
        if(leftHeight <= -1) return -1;

        int rightHeight = Height(root->right);
        if(rightHeight <= -1) return -1;

        if(abs(leftHeight - rightHeight) <= 1) return 1 + max(leftHeight, rightHeight);
        else return -1;
    }
```

If the subtree isn't balanced, return -1 and no need for extra calculation.

Every node will be traversed one time, so the time consuming is O(n).

## 111. Minimum Depth of Binary Tree

#### 1. Recursion

Because the minimum depth need the length of the path ended with leaf, so if the child is NULL, and the other isn't, then must choose the other path.

```c++
int minDepth(TreeNode* root) {
        if(root == NULL) return 0;
        if(root->left == NULL && root->right == NULL) return 1;
        if(root->left == NULL) return 1 + minDepth(root->right);
        if(root->right == NULL) return 1 + minDepth(root->left);
        return 1 + min(minDepth(root->left), minDepth(root->right));
    }
```

The time consuming is O(n).

#### 2. Hierarchical traverse

```c++
int minDepth(TreeNode* root) {
        if(!root) return 0;
        queue<TreeNode*> q;
        q.push(root);
        int height = 0;
        bool isTerminal = false;
        while(!q.empty()){
            int length = q.size();
            height++;
            for(int i = 0;i < length;i++){
                TreeNode* cur = q.front();
                q.pop();
                if(cur->left == NULL && cur->right == NULL){
                    isTerminal = true;
                    break;
                }
                if(cur->left != NULL) q.push(cur->left);
                if(cur->right != NULL) q.push(cur->right);
            }
            if(isTerminal) break;
        }
        return height;
    }
```

The first leaf node in Hierarchical traverse is the shortest path in binary tree.

## 112. Path Sum

#### 1. Recursion

```c++
bool hasPathSum(TreeNode* root, int sum) {
        if(root == NULL) return false;
        if(root->left == NULL && root->right == NULL) return root->val == sum;
        return hasPathSum(root->left, sum - root->val) || hasPathSum(root->right, sum - root->val);
    }
```

Firstly, I don't want to judge if the node is leaf, because I think if the tree is empty and the sum is 0, it should return true.

But according to the requirement, I compromise....

## 226. Invert Binary Tree

#### 1. Recursion

```c++
TreeNode* invertTree(TreeNode* root) {
        if(root == NULL ||
            (root->left == NULL && root->right == NULL)) return root;
        else {
            TreeNode *tmp = root->left;
            root->left = invertTree(root->right);
            root->right = invertTree(tmp);
            return root;
        }
    }
```

The thing needs to be noticed is **tmp** , need to store it firstly.

The time consuming is O(n).

#### 2. Iteration

The core is to access every node. If access every node, we can change its child. So if using iteration methods, only to do is to push every node into queue.

```c++
TreeNode* invertTree(TreeNode* root) {
        if(root == NULL) return root;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            TreeNode* head = q.front();
            q.pop();
            if(head->left == NULL && head->right == NULL) continue;
            TreeNode* tmp = head->left;
            head->left = head->right;
            head->right = tmp;
            if(head->left != NULL)
                q.push(head->left);
            if(head->right != NULL)
                q.push(head->right);
        }
        return root;
    }
```

**Every time using Iteration, needs to consider that if the child is NULL, it can't be push into queue!!!!!!!!**

## 235. Lowest Common Ancestor of BST

#### 1. Recursion

```c++
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if((p->val <= root->val && q->val >= root->val)
            || (p->val >= root->val && q->val <= root->val)) return root;
        else if(p->val < root->val){
            return lowestCommonAncestor(root->left, p, q);
        }
        else return lowestCommonAncestor(root->right, p, q);
    }
```

The time consuming is **O(n)**. (Worst situation, a linear BST)

Iteration don't have much to do, because the recursion itself is iteration. only to do is to change the root pointer.

## 257. Binary Tree Paths

#### 1. Recursion (my solution)

```c++
	vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> result;
        vector<TreeNode*> path;
        OutputPath(root, path, result);
        return result;
    }
    void OutputPath(TreeNode* root, vector<TreeNode*> &path, vector<string> &result){
        if(root == NULL) return;
        if(root->left == NULL && root->right == NULL){
            string s = "";
            for(int i = 0;i < path.size();i++){
                s += to_string(path[i]->val);
                s += "->";
            }
            s += to_string(root->val);
            result.push_back(s);
            return;
        }
        else{
            path.push_back(root);
            if(root->left) OutputPath(root->left, path, result);
            if(root->right) OutputPath(root->right, path, result);
            path.erase(path.begin() + path.size() - 1);
        }
    }
```

**1. The thing need to notice is : in else, after push and go through the left and right children, we need to pop **

<font color='red'>If we  don't use &path, we can omit the pop methods, but will sacrifice the space</font> 

**2. queue : push(), pop(), front()**

​    **vector : push_back(), erase()**

**3. to_string : int to string**

<font color="red">Remember to analyse time and space complexity</font> : Time complexity: O(n), Space complexity : O(n). 

#### 2. Recursion

The first solution is the usual idea, but we could simply pass string to record the path, rather than vector<TreeNode*>.

```c++
	vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> result;
        string path;
        OutputPath(root, path, result);
        return result;
    }
    void OutputPath(TreeNode* root, string path, vector<string> &result){
        if(root == NULL) return;
        if(root->left == NULL && root->right == NULL){
            path += to_string(root->val);
            result.push_back(path);
            return;
        }
        else{
            path += to_string(root->val);
            path += "->";
            if(root->left) OutputPath(root->left, path, result);
            if(root->right) OutputPath(root->right, path, result);
        }
    }
```

## 404. Sum of Left Leaves

#### 1. Recursion

这道题一开始我没想出来，但是题解给了一个很好的分析题目的思路：

首先求所有节点的和，我们可以简单递归；

如果求所有叶子节点的和，则多加一个判断递归；

如果是左叶子的话，需要加一个flag（我是因为死活不想用flag所以写不出来）

```c++
int sumOfLeftLeaves(TreeNode* root) {
        if(root == NULL) return 0;
        return Sum(root->left, true) + Sum(root->right, false);
    }
    int Sum(TreeNode* root, bool left){
        if(root == NULL) return 0;
        if(root->left == NULL && root->right == NULL && left) return root->val;
        return Sum(root->left, true) + Sum(root->right, false);
    }
```



另外需要注意初始函数需要判断是否为NULL，因为空树应该返回0。（这里需要注意不能直接调用Sum(root, true)，因为如果这棵树只有一个节点，则不应该是左叶子，不能默认初始为true。

时间复杂度为O(n)，因为每个节点访问一遍。



