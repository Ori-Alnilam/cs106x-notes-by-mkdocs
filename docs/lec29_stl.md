# Lec29_STL
!!! note
    #### 迭代器
    1. C++ 的迭代器使得遍历各种容器变得统一。迭代器类似指针，重载了`*`、`++`等运算符。同时STL提供了大量基于迭代器的算法`include <algorithm>`：`for_each`、`find`、`count`、`sort`......
    2. 用迭代器遍历 collection 时，`++it`前置会提高一点点性能，因为后置`it++`需要先返回旧值，再增加到新值，多了一个迭代器的复制操作。而前置`++it`会直接增加到新值

    #### 智能指针( smart pointers )
    - C++11 后引入了智能指针，可以自动管理内存，而不需要手动 delete
    - `include <memory>`后可以使用 unique_ptr 等模板类


