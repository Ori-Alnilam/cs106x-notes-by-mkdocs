# Lec7-9_Recursion
> 课后Recursion练习题（CS106X_2017_Section2）
> - [x] [1. recursionMysteryComma](https://www.codestepbystep.com/problem/view/cpp/recursion/recursionMysteryComma)
> - [x] [2. reverse反转字符串](https://www.codestepbystep.com/problem/view/cpp/recursion/reverse)
> - [x] [3. sumOfSquares](https://www.codestepbystep.com/problem/view/cpp/recursion/sumOfSquares)
> - [x] [4. stutterStack](https://www.codestepbystep.com/problem/view/cpp/recursion/stutterStack)
> - [x] [5. countToBy](https://www.codestepbystep.com/problem/view/cpp/recursion/countToBy)
> - [x] [6. combin](https://www.codestepbystep.com/problem/view/cpp/recursion/combin)这个用到了概率论的知识。练习了lec9学到的memoization
> - [x] [7. isSubsequence](https://www.codestepbystep.com/problem/view/cpp/recursion/isSubsequence)判断是否是子序列
> - [ ] [longestCommonSubsequence](https://www.codestepbystep.com/problem/view/cpp/backtracking/longestCommonSubsequence)不会写
> - [ ] [editDistance](https://www.codestepbystep.com/problem/view/cpp/recursion/editDistance)不想写
> 
> lec7练习题
> - [x] [1. recursionMystery648](https://www.codestepbystep.com/problem/view/cpp/recursion/recursionMystery648)简单的选择题
> - [x] [2. power](https://www.codestepbystep.com/problem/view/cpp/recursion/power)递归实现pow()
> - [x] [3. isPalindrome](https://www.codestepbystep.com/problem/view/cpp/recursion/isPalindrome)递归判断回文字符串

### lec7练习题3：isPalindrome
```cpp
bool isPalindrome(string s) {
    s = toLowerCase(s);
    if (s.size() <= 1) {
        return true;
    } else if (s[0] != s[s.size() - 1]) {
        return false;
    } else {
        string s2 = s.substr(1, s.size() - 2);
        return isPalindrome(s2);
    }
}
```
感觉我写得怪怪的......

### Section2练习题6：Combin
> [!note] 
> **组合数（Combination）**：在数学中，组合数 $C(n,k)$ 表示从 $n$ 个不同元素中选取 $k$ 个元素的方案数，不考虑顺序。公式为：
> $$C(n, k) = \frac{n!}{k! \cdot (n - k)!}$$
> 
> 组合数 $C(n,k)$ 满足以下递推关系：
> 
> $$C(n, k) = C(n-1, k-1) + C(n-1, k)$$
> 
> - 从 nn 个元素中选取 kk 个元素，可以分为两种情况：
> 	
> 	1. 包含第 $n$ 个元素：需要从剩下的 $n−1$ 个元素中选取 $k−1$ 个元素，即 $C(n−1,k−1)$ 。
> 		
> 	2. 不包含第 $n$ 个元素：需要从剩下的 $n−1$ 个元素中选取 $k$ 个元素，即 $C(n−1,k)$ 。
> 		
> - 将这两种情况的方案数相加，就是总的方案数。

```cpp
long long combin(int n, int k) {
    static HashMap<string, long long> m;
    string key = to_string(n) + "," + to_string(k);
    if (m.containsKey(key)) {
        return m[key];
    }
        
    if (k == 0 || k == n) {
        return 1;
    } else if (n < 1 || k < 0 || k > n) {
        return 0;
    } else {
        long long ans = combin(n - 1, k - 1) + combin(n - 1, k);
        m[key] = ans;
        return ans;
    }
}
```
- [!] 如果不用SPL库，只需把HashMap改成`unordered_map`，`if (m.containsKey(key))`改成`if (m.find(key) != m.end()`就可以了。

### Section2练习题7：isSubsequence
```cpp
bool isSubsequence(string s, string subs) {
    if (subs.empty()) {
        return true;
    }
    if (s.find(subs[0]) == std::string::npos) {
        return false;
    } else {
        int index = s.find(subs[0]);
        return isSubsequence(s.substr(index + 1), subs.substr(1));
    }
}
```
感觉自己写得怪怪的+1

- [!] 改进后的代码：
```cpp
bool isSubsequence(const string& s, const string& t, int i = 0, int j = 0) {
    if (j == t.length()) { // 或者if(t.empty())
        return true;
    }
    if (i == s.length()) {
        return false;
    }
    
    if (s[i] == t[j]) {
        return isSubsequence(s, t, i + 1, j + 1);
    } else {
        return isSubsequence(s, t, i + 1, j);
    }
}
```

### 课堂练习1：printBinary
[printBinary](https://www.codestepbystep.com/problem/view/cpp/recursion/printBinary)
> 十进制转化为二进制：**除2取余法**

```cpp
void printBinary(int n) {
    if (n < 0) {
        cout << "-";
        printBinary(-n);
    } else if (n <= 1) {
        cout << n;
    } else {
        printBinary(n / 2);
        cout << n % 2; // 也可以printBinary(n % 2)但我不想多调用一次
    }
}
```

### 课堂练习2：reverseLines
[reverseLines](https://www.codestepbystep.com/problem/view/cpp/recursion/reverseLines)

```cpp
void reverseLines(ifstream& input) {
	string line;
	if (getline(input, line)) {
		reverselines(input);
		cout << line << endl;
	}
}
```

### 课堂练习3：crawl
> [crawl](https://www.codestepbystep.com/problem/view/cpp/recursion/crawl)打印文件目录

```cpp
#include "filelib.h"
void crawl(const string& filename, const string& indentation = "");

void crawl(const string& filename, const string& indentation) {
	cout << indentation << getTail(filename) << endl;
	if (isDirectory(filename)) {
		// recursive case: directory
		Vector<string> files = listDirectory(filename);
		for (string file : files) {
			crawl(filename + "/" + file, indentation + "    ");
		}
	}
	// else, base case: normal file, do nothing
}
```

- [!] 当字符串作为参数传递时，大多时候`const string& s`更好。*（如果不希望字符串被更改的话）*

### 课堂练习4：Fibonacci
> 斐波那契数列1、1、2、3、5、8、13、21、34...

```cpp
#include "hashmap.h"

int fib(int n) {
	if (n <= 2) {
		return 1;
	} else {
		return fib(n - 1) + fib(n - 2);
	}
}
```

- [!] 这种方法虽然可行，效率却很低，有很多不必要的调用。fib(3)已经计算过了，但计算fib(4)的时候还会再计算一遍fib(3)、计算fib(5)的时候又会重复计算fib(3)和fib(4)......
- [!] 所以把计算的结果缓存起来！下次调用时直接return而不是进入递归计算 

### memoization
```cpp
int fib(int n) {
	static HashMap<int, int> cache;

	if (n <= 2) {
		return 1;
	} else if (cache.containsKey(n)) {
		return cache.get[n];
	} else {
		int result = fib(n - 1) + fib(n - 2);
		cache.put(n, result);
		return result;
	}
}
```

函数体内的static变量：

> [!note] 
> 我们想用一个HashMap来缓存fib(n)的值，最开始是在函数外声明一个全局变量
> ```cpp
> HashMap<int, int> cache;
> int fib(int n) {...}
> ```
> 使用global variable是糟糕的！*（全局变量会破坏数据的安全性）*在C++中，可以在函数体内声明一个static变量，相当于“函数的专属global变量”。这样，不会在每次调用函数时都构造一个新的HashMap对象，而只会在函数第一次调用时构造一次，但之后的调用都可以使用这个对象。“仅对此函数可见的global变量”*（这样形容只是方便理解，并不是global变量啊）*

### 课堂练习5：evaluate
> [evaluateMathExpression](https://www.codestepbystep.com/problem/view/cpp/recursion/evaluateMathExpression)
> 讲到了`recursion.h`中的`recursionIndent()`

```cpp
int evalHelper(const string& exp, int& index) {
    if (isdigit(exp[index])) {
        return exp[index++] - '0';
    } else { // if exp[index] == '('
        index++;
        int left = evalHelper(exp, index);
        int op = exp[index++];
        int right = evalHelper(exp, index);
        index++;
        
        if (op == '+') {
            return left + right;
        } else { // op == '*'
            return left * right;
        }
    }
}

int evaluateMathExpression(const string& exp) {
    int index = 0;
    return evalHelper(exp, index);
}
```

### 课堂练习6：fractals

```sql
---------------------------
---------         ---------
---   ---         ---   ---
- -   - -         - -   - -
```

> 绘制一个cantorSet(leval 4)相当于：
> 1. 先绘制一条长线
> 2. 再左右各绘制一个低一级的cantorSet(leval 3)
> 
> 	绘制一个cantorSet(leval 3) 相当于......

```cpp
void cantorSet(GWindow& window, int x, int y, int length, int levels) {
	if (levels >= 1) {
		pause(20); // 可以看到绘制过程
		window.drawLine(x, y, x + length, y);
		cantorSet(window, x, y + 20, length / 3, levels - 1); // 左
		cantorSet(window, x + 2 * length / 3, y + 20, length / 3, levels - 1);
	}
}
```

### 课堂练习7：evenDigits
[evenDigits](https://www.codestepbystep.com/problem/view/cpp/recursion/evenDigits)

```cpp
int evenDigits(int n) {
    if (n == 0) {
        return 0;
    } else {
        int last = n % 10;
        int ans = evenDigits(n / 10);
        if (last % 2 == 0) {
            return ans * 10 + last;
        } else {
            return ans;
        }
    }
}
```


---
