---
title: C++课后练习留档
date: 2024-03-20 17:32:11
categories:
	- CPP
tags:
	- 学习笔记
	- C++
---
# C++ Primer/Essential C++ 课后练习留档
## 第1章开始
### 练习1.3
> 编写程序，在标准输出上打印 Hello World.
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

### 练习 1.7
> 创建一个txt文件，输入两行文字并保存。编写一个程序，打开该文本文件，将其中每个字都读取到一个 vector<string>对象中。遍历 vector，将内容显示到 cout。利用泛型算法 sort()，对所有文字排序，再将排序结果输出至另一文件。
```CPP
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

int main()
{
	ifstream infile("D:\\CODE\\CPPProject\\Data\\Exercise 1.6_IN.txt");
	ofstream outfile("D:\\CODE\\CPPProject\\Data\\Exercise 1.6_OUT.txt");
	if (! infile)
	{
		cout << "Can't find infile." << endl;
	}
	if (! outfile)
	{
		cout << "Can't find outfile." << endl;
	}

	vector<string> PrintInformation;
	string word;
	while (infile >> word)
	{
		PrintInformation.push_back(word);
	}
	for (int i = 0; i < PrintInformation.size(); i++)
	{
		cout << PrintInformation[i];
	}
	cout << endl;

	sort(PrintInformation.begin(), PrintInformation.end());
	for (int j = 0; j < PrintInformation.size(); j++)
	{
		outfile << PrintInformation[j] << ' ';
	}
	outfile << endl;

	return 0;
}
```

## 第二章 面向过程的编程风格
### 练习 2.1
> 编写一个函数，该函数返回斐波那契数列中用户指定的某个位置的元素。改写main()使其允许用户不断输入位置值直到用户希望停止为止。
```CPP
#include <iostream>
using namespace std;

bool fibon_elem(int pos, int& elem);

int main()
{
	int pos;
	bool more = true;
	char change;

	while (more)
	{
		cout << "Please enter a position: ";
		cin >> pos;

		int elem;
		if (fibon_elem(pos, elem))
		{
			cout << "element # " << pos
				<< " is " << elem << endl;
		}
		else cout << "Sorry.Could not calculate element # "
			<< pos << endl;

		cout << "try again?(Y/N)";
		cin >> change;
		
		if ((change != 'Y') && (change != 'y'))
		{
			more = false;
		}
	}


}

bool fibon_elem(int pos, int& elem)
{
	// 检查位置是否合理
	if (pos <= 0 || pos >= 1024)
	{
		elem = 0;
		return false;
	}
	//位置为1、2时elem值为1
	elem = 1;
	int n2 = 1, n1 = 1;
	for (int i = 3; i <= pos; i++)
	{
		elem = n2 + n1;
		n2 = n1;
		n1 = elem;
	}

	return true;
}
```

### 练习 2.2
> Pentagonal数列的求值公式是$P(n)=\frac{n(3n-1)}{2}$,借此产生1，5，12，22，35等值。定义一函数，将产生的元素放到用户传入的vector中，元素个数由用户指定。注意检查元素个数的有效性。再编写一个函数，能将给定vector的所有元素一一打印出来。此函数第二参数接受一个表示数列类型的字符串。最后编写main()测试上述两个函数。
```CPP
//练习写的，和参考答案有出入
#include <iostream>
#include <vector>
#include <string>
using namespace std;


const vector<int>* pent_seq(int size)
{
	const int max_size = 1024;
	static vector<int> elems;
	if (size <= 0 || size > max_size)
	{
		cerr << " pent_seq(): oops: invalid size: " << size << " --can't fulfill request.\n";
		return 0;
	}
	//如果size小于elems.size就不用重新计算了
	for (int i = elems.size(); i < size; i++)
	{
		elems.push_back((i + 1) * (3 * (i + 1) - 1) / 2);
	}
	return &elems;
}

void display_message(const vector<int>& vec, const string &vectype)
{
	cout << vectype;
	for (int i = 0; i < vec.size(); i++)
	{
		cout << vec[i] << ' ';
	}
}

int main()
{
	const string vect = "int : ";
	const vector<int>* pseq = pent_seq(5);
	pseq = pent_seq(7);
	display_message(*pseq, vect);
}

```

### 练习 2.3
> 将练习 2.2 中的 Pentagonal 数列求值函数拆分为两个函数，其中之一为 inline, 用来检验元素个数是否合理。如果合理且尚未被计算便执行第二个函数，执行求值工作。
```CPP
#include <iostream>
#include <vector>
#include <string>
using namespace std;

inline bool is_size_ok(vector<int>& elems, int size);
void pent_seq(vector<int>& elems, int size);


inline bool is_size_ok(vector<int>& elems, int size)
{
	const int max_size = 1024;
	if (size <= 0 || size > max_size)
	{
		cerr << " pent_seq(): oops: invalid size: " << size << " --can't fulfill request.\n";
		return false;
	}

	if (size < elems.size())
	{
		pent_seq(elems, size);
	}
	return true;
}

void pent_seq(vector<int>& elems, int size)
{

	//如果size小于elems.size就不用重新计算了
	for (int i = elems.size(); i < size; i++)
	{
		elems.push_back((i + 1) * (3 * (i + 1) - 1) / 2);
	}
}

void display_message(const vector<int>& vec, const string &vectype)
{
	cout << vectype;
	for (int i = 0; i < vec.size(); i++)
	{
		cout << vec[i] << ' ';
	}
}

int main()
{
	vector<int> elems;
	const string vect = "int : ";
	pent_seq(elems, 5);
	display_message(elems, vect);
}
```

### 练习 2.4
> 写一个函数，以局部静态的 vector 存储数列元素，返回一个 const 指针指向该 vector。若 vector 的大小小于指定元素个数，就扩充 vector 大小。接下来在事先第二个函数，接受一个位置值，返回该位置上的元素。最后编写 main 测试。
```CPP
const vector<int>* save_vec(int size)
{
	static vector<int> vect;
	for (int i = vect.size(); i < size; i++)
	{
		vect.push_back((i + 1) * (3 * (i + 1) - 1) / 2);
	}
	return &vect;
}

int pent_elem(int pos)
{
	const vector<int>* pseq = save_vec(pos);
	if (!pseq)
	{
		return 0;
	}
	return (*pseq)[pos - 1];
}
int main()
{
	int a = pent_elem(6);
	cout << '\n' << "The number is " << a << endl;
}
```