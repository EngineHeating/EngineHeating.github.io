---
title: CPP基础
date: 2024-03-20 17:31:17
tags:
    - 学习笔记
    - C++
---
## 基础知识
在基础知识部分，好像只有头文件的引用和输入输出函数发生了变化。

头文件下加入`using namespace std;`

`#include<stdio.h>——>#include< iostream>`

`printf——>cout`

`scanf——>cin`

C++有字符串类型string,这是C语言所不具备的。

### 指针
#### 基础概念
```CPP
int a = 10;             //假设a的地址为1000
int *p = &a;
```

这是定义了一个指向int形变量a的指针。此时`p = &a = 1000`,`*p = a = 10`。
> &：取址运算符，取&后面的变量的地址值。
> 
> *： 取值运算符：取\*后面的地址里保存的变量值。

#### 指针传入函数
调用一个函数时，通常不会将变量直接传入函数，而是将调用的变量复制一份副本传入函数。

```cpp
#include <iostream>
using namespace std;
//没用指针，交换失败
void swap(int a, int b)
{
    int temp = a;
    a = b;
    b = temp;
}
int main()
{
    int a = 10;
    int b = 20;
    swap(a, b);
    cout << "a:" << a << endl;
    cout << "b:" << b << endl;
}
//a:10
//b:20
```

```cpp
#include <iostream>
using namespace std;

void swap(int *a, int *b)
{
    int temp = *a;
    *a = *b;
    *b = temp;
}
int main()
{
    int a = 10;
    int b = 20;
    swap(&a, &b);
    cout << "a:" << a << endl;
    cout << "b:" << b << endl;
}
//a:20
//b:10
```

#### 指针和数组
```cpp
int b[5];   //假设b[0]地址为1500，则b[1]地址为1504……
```
不能对b进行别的操作，想要用指针对数组进行操作，需定义一个新的指针指向这个数组，如：`int *pb = &b[0];`.如果此时进行`pb++`，则`pb = &b[1]`。