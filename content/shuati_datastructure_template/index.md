+++
title = "Data structure template"
date = 2021-04-10 17:21:06
slug = "202104101721"

[taxonomies]
tags = ["Cram" ]
categories = ["Cram"]

+++

<!-- more -->

# 并查集

## 路径压缩（常用）

```c++
class UnionFind{
public:
    //查找根节点，如果节点的父节点不为空，那就不断迭代。
    int find(int x){
        int root = x;
        
        while(father[root] != -1){
            root = father[root];
        }
        //路径压缩
        while(x != root){
            int original_father = father[x];
            father[x] = root;
            x = original_father;
        }
        
        return root;
    }
    
    //判断两节点是否相连，判断它们的祖先是否相同
    bool is_connected(int x,int y){
        return find(x) == find(y);
    }
    
    //合并两个节点，y的根结点作为x根节点的父亲
    void merge(int x,int y){
        int root_x = find(x);
        int root_y = find(y);
        
        if(root_x != root_y){
            //root_x 接在 root_y 后面
            father[root_x] = root_y;
            num_of_sets--;
        }
    }
    
    //添加新节点，它的父节点应该为空
    void add(int x){
        if(!father.count(x)){
            father[x] = -1;
            num_of_sets++;
        }
    }
    //  返回集合数
    int get_num_of_sets(){
        return num_of_sets;
    }
    
private:
    // 记录父节点
    unordered_map<int,int> father;
    // 记录集合数量
    int num_of_sets = 0;
};

```

## 启发式合并（按秩排序）

在平均情况下

- 不使用启发式合并、只使用路径压缩，时间复杂度依然是 O(ma(m, n))
- 如果只使用启发式合并，而不使用路径压缩，时间复杂度为 O(mlogn)

```c++
class UnionFind {
public:
    vector<int> parent;  // 下标idx为节点，parent[idx]为该节点的父亲
    vector<int> size;    // 若idx为父亲根节点，size[idx]为该连通分量的大小
    int n;               // 节点数量
    int setCount;        // 连通分量的数量

public:
    UnionFind(int _n) : n(_n), setCount(_n), parent(_n), size(_n, 1) {
        iota(parent.begin(), parent.end(), 0);
    }

    //递归查找根节点，如果节点i的父节点为本身就找到了根，结束递归
    int find(int x) {
        return parent[x] == x ? x : parent[x] = find(parent[x]);
    }

    //合并两个节点
    bool unite(int x, int y) {
        x = find(x);
        y = find(y);
        if (x == y) return false;

        if (size[x] < size[y]) {
            swap(x, y);
        }
        parent[y] = x;       // x 作为 y 的父亲
        size[x] += size[y];  // 父亲节点x的联通分量大小加上y节点的
        --setCount;
        return true;
    }

    //判断两个节点是否连通
    bool is_connected(int x, int y) {
        x = find(x);
        y = find(y);
        return x == y;
    }

    //断开节点与他父节点的连接
    void disconnected(int x) {
        if (x != parent[x]) {
            parent[x] = x;
            size[x] = 1;
            ++setCount;
        }
    }
};
```



# 二叉树

```c++
// Definition for a binary tree node.
struct TreeNode {
      int val;
      TreeNode *left;
      TreeNode *right;
      TreeNode() : val(0), left(nullptr), right(nullptr) {}
      TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
      TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
};
```

## 前中后序遍历

```c++
class Solution {
public:
    //前序
    void dfs(TreeNode *cur, vector<int> &vec) {
        if (cur == NULL) return;
        vec.push_back(cur->val);  // 中
        dfs(cur->left, vec);      // 左
        dfs(cur->right, vec);     // 右
    }
    //中序
    void dfs(TreeNode *cur, vector<int> &vec) {
        if (cur == NULL) return;
        dfs(cur->left, vec);
        vec.push_back(cur->val);
        dfs(cur->right, vec);
    }
    //后序
     void dfs(TreeNode *cur, vector<int> &vec) {
        if (cur == NULL) return;
        dfs(cur->left, vec);
        dfs(cur->right, vec);
        vec.push_back(cur->val);
    }
    vector<int> Traversal(TreeNode *root) {
        vector<int> res;
        dfs(root, res);
        return res;
    }
};
```

