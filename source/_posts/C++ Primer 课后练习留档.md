---
title: C++课后练习留档
date: 2024-03-20 17:32:11
tags:
---
# C++ Primer/Essential C++ 课后练习留档
## 第1章开始
### 练习1.3
> 编写程序，在标准输出上打印Hello World.
```CPP
#include <iostream>
int main()
{
	std::cout << "Hello, World";
	return 0;
}
```


### 练习1.4
> 我们的程序使用加法运算符+将两个数相加。编写程序使用乘法运算符*打印两个数的积。
```cpp
#include <iostream>
int main()
{
	int mul1, mul2;
	std::cin >> mul1 >> mul2;
	std::cout << mul1 * mul2 << std::endl;
}
```


### 练习1.5
> 我们将所有输出操作放在一条很长的语句中。重写程序，将每个运算对象的打印操作放在一条独立的语句中。
```cpp
#include <iostream>
int main()
{
	std::cout << "Enter two numbers:" << std::endl;
	int v1 = 0, v2 = 0;
	std::cin >> v1 >> v2;
	std::cout << "The number of ";
	std::cout << v1;
	std::cout << " and ";
	std::cout << v2;
	std::cout << " is ";
	std::cout << v1 + v2 << std::endl;
}
```


### 练习1.6
> (非书上题)输出所有三位数的水仙花数。
```cpp
#include <iostream>
using namespace std;
int main()
{
	int m = 100;
	int i = 1;
	int j = 0;
	int k = 0;
	do
	{
		if ((i * 100 + j * 10 + k) == (i*i*i + j*j*j + k*k*k))
		{
			cout << m << endl;
		}
		m++;
		k++;
		if (k == 10)
		{
			k = 0;
			j++;
			if (j == 10)
			{
				j = 0;
				i++;
			}
		}
	} while (m < 1000);
}
```