# Lec10-12_exhaustive_search
## lec10穷举搜索exhaustive_search
原来我前面写的那些题用到的是**穷举搜索**的知识，到这里才学啊~作业写早了喵

### 课堂练习1：printBinary
```cpp
// printBinary(3) --> 000\n 001\n 010\n 011\n 100\n 101...
void printBinary(int n, string prefix = "") {
    if (n == 0) {
        cout << prefix << endl;
        return;
    }

    printBinary(n - 1, prefix + "0");
    printBinary(n - 1, prefix + "1");
}
```

不知道为什么本地没有`resursion.h`，可以去 CodeStepByStep 上写[这题](https://www.codestepbystep.com/problem/view/cpp/backtracking/printAllBinary)，用`recursionIndent`查看递归调用的情况：

!!! success "recursionIndent"
    斯坦福库里的递归关系可视化函数
    ```cpp
    void printAllBinary(int n, string prefix = "") {
        cout << recursionIndent()
            << "n = " << n
            << ", prefix = " << prefix << endl;
        if (n <= 0) {
            cout << prefix << endl;
            return;
        }

        printAllBinary(n - 1, prefix + "0");
        printAllBinary(n - 1, prefix + "1");
    }
    ```

### 课堂练习2：printDecimal
[printDecimal](https://www.codestepbystep.com/problem/view/cpp/backtracking/printAllDecimal)
```cpp
// printDecimal(2) --> 01, 02, ... , 98, 99
void printDecimal(int n, string soFar = "") {
    if (n <= 0) {
        cout << soFar << endl;
        return;
    }

    for (int i = 0; i <= 9; i++) {
        printDecimal(n - 1, soFar + to_string(i));
    }
}
```

### 课堂练习3：permute
**permutation排列**

[permute](https://www.codestepbystep.com/problem/view/cpp/backtracking/permute)

```cpp
// permute("MENY") --> 
void permute(string s, string prefix = "") {
	if (s.empty()) {
		cout << prefix << endl;
		return;
	}

	for (int i = 0; i < s.size(); i++) {
		string s2 = s.substr(0, i) + s.substr(i + 1);
		permute(s2, prefix + s[i]);
	}
}
```

💡 如果想把permute的结果存入Vector：

```cpp
void permute(string s, Vector<string>& v, string prefix = "");

int main() {
	Vector<string> v;
	permute("MENY", v);
	cout << v << endl;
}

void permute(string s, Vector<string>& v, string prefix) {
	if (s.empty()) {
		v.add(prefix);
		return;
	}

	for (int i = 0; i < s.size(); i++) {
		string s2 = s.substr(0, i) + s.substr(i + 1);
		permute(s2, v, prefix + s[i]);
	}
}
```

💡 如果想让permute返回一个Vector：

```cpp
Vector<string> permute(string s);
void permuteHelper(string s, Vector<string>& v, string prefix);

int main() {
	Vector<string> v = permute("MENY");
	cout << v << endl;
}

Vector<string> permute(string s) {
    Vector<string> v;
    permuteHelper(s, v, "");

    return v;
}

void permuteHelper(string s, Vector<string>& v, string prefix) {
    if (s.empty()) {
        v.add(prefix);
        return;
    }

    for (int i = 0; i < s.size(); i++) {
        string s2 = s.substr(0, i) + s.substr(i + 1);
        permuteHelper(s2, v, prefix + s[i]);
    }
}
```

### 课堂练习4：combin
[combin](https://www.codestepbystep.com/problem/view/cpp/backtracking/combin)

permute 的变体，保证得到的字符串是唯一没有重复的（字符可以重复，比如可以有`AAB`，但不能有两个相同的字符串 ~~{AAB, AAB}~~

```cpp
void combin(string s, string prefix = "") {
    static Set<string> printed;
    if (s.empty()) {
        if (!printed.contains(prefix)) {
            cout << prefix << endl;
            printed.add(prefix);
            return;
        }
    }
    
    for (int i = 0; i < s.size(); i++) {
        string s2 = s.substr(0, i) + s.substr(i + 1);
        combin(s2, prefix + s[i]);
    }
}
```

这个实现不好，还可以优化

[^1]: 这里Helper函数的参数：`const Vector<int>& soFar`，如果去掉`const`修饰符，将无法在调用时把`{}`作为参数传入。
	
	⚠error: cannot bind non-const lvalue reference of type `Vector<int>&` to an rvalue of type `Vector<int>`
	
	（不能在非`const`左值引用`Vector<int>`时，传入右值参数`{}`）

### Problem Six: Eating a Chocolate Bar
[CS106B_Section 2](https://web.stanford.edu/class/cs106b/section/section2/#recursion)

这题是lec10、lec11的知识点：穷尽搜索和回溯法
#### 1. 返回方法数`int`
```cpp
int numWaysToEat(int numSquares) {
    if (numSquares < 0) {
        error("Invalid input");
    }
    
    if (numSquares <= 1) {
        return 1;
    }
    
    return numWaysToEat(numSquares - 1) + numWaysToEat(numSquares - 2);
}
```

memoization优化后：

```cpp
int numWaysToEat(int numSquares) {
    static HashMap<int, int> m;
    if (m.containsKey(numSquares)) {
        return m[numSquares];
    }

    if (numSquares < 0) {
        error("Invalid input");
    }

    if (numSquares <= 1) {
        return 1;
    }

    int result = numWaysToEat(numSquares - 1) + numWaysToEat(numSquares - 2);
    m[numSquares] = result;
    return result;
}
```

#### 2. 打印所有方法数`Vector<int>`
注意const[^1]
```cpp
void printWaysToEatHelper(int numSquares, const Vector<int>& soFar) {
    if (numSquares == 0) {
        cout << soFar << endl;
        return;
    }
    if (numSquares == 1) {
        cout << soFar + 1 << endl;
        return;
    }

    printWaysToEatHelper(numSquares - 1, soFar + 1);
    printWaysToEatHelper(numSquares - 2, soFar + 2);
}

void printWaysToEat(int numSquares) {
	if (numSquares < 0) {
		error("Invalid input!");
	}
	printWaysToEatHelper(numSquares, {});
}
```

- 每次递归调用时，`soFar + 1` 和 `soFar + 2` 这种操作，会生成新的 `Vector<int>` 对象，而不会改变原始的 `soFar` 对象。这就保证了在递归调用返回之后，原始状态不会受到影响，也就无需进行 “回退” 操作。
- 当然也可以用回溯法直接在同一个`Vector<int>`对象上修改啦！

##### 2.1 回溯法（用STL
```cpp
void printWaysToEatHelper(int numSquares, vector<int>& soFar) {
    if (numSquares == 0) {
        cout << "{";
        if (!soFar.empty()) {
            cout << soFar[0];
            for (size_t i = 1; i < soFar.size(); ++i) {
                cout << ", " << soFar[i];
            }
        }
        cout << "}" << endl;
        return;
    }
    if (numSquares == 1) {
        soFar.push_back(1);

        cout << "{";
        if (!soFar.empty()) {
            cout << soFar[0];
            for (size_t i = 1; i < soFar.size(); ++i) {
                cout << ", " << soFar[i];
            }
        }
        cout << "}" << endl;

        soFar.pop_back();
        return;
    }

    soFar.push_back(1);
    printWaysToEatHelper(numSquares - 1, soFar);
    soFar.pop_back();

    soFar.push_back(2);
    printWaysToEatHelper(numSquares - 2, soFar);
    soFar.pop_back();
}

void printWaysToEat(int numSquares) {
    if (numSquares < 0) {
        error("Invalid input!");
    }

    vector<int> soFar;
    printWaysToEatHelper(numSquares, soFar);
	// 注意去掉const修饰符后不能再将{}作为参数了
}
```

因为斯坦福库里 Vector 类没有从末尾删除元素的函数，所以这里用的 STL 更方便一点

事实上，因为没有 SPL 中的 cout ，导致输出 vector 的内容复杂了很多。。

##### 2.2 回溯法（用SPL
```cpp
void printWaysToEatHelper(int numSquares, Vector<int>& soFar) {
    if (numSquares == 0) {
        cout << soFar << endl;
        return;
    }

    if (numSquares == 1) {
        soFar.add(1);
        cout << soFar << endl;
        soFar.remove(soFar.size() - 1);
        return;
    }

    soFar.add(1);
    printWaysToEatHelper(numSquares - 1, soFar);
    soFar.remove(soFar.size() - 1);

    soFar.add(2);
    printWaysToEatHelper(numSquares - 2, soFar);
    soFar.remove(soFar.size() - 1);
}

void printWaysToEat(int numSquares) {
    if (numSquares < 0) {
        error("Invalid input!");
    }

    Vector<int> soFar;
    printWaysToEatHelper(numSquares, soFar);
}
```

#### 3. 返回所有方法数的集合`Set<Vector<int>>`

```cpp
void waysToEatHelper(int numSquares, Set<Vector<int>>& result, const Vector<int>& soFar) {
    if (numSquares == 0) {
        result.add(soFar);
        return;
    }

    if (numSquares == 1) {
        result.add(soFar + 1);
        return;
    }

    waysToEatHelper(numSquares - 1, result, soFar + 1);
    waysToEatHelper(numSquares - 2, result, soFar + 2);
}

Set<Vector<int>> waysToEat(int numSquares) {
    if (numSquares < 0) {
        error("Invalid input!");
    }

    Set<Vector<int>> result;
    waysToEatHelper(numSquares, result, {});
    return result;
}
```

##### 3.1 官方题解
```cpp
Set<Vector<int>> waysToEatRec(int numSquares, const Vector<int>& soFar) {
    if (numSquares == 0) {
        return { soFar };
    } else if (numSquares == 1) {
        return { soFar + 1 };
    } else {
        return waysToEatRec(numSquares - 1, soFar + 1) + 
               waysToEatRec(numSquares - 2, soFar + 2);
    }
}

Set<Vector<int>> waysToEat(int numSquares) {
    if (numSquares < 0) {
        error("You owe me some chocolate!");
    }
   
    return waysToEatRec(numSquares, {});
```

斯坦福库`Set`类重载了`+`运算符，这样写好简单

---

## lec11回溯法recursive backtracking
### 1. 初始回溯法：diceRolls
作业链接：[diceRolls](https://www.codestepbystep.com/problem/view/cpp/backtracking/diceRolls)

作业描述：投掷n枚骰子🎲，打印出所有可能的结果。变量dice代表🎲数量

作业备注：每一个骰子都有6种可能的点数（这也需要备注吗
#### 1.1 前面所学的解法：
```cpp
void diceRollsHelper(int dice, const Vector<int>& soFar) {
    if (dice == 0) {
        cout << soFar << endl;
        return;
    }

    for (int i = 1; i <= 6; i++) {
        diceRollsHelper(dice - 1, soFar + i);
    }
}

void diceRolls(int dice) {
    diceRollsHelper(dice, {});
}
```

#### 1.2 回溯法
```cpp
void diceRollsHelper(int dice, Vector<int>& soFar) {
    if (dice == 0) {
        cout << soFar << endl;
        return;
    }

    for (int i = 1; i <= 6; i++) {
        // - choose
        soFar.add(i);
        
        // - explore
        diceRollsHelper(dice - 1, soFar);
        
        // - unchoose
        soFar.remove(soFar.size() - 1);
    }
}

void diceRolls(int dice) {
    Vector<int> soFar;
    diceRollsHelper(dice, soFar);
}
```

### 2. 剪枝diceSum
🔗[diceSum](https://www.codestepbystep.com/problem/view/cpp/backtracking/diceSum)

🔍上面我们得出的是掷2枚🎲的所有可能情况。

💡现在加一点限制：求掷2枚🎲，且和为7的所有情况

```cpp
void diceSumHelper(int dice, int sum, Vector<int>& soFar, int currentSum) {
    if (dice == 0) {
        if (sum == currentSum) {
            cout << soFar << endl;
            return;
        }
    }

    for (int i = 0; i <= 6; i++) {
        int min = currentSum + i + (dice - 1) * 1;
        int max = currentSum + i + (dice - 1) * 6;

        if (min <= sum && sum <= max) {
	        // - choose
            soFar.add(i);

			// - explore
            diceSumHelper(dice - 1, sum, soFar, currentSum + i);
            
            // - unchoose
            soFar.remove(soFar.size() - 1);
        }
    }
}

void diceSum(int dice, int sum) {
    Vector<int> soFar;
    diceSumHelper(dice, sum, soFar, 0);
}
```

### 3. 子集sublists
🔗[printSubVectors](https://www.codestepbystep.com/problem/view/cpp/backtracking/printSubVectors)

🔍对于集合中的每个元素，有包含在子集中和不包含在子集中两种可能
#### 3.1 用写Assignment 3的方法
```cpp
void sublistsHelper(Vector<string>& v, Vector<string>& chosen) {
    // Base case
    if (v.isEmpty()) {
        cout << chosen << endl;
        return;
    }

    // 选择第一个元素
    string s = v[0];
    Vector<string> remaining = v.subList(1, v.size() - 1);

    // 包含它
    Vector<string> selected = chosen;
    selected.add(s);
    sublistsHelper(remaining, selected);

    // 不包含它
    Vector<string> unselected = chosen;
    sublistsHelper(remaining, unselected);
}

void sublists(Vector<string>& v) {
    Vector<string> chosen;
    sublistsHelper(v, chosen);
}
```

#### 3.2 回溯法

```cpp
void sublistsHelper(Vector<string>& v, Vector<string>& chosen) {
    // Base case
    if (v.isEmpty()) {
        cout << chosen << endl;
        return;
    }

    // - choose
    string first = v[0];
    v.remove(0);

    // 包含它
    chosen.add(first);
    sublistsHelper(v, chosen);
    chosen.remove(chosen.size() - 1);

    // 不包含它
    sublistsHelper(v, chosen);

	// - unchoose
	v.insert(0, first);
}
```

STL写法：

1. `vector<string> remaining(v.bigin() + 1, v.end());`
2. `chosen.push_back(first)` && `chosen.pop_back()`
3. `v.erase(v.begin())` && `v.insert(v.begin(), first)`

---

## lec12回溯法 8 queens
💻课堂练习：

- [x] [travel](https://www.codestepbystep.com/problem/view/cpp/recursion/travel)

```cpp
bool solveQueensHelper(Board& board, int col) {
	if (col >= board.size()) {
		cout << board << endl;
		return true;
	}

	for (int row = 0; row < board.size(); ++row) {
		// -choose
		if (board.isSafe(row, col)) {
			board.place(row, col);
		}

		// - explore
		int result = solveQueensHelper(board, col + 1);
		if (result) {
			return result;
		}

		// - unchoose
		board.remove(row, col);
	}
	return false;
}

void solveQueens(Board& board) {
	solveQueensHelper(board, 0);
}
```

---