## 层序遍历

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode *root) {
        vector<vector<int>> res;
        if (!root) return res;

        queue<TreeNode *> q;
        q.push(root);
        while (!q.empty()) {
            int layerSz = q.size();
            vector<int> layer;
            for (int i = 0; i < layerSz; i++) {
                auto node = q.front();
                q.pop();
                layer.push_back(node->val);
                if (node->left) q.push(node->left);
                if (node->right) q.push(node->right);
            }
            res.push_back(layer);
        }
        return res;
    }
};
```

## 前序中序序列构造二叉树

```c++
class Solution {
private:
    unordered_map<int, int> index;

public:
    TreeNode* myBuildTree(const vector<int>& preorder, const vector<int>& inorder, int preorder_left, int preorder_right, int inorder_left, int inorder_right) {
        if (preorder_left > preorder_right) {
            return nullptr;
        }
        
        // 前序遍历中的第一个节点就是根节点
        int preorder_root = preorder_left;
        // 在中序遍历中定位根节点
        int inorder_root = index[preorder[preorder_root]];
        
        // 先把根节点建立出来
        TreeNode* root = new TreeNode(preorder[preorder_root]);
        // 得到左子树中的节点数目
        int size_left_subtree = inorder_root - inorder_left;
        // 递归地构造左子树，并连接到根节点
        // 先序遍历中「从 左边界+1 开始的 size_left_subtree」个元素就对应了中序遍历中「从 左边界 开始到 根节点定位-1」的元素
        root->left = myBuildTree(preorder, inorder, preorder_left + 1, preorder_left + size_left_subtree, inorder_left, inorder_root - 1);
        // 递归地构造右子树，并连接到根节点
        // 先序遍历中「从 左边界+1+左子树节点数目 开始到 右边界」的元素就对应了中序遍历中「从 根节点定位+1 到 右边界」的元素
        root->right = myBuildTree(preorder, inorder, preorder_left + size_left_subtree + 1, preorder_right, inorder_root + 1, inorder_right);
        return root;
    }

    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = preorder.size();
        // 构造哈希映射，帮助我们快速定位根节点
        for (int i = 0; i < n; ++i) {
            index[inorder[i]] = i;
        }
        return myBuildTree(preorder, inorder, 0, n - 1, 0, n - 1);
    }
};
```

**复杂度分析**

时间复杂度：O(n)，其中 n 是树中的节点个数。

空间复杂度：O(n))，除去返回的答案需要的 O(n)空间之外，我们还需要使用 O(n) 的空间存储哈希映射，以及 O(h)其中 h 是树的高度）的空间表示递归时栈空间。这里 h < n，所以总空间复杂度为 O(n)。



## 中序后序序列构造二叉树

```c++
class Solution {
    int post_idx;
    unordered_map<int, int> idx_map;
public:
    TreeNode* helper(int in_left, int in_right, vector<int>& inorder, vector<int>& postorder){
        // 如果这里没有节点构造二叉树了，就结束
        if (in_left > in_right) {
            return nullptr;
        }

        // 选择 post_idx 位置的元素作为当前子树根节点
        int root_val = postorder[post_idx];
        TreeNode* root = new TreeNode(root_val);

        // 根据 root 所在位置分成左右两棵子树
        int index = idx_map[root_val];

        // 下标减一
        post_idx--;
        // 构造右子树
        root->right = helper(index + 1, in_right, inorder, postorder);
        // 构造左子树
        root->left = helper(in_left, index - 1, inorder, postorder);
        return root;
    }
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        // 从后序遍历的最后一个元素开始
        post_idx = (int)postorder.size() - 1;

