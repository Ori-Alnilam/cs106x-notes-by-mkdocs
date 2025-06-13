# Lec19-21_Trees
> - Lec19 二叉树
> - Lec20 二叉搜索树
> - Lec21 Advanced Trees
> 	- 平衡树
> 		- ALV树
> 		- 红黑树
> 		- 伸展树
> 	- 字典树Tries
> 
> 看视频效率太低了，也讲不了多少东西。以后直接看PPT，不懂的地方问GPT
> 1. print
> 2. size
> 3. contains
> 4. getMin
> 5. getMax
> 6. add
> 7. remove

```cpp
struct TreeNode {
	int data;
	TreeNode* left;
	TreeNode* right;

	TreeNode(int d = 0, TreeNode* l = nullptr, TreeNode* r = nullptr) {
		data = d;
		left = l;
		right = r;
	}
}
```

> [!note]
> 可以在构造函数添加*成员初始化列表*：
> ```cpp
> TreeNode(int d = 0, TreeNode* l = nullptr, TreeNode* r = nullptr)
> 	: data(d), left(l), right(r) {}
> ```
> 也可以分开写多个构造函数，可读性更好：
> ```cpp
> // 无参数
> TreeNode() : data(0), left(nullptr), right(nullptr) {}
> // 一个参数的构造函数
> TreeNode(int d) : data(d), left(nullptr), right(nullptr) {}
> // 三个参数
> TreeNode(int d, TreeNode* l, TreeNode* r) 
> 	: data(d), left(l), right(r) {}
> ```

#### 1. print
> 前、中、后序遍历
```cpp
void print(TreeNode* node) {
	if (node != nullptr) {
		// pre-order
		cout << node->data << endl;
		print(node->left);
		// in-order
		print(node->right);
		// post-order
	}
}
```

#### 2. size
```cpp
void size(TreeNode* node) {
	if (node == nullptr) {
		return 0;
	}
	return 1 + size(node->left) + size(node->right);
}
```

#### 3. contains
```cpp
bool contains(TreeNode* node, int value) {
	if (node != nullptr) {
		if (node->data == value) {
			return true;
		}
		if (contains(node->left, value) ||
			contains(node->right, value)) {
			return true;	
		}
	}
	return false;
}
```

> 进一步，当是**二叉搜索树**时
> - BSTs
> 	1. 遍历Traversing
> 		- contains
> 		- getMin/getMax
> 	2. 添加Adding
> 	3. 删除Removing 

```cpp
bool contains(TreeNode* node, int value) {
	if (node != nullptr) {
		if (value == node->data) {
			return true;
		} else if (value < node->data) {
			return contains(node->left, value);
		} else {
			return contains(node->right, value);
		} 
	}
	return false;
}
```

#### 4. getMin
```cpp
// Recursion
int getMin(TreeNode* root) {
	if (root->left == nullptr) {
		return root->data;
	} else {
		return getMin(root->left);
	}
}

// Iteration
int getMin(TreeNode* root) {
	while (root->left != nullptr) {
		root = root->left; // 没有按引用传递，改变的是副本没关系
	}
	return root->data;
}
```

#### 5. getMax
```cpp
int getMax(TreeNode* root) {
	if (root->right == nullptr) {
		return root->data;
	} else {
		return getMax(root->right);
	}
}
```

#### 6. add
```cpp
void add(TreeNode*& node, int value) {
	// Base case
	if (node == nullptr) {
		node = new TreeNode(value);
		return;
	}
	// Recursive case
	if (value < node->data) {
		add(node->left, value);
	} else if (value > node->data) {
		add(node->right, value);
	}
}
```

> [!important]
> 注意按引用传递。

#### remove
```cpp
void remove(TreeNode*& node, int value) {
	if (node == nullptr) {
		return;
	}
	
	if (value < node->data) {
		remove(node->left, value);
	} else if (value > node->data) {
		remove(node->right, value);
	} else {
		// case1: leaf
		if (node->left == nullptr && node->right == nullptr) {
			delete node;
			node = nullptr;
		} else if (node->right == nullptr) {
			// case2: only left subtree
			TreeNode* trash = node;
			node = node->left;
			delete trash;
		} else if (node->left == nullptr) {
			// case2: only right subtree
			TreeNode* trash = node;
			node = node->right;
			delete trash;
		} else {
			// case3: full tree
			int minValue = getMin(node->right);
			node->data = minValue;
			remove(node->right, minValue);
		}
	}
}
```

#### Free Tree
用后序遍历。delete当前节点前，需要先保证左右子树已经全部delete

## Tries
#### containsPrefix
> 字典树

```cpp
bool containsPrefix(TreeNode* node, string prefix) {
	if (node == nullptr) {
		return false;
	} else if (prefix == "") {
		return true;
	} else {
		char cur = prefix[0];
		int index = cur - 'a';
		return containsPrefix(node->children[index], prefix.substr(1));
	}
}
```

感觉还有更好的写法......

---

