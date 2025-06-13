# Lec13-15_Linked_Lists
> 💻课后练习：
> #### Lec13：
> - [x] [1. v1v2p1p2](https://www.codestepbystep.com/problem/view/cpp/pointers/v1v2p1p2)
> - [x] [2. parameterMystery1](https://www.codestepbystep.com/problem/view/cpp/pointers/parameterMystery1)
> #### Lec14
> - [x] [1. min](https://www.codestepbystep.com/problem/view/cpp/collectionimpl/linkedlistptr/min)
> - [x] [2. countDuplicates](https://www.codestepbystep.com/problem/view/cpp/collectionimpl/linkedlistptr/countDuplicates)

Lec13指针的内容都是已经会的，没有新东西；Lec14在讲座结束的时候提到了：
```cpp
void add(ListNode*& first, int n) {...}
```
这个`&`在C里面是没有的（吧）。应该是因为当链表为空时，if语句里修改了作为参数传入的指针的指向，所以要**按引用传递**（我猜）。相应的，在C里面如果也要修改传入的指针参数，应该是`ListNode** first`这样？（忘了）
> 看完Lec15后的解答：yesyes, 就是你想的那样。在C里面我们还需要用二级指针，C++只需加上`&`就可以了。以及C学过的，这就忘了（鄙夷

#### 定义链表节点
```cpp
struct ListNode {
	int val;
	ListNode* next;

	ListNode() : val(0), next(nullptr) {}
	ListNode(int x) : val(x), next(nullptr) {}
	ListNode(int x, ListNode* next) : val(x), next(next) {}
}
```

#### vectorToLinkedList()
```cpp
ListNode* vectorToLinkedList(const vector<int>& v) {
	if (v.empty()) {
		return nullptr;
	}

	ListNode* front = new ListNode(v[0], nullptr);
	ListNode* tmp = front;
	for (int i = 1; i < v.size(); i++) {
		tmp->next = new ListNode(v[i], nullptr);
		tmp = tmp->next;	
	}
	return front;
}
```

#### size()
```cpp
int size(ListNode* first) {
	int size = 0;
	ListNode* tmp = first;
	while (tmp != nullptr) {
		size++;
		tmp = tmp->next;
	}
	return size;
}
```

```cpp
int size(ListNode* first) {
	if (first == nullptr) {
		return 0;
	}

	return 1 + size(first->next);
}
```

#### print()
```cpp
void print(ListNode* first) {
	ListNode* tmp = first;
	while (tmp != nullptr) {
		cout << tmp->data << " -> ";
		tmp = tmp->next;
	}
	cout << "nullptr" << endl;
}
```

#### get()
```cpp
int get(ListNode* first, int index) {
	ListNode* tmp = first;
	for (int i = 0; i < index; i++) {
		tmp = tmp->next;
	}
	return tmp->data;
}
```

```cpp
int get(ListNode* first, int index) {
	if (index == 0) {
		return first->data;
	}

	return get(first->next, index - 1);
}
```

`Shift + Ctrl + R`rename

#### addLast()
```cpp
void add(ListNode*& first, int n) {
	ListNode* newNode = new ListNode(n);
	if (first == nullptr) {
		first = newNode;
		return;
	}

	ListNode* tmp = first;
	while (tmp->next != nullptr) {
		tmp = tmp->next;
	}
	tmp->next = newNode;
}
```

#### addFirst()
```cpp
void addFirst(ListNode*& first, int n) {
	ListNode* newNode = new ListNode(n);
	newNode->next = first;
	first = newNode;

	// or: 
	// first = new ListNode(n, first);
}
```

#### removeFirst()
```cpp
void removeFirst(ListNode*& first) {
	if (first != nullptr) {
		ListNode* trash = first;
		first = first->next;
		delete trash;
	}
}
```

> [!note]
> #### delete
> 在C++中，如果一块内存没有指针指向，这块内存在程序运行结束前都无法再使用（C++不会回收），造成内存泄漏。所以需要手动`delete`管理内存。`delete trash`会删除指针指向的那块在**堆**上的内存，而指针本身存储在函数的局部变量**栈**上，栈上的内存会在函数返回时被清理掉。
> 
> 记得在Java里删除链表头节点只需一行`first = first.next`就可以了，因为像Java、Python、C#、JavaScript、Ruby等都会自动管理内存，没有指针指向的内存块会自动被回收。

#### remove()
```cpp
void remove(ListNode*& first, int index) {
	if (first == nullptr) {
		throw "Cannot remove of empty list :(";
	}
	
	if (index == 0) {
		ListNode* trash = first;
		first = first->next;
		delete trash;
	} else {
		ListNode* tmp = first;
		for (int i = 0; i < index - 1; i++) {
			tmp = tmp->next;
		}
		ListNode* trash = tmp->next;
		tmp->next = tmp->next->next;
		delete trash;
	}	
}
```

#### insert()
```cpp
void insert(ListNode*& first, int index, int value) {
	if (index == 0) {
		front = new ListNode(value, front);
		return;
	}

	ListNode* tmp = first;
	for (int i = 0; i < index - 1; i++) {
		tmp = tmp->next;
	}
	tmp->next = new ListNode(value, tmp->next);
}
```

## 实现LinkedIntList类
> 学完Lec16-17后实现链表类

#### 头文件
1. **ListNode.h**
```cpp
#ifndef LISTNODE_H
#define LISTNODE_H

struct ListNode {
    int data;           // element stored in each node
    ListNode* next;   // pointer to the next node (nullptr if none)

    ListNode(const int d = 0, ListNode* n = nullptr) {
        data = d;
        next = n;
    }
};

#endif
```

2. **LinkedIntList.h**
```cpp
#ifndef LINKEDINTLIST_H
#define LINKEDINTLIST_H

#include "ListNode.h"
#include <iostream>
using namespace std;

class LinkedIntList {
public:
    // constructor & destructor
    LinkedIntList();
    ~LinkedIntList();

    // public member functions
    void add(int value); // addLast
    void addFront(int value);
    int get(int index) const;
    void insert(int index, int value);
    bool isEmpty() const;
    void removeFront();
    void removeBack();
    void remove(int index);
    int size() const;

    // friend
    friend ostream& operator <<(ostream& out, const LinkedIntList& list);

private:
    // private member variables
    ListNode* front;
    int length;
};

// operator overload
ostream& operator <<(ostream& out, const LinkedIntList& list);

#endif
```

#### 实现文件
**LinkedIntList.cpp**

```cpp
#include "LinkedIntList.h"
#include <iostream>
using namespace std;

// constructor & destructor
LinkedIntList::LinkedIntList() {
    front = nullptr;
    length = 0;
}

LinkedIntList::~LinkedIntList() {
    ListNode* curr = front;
    while (curr != nullptr) {
        ListNode* nextNode = curr->next;
        delete curr;
        curr = nextNode;
    }
}

// member functions
void LinkedIntList::add(int value) {
    length++;
    if (front == nullptr) {
        front = new ListNode(value);
        return;
    }

    ListNode* curr = front;
    while (curr->next != nullptr) {
        curr = curr->next;
    }
    curr->next = new ListNode(value);
}

void LinkedIntList::addFront(int value) {
    length++;
    front = new ListNode(value, front);
}

int LinkedIntList::get(int index) const {
    ListNode* curr = front;
    for (int i = 0; i < index; i++) {
        curr = curr->next;
    }
    return curr->data;
}

void LinkedIntList::insert(int index, int value) {
    length++;
    if (index == 0) {
        front = new ListNode(value, front);
        return;
    }

    ListNode* curr = front;
    for (int i = 0; i < index - 1; i++) {
        curr = curr->next;
    }
    curr->next = new ListNode(value, curr->next);
}

bool LinkedIntList::isEmpty() const {
    return front == nullptr;
}

void LinkedIntList::removeFront() {
    if (front != nullptr) {
        ListNode* trash = front;
        front = front->next;
        delete trash;
        length--;
    } else {
        throw "Cannot remove front of empty list :(";
    }
}

void LinkedIntList::removeBack() {
    if (front == nullptr) {
        throw "Cannot remove back of empty list :(";
    } else {
        remove(length - 1);
    }
}

void LinkedIntList::remove(int index) {
    if (index == 0) {
        removeFront();
        return;
    }

    ListNode* curr = front;
    for (int i = 0; i < index - 1; i++) {
        curr = curr->next;
    }
    ListNode* trash = curr->next;
    curr->next = trash->next;
    delete trash;
    length--;
}

int LinkedIntList::size() const {
    return length;
}

// operator overload
ostream& operator <<(ostream& out, const LinkedIntList& list) {
    ListNode* curr = list.front;
    while (curr != nullptr) {
        out << curr->data << " ";
        curr = curr->next;
    }
    return out;
}
```

**官方析构函数实现**：
```cpp
template <typename T>
LinkedListClass<T>::~LinkedListClass() {
    clear();
}

template <typename T>
void LinkedListClass<T>::clear() {
    deleteListStartingAt(front);
    front = nullptr;
    currSize = 0;
}
  
template <typename T>
void LinkedListClass<T>::deleteListStartingAt(ListNode<T> *node) {
    if (node == nullptr) return;
    deleteListStartingAt(node->next);
    delete node;
}
```

#### 客户端文件
**main.cpp**

```cpp
#include <iostream>
#include "console.h"
#include "LinkedIntList.h"
using namespace std;

int main() {
	LinkedIntList myList;
	myList.add(42);
	myList.add(-3);
	myList.add(17);
	myList.add(9);

	myList.addFront(888);

	cout << "After adds:" << endl;
	cout << "List: " << myList << endl;
	cout << "Size: " << myList.size() << endl;

	myList.removeFront();
	myList.removeBack();

	cout << "After removes:" << endl;
	cout << "List: " << myList << endl;
	cout << "Size: " << myList.size() << endl;
}
```

---