        // 建立（元素，下标）键值对的哈希表
        int idx = 0;
        for (auto& val : inorder) {
            idx_map[val] = idx++;
        }
        return helper(0, (int)inorder.size() - 1, inorder, postorder);
    }
};
```

**复杂度分析**

时间复杂度：O(n)，其中 n 是树中的节点个数。

空间复杂度：O(n)。我们需要使用 O(n)的空间存储哈希表，以及 O(h)（其中 h 是树的高度）的空间表示递归时栈空间。这里 h < n，所以总空间复杂度为 O(n)。

## 二叉搜索树与序列化和反序列化

二叉搜索树能只够通过前序序列或后序序列构造

### 序列化和反序列化二叉搜索树

普通二叉树同样适用

#### BFS

```c++
public class Codec {

    //把树转化为字符串（使用BFS遍历）
    public String serialize(TreeNode root) {
        //边界判断，如果为空就返回一个字符串"#"
        if (root == null)
            return "#";
        //创建一个队列
        Queue<TreeNode> queue = new LinkedList<>();
        StringBuilder res = new StringBuilder();
        //把根节点加入到队列中
        queue.add(root);
        while (!queue.isEmpty()) {
            //节点出队
            TreeNode node = queue.poll();
            //如果节点为空，添加一个字符"#"作为空的节点
            if (node == null) {
                res.append("#,");
                continue;
            }
            //如果节点不为空，把当前节点的值加入到字符串中，
            //注意节点之间都是以逗号","分隔的，在下面把字符
            //串还原二叉树的时候也是以逗号","把字符串进行拆分
            res.append(node.val + ",");
            //左子节点加入到队列中（左子节点有可能为空）
            queue.add(node.left);
            //右子节点加入到队列中（右子节点有可能为空）
            queue.add(node.right);
        }
        return res.toString();
    }

    //把字符串还原为二叉树
    public TreeNode deserialize(String data) {
        //如果是"#"，就表示一个空的节点
        if (data == "#")
            return null;
        Queue<TreeNode> queue = new LinkedList<>();
        //因为上面每个节点之间是以逗号","分隔的，所以这里
        //也要以逗号","来进行拆分
        String[] values = data.split(",");
        //上面使用的是BFS，所以第一个值就是根节点的值，这里创建根节点
        TreeNode root = new TreeNode(Integer.parseInt(values[0]));
        queue.add(root);
        for (int i = 1; i < values.length; i++) {
            //队列中节点出栈
            TreeNode parent = queue.poll();
            //因为在BFS中左右子节点是成对出现的，所以这里挨着的两个值一个是
            //左子节点的值一个是右子节点的值，当前值如果是"#"就表示这个子节点
            //是空的，如果不是"#"就表示不是空的
            if (!"#".equals(values[i])) {
                TreeNode left = new TreeNode(Integer.parseInt(values[i]));
                parent.left = left;
                queue.add(left);
            }
            //上面如果不为空就是左子节点的值，这里是右子节点的值，注意这里有个i++，
            if (!"#".equals(values[++i])) {
                TreeNode right = new TreeNode(Integer.parseInt(values[i]));
                parent.right = right;
                queue.add(right);
            }
        }
        return root;
    }
}
```



#### DFS

```c++
class Codec {

    //把树转化为字符串（使用DFS遍历，也是前序遍历，顺序是：根节点→左子树→右子树）
    public String serialize(TreeNode root) {
        //边界判断，如果为空就返回一个字符串"#"
        if (root == null)
            return "#";
        return root.val + "," + serialize(root.left) + "," + serialize(root.right);
    }

    //把字符串还原为二叉树
    public TreeNode deserialize(String data) {
        //把字符串data以逗号","拆分，拆分之后存储到队列中
        Queue<String> queue = new LinkedList<>(Arrays.asList(data.split(",")));
        return helper(queue);
    }

    private TreeNode helper(Queue<String> queue) {
        //出队
        String sVal = queue.poll();
        //如果是"#"表示空节点
        if ("#".equals(sVal))
            return null;
        //否则创建当前节点
        TreeNode root = new TreeNode(Integer.valueOf(sVal));
        //分别创建左子树和右子树
        root.left = helper(queue);
        root.right = helper(queue);
        return root;
    }
}

```



#### stringstream

```c++
class Codec {
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        if(root == nullptr)
            return "#";

        return to_string(root->val) + ' ' + serialize(root->left) + ' ' + serialize(root->right);
    }

    // Decodes encoded data to tree.
    TreeNode* rebuildTree(stringstream &ss){
        string tmp;
        ss >> tmp;
        
        if(tmp == "#")
            return nullptr;

        TreeNode* node = new TreeNode(stoi(tmp));
        node->left = rebuildTree(ss);
        node->right = rebuildTree(ss);

        return node;
    }
    
    TreeNode* deserialize(string data) {
        stringstream ss(data);
        return rebuildTree(ss);
    }
};

```



# **树状数组**和线段树

维护区间信息 的数据结构有：前缀和、差分数组、树状数组、线段数

| 数据结构 \ 操作 | 单点修改 | 区间查询 |
| --------------- | -------- | -------- |
| 前缀和          | O(n)     | O(1)     |
| 树状数组        | O(logn)  | O(logn)  |
| 线段数          | O(logn)  | O(logn)  |

注意：「线段树」和「树状数组」不能处理输入数组的长度有增加或者减少的情况。

## 应用场景

## ![](.\img\choice.png)

## 结构

![](.\img\structure.PNG)

[树状数组&线段树](https://dowalle.gitbook.io/algo/algorithm/1-shu-ju-jie-gou/5-shu-zhuang-shu-zu-xian-duan-shu#er-shu-zhuang-shu-zu)

## 树状数组模板

n 为原始数组的元素个数

idx 为从 1 开始计，0 号下标不用

```c++
class FenwickTree {
public:
    int size;
    vector<int> tree;

    FenwickTree(int n) {
        size = n;
        tree.resize(size + 1, 0);
    }
	// C[i] 的父亲为 C[i+lowbit(i)]
    // C[i] 由 lowbit(i) 个 A 中元素组成
    int lowbit(int idx) {
        // idx 为树状数组下标
        return idx & (-idx);
    }
    void add(int idx, int delta) {
        // 单点更新，从前往后，idx 为树状数组下标
        // delta 为变化量，如果已知需要变化的值val，传入 val - 原始数组[idx-1]
        while (idx < size + 1) {
            tree[idx] += delta;
            idx += lowbit(idx);
        }
    }
    int query(int idx) {
        // 查询前缀和，从后往前，idx 为树状数组下标
        int sum = 0;
        while (idx > 0) {
            sum += tree[idx];
            idx -= lowbit(idx);
        }
        return sum;
    }
    int sumRange(int left, int right) {
        // left, right 为树状数组下标
        int preLeft = query(left - 1);
        int preRight = query(right);
        return preRight - preLeft;
    }
};
```

### 



## 线段树模板

```c++
struct Node{
    int l, r;
    int sum;
};

class NumArray {
private:
    vector<Node> tr;
    vector<int> nums;
    int n;
public:
    NumArray(vector<int>& nums) 
    {
        n = nums.size();
        tr = vector<Node>(n * 4);
        this -> nums = nums;
        build(1, 1, n);//建立线段树
    }
    
    void update(int index, int val) 
    {
        modify(1, index + 1, val);
    }
    
    int sumRange(int left, int right) 
    {
        return query(1, left + 1, right + 1).sum;
    }

    void build(int u, int l, int r)
    {
        if (l == r) tr[u] = {l, r, nums[l - 1]};
        else
        {
            tr[u] = {l, r};
            int mid = l + r >> 1;
            build(u << 1, l, mid), build(u << 1 | 1, mid + 1, r);
            pushup(u);
        }
    }//线段树初始化

    void pushup(Node &u, Node &l, Node &r)
    {
        u.sum = l.sum + r.sum;
    }//向上调整

    void pushup(int u)
    {
        pushup(tr[u], tr[u << 1], tr[u << 1 | 1]);
    }//向上调整

    void modify(int u, int x, int val)
    {
        if (tr[u].l == x && tr[u].r == x) tr[u] = {x, x, val};
        else
        {
            int mid = tr[u].l + tr[u].r >> 1;
            if (x <= mid) modify(u << 1, x, val);//左边更新
            else modify(u << 1 | 1, x, val);//右边更新
            pushup(u);//向上更新父节点
        }
    }

    Node query(int u, int l, int r)
    {
        if (tr[u].l >= l && tr[u].r <= r) return tr[u];//返回结点
        else
        {
            int mid = tr[u].l + tr[u].r >> 1;
            if (r <= mid) return query(u << 1, l, r);//左边查询
            else if (l > mid) return query(u << 1 | 1, l, r);//右边查询
            else
            {
                auto left = query(u << 1, l, r);
                auto right = query(u << 1 | 1, l, r);
                Node res;
                pushup(res, left, right);//两边查询后返回结点和
                return res;
            }
        }
    }

};
```

# 字典树

Trie 树是一种用于**快速查询「某个字符串/字符前缀」**是否存在的数据结构。

其核心是使用「边」来代表有无字符，使用「点」来记录是否为「单词结尾」以及「其后续字符串的字符有哪些」。

![](C:\Users\prts\Desktop\folder\my\blog\ArCeusJ2000.github.io\content\shuati_datastructure_template\img\trie.png)

## Trie树优化DFS

`if (node->son[nidx] != nullptr)` 可做到提前剪枝，大幅度优化

```c++
void dfs(int row, int col, Trie* node) {
    if (node->str != "") {
        ans_set.insert(node->str);
    }
    for (pair<int, int> dir : direction) {
        int nr = row + dir.first;
        int nc = col + dir.second;
        if (0 <= nr && nr < rows && 0 <= nc && nc < cols) {
            if (!visited[nr][nc]) {
                int nidx = board[nr][nc] - 'a';
                if (node->son[nidx] != nullptr) {
                    visited[nr][nc] = true;
                    dfs(nr, nc, node->son[nidx]);
                    visited[nr][nc] = false;
                }
            }
        }
    }
}
```

## 模板

### TrieNode 实现

随着数据的不断插入，根据需要不断创建 TrieNode 节点。

时间复杂度：Trie 树的每次调用时间复杂度取决于入参字符串的长度。复杂度为 O(Len)。

空间复杂度：结点数量为 n，字符集大小为 k。复杂度为 O(nk)。

```c++
class Trie {
public:
    Trie* son[26];  // 存放当前字符之后的字符
    bool isWord;
    string str;
  
    Trie() {
        for (int i = 0; i < 26; i++) son[i] = nullptr;
        isWord = false;
        str = "";
    }
    ~Trie() {
        for (int i = 0; i < 26; i++) {
            if (son[i] != nullptr) delete son[i];
        }
    }

    void insert(string word) {
        Trie* root = this;     // 从头节点开始查
        for (char c : word) {  // 类似链表的遍历
            int cur = c - 'a';
            if (root->son[cur] == nullptr) root->son[cur] = new Trie();
            root = root->son[cur];
        }
        root->isWord = true;  // 在单词的结尾节点标记一下 是单词
        root->str = word;     // 结尾直接记录单词
    }

    bool search(string word) {
        Trie* root = this;  // 从头节点开始查
        for (char c : word) {
            int cur = c - 'a';
            if (root->son[cur] == nullptr) return false;  // word还没遍历完，就找不到了
            root = root->son[cur];
        }
        return root->isWord;  // 遍历到word结尾的节点，是否是之前存在的单词
    }

    bool startsWith(string prefix) {
        Trie* root = this;
        for (char c : prefix) {
            int cur = c - 'a';
            if (root->son[cur] == nullptr) return false;  // prefix还没遍历完，就找不到了
            root = root->son[cur];
        }
        return true;  // prefix正常遍历完，就返回true
    }
};
```

