---
title: CPP核心编程
date: 2024-03-20 14:39:17
categories:
	- CPP
tags:
    - 学习笔记
    - C++
top: 30
---

> 第一章内容基于视频84-90节内容，第二章后内容基于Essential C++.
## 1 内存分区模型
C++程序执行时，将内存大致分为4个区域：
- 代码区
- 全局区
- 栈区
- 堆区

### 1.1 程序运行前
程序编译后，生成了 exe 可执行文件，未执行该程序前分为两个区域：

代码区：

- 存放CPU执行的机器指令
- 代码区是**共享**的。对于被频繁执行的程序，只需存储一份代码
- 代码区是**只读**的。防止程序意外修改它的指令。

全局区：
- 存放全局变量(函数外定义的变量)、静态变量（static）、常量（3种）
- 该区域数据程序结束后由操作系统释放。

### 1.2 程序运行后
栈区：
- 编译器自动分配释放，存放函数的参数值，局部变量等；
- 注：不要返回局部变量的地址。

堆区：
- 堆区数据由程序员管理和释放
- 堆区数据由new关键字开辟内存
```cpp
int *p = new int(10);
```

### 1.3 new运算符
在堆区开辟数据，由 delete 释放。
```cpp
int *arr = new int[10]; //分配10个int数据组成的数组，arr指针指向第一个Int
delete[] arr;   //arr必须指向一个动态分配的数组或者为空
```

### 1.4 引用
#### 1.4.1 基本操作
作用：给变量起别名
```cpp
int a = 10;
int &b = a; //给a起一个别名为b
```
- 引用必须初始化
- 引用一经初始化后不可更改

#### 1.4.2 引用做函数参数及返回值
**作用**：函数传参时，可以用引用让形参修饰实参，简化指针修改实参。

```cpp
int *find(const vector<int> &vec, int value)//vec代表对vec这个vector的引用，避免拷贝整个vector
{
    for(int ix = 0; ix < vec.size; ++ix)
    {
        if(vec[ix] == value)
            return &vec[ix];
        return 0;
    }
}
```
**注意**：不要返回局部变量引用

## 2 面向过程的编程风格
### 2.1 编写函数
函数必须先被声明，然后才能被调用。

如果函数返回类型不为 void，那它必须在每个可能的退出点上将值返回。

### 2.2 调用函数
当调用一个函数时，会在内存中建立起一块特殊区域，称为“**程序堆栈**”。这块区域提供了每个函数参数的存储空间。他也提供了函数所定义的每个对象的内存空间，称为局部对象。一旦函数完成，这块内存会被释放掉。

#### 2.2.1 Pass by Reference语义

**reference**:引用，见1.4节。

当我们以 by reference 方式将对象作为函数参数传入时，对象本身并不会复制出另一份，**复制的是对象的地址。**

将函数声明为 reference (引用)的理由：
- 希望直接对传入对象进行修改
- 降低复制大型对象的额外负担

例如，将 vector 以传值方式传入 display() 中：
```CPP
void display(const vector<int> &vec)
{···}
int main()
{
    display(vec);
}
```
本例中由于函数中不会更改 vector 的内容，故少了 const 不会有错误，但加上 const 可以让阅读程序的人了解，我们用传址的方式传递 vector。

**pointer**: 以 pointer 形式传递的效果相同，唯一差别在于用法不同。如：
```CPP
void display(const vector<int> *vec)
{···}
int main()
{
    display(&vec);
}
```

#### 2.2.2 作用域及范围
为对象分配的内存，其存活时间称为**储存期**或**范围**。每次函数执行，会为函数内变量分配内存，函数结束便会释放。我们称此对象具有**局部性范围**。

对象在程序内的存活区域称为该对象的**作用域**(scope)。
- 局部作用域(local scope)：名称在 local scope 以外不可见。
- file scope：从其声明点至文件末尾都可见。

#### 2.2.3 动态内存管理
见第 1 章。

### 2.3 提供默认参数值
C++允许我们为全部或部分参数设定默认值。如下例所示，指定 ofstream 指针默认值为0。
```CPP
void bubble_sort(vector<int &vec>, ofstream *ofil = 0)
{···}//必须是pointer而非reference，因为reference无法被设置为0

//另一种让cout称为默认的ofstream参数
void display(const vector<int> &vec, ofstream &os = cout)
{···}
```
- 默认值操作必须由最右边开始进行，即提供默认值参数的右侧参数必须具有默认值。
- 默认值只能指定一次。为了更高可见性，一般可将默认值放在开头的函数声明处而非定义处。

### 2.4 使用局部静态对象
定义在函数中的局部静态对象，其所处的内存空间，即使在不同的函数调用过程中，依然持续存在。
```CPP
fibon_seq(int size)
{
    static vector<int> elems;
    ···
}
```

### 2.5 声明inline函数
将函数声明为 **inline（内联）**，表示要求编译器在每个函数调用点上将函数的内容展开。对一个 inline 函数，编译器可**选择**将该函数的调用操作改为以一份函数代码副本代替。相当于将函数写入进来。
```CPP
inline bool fibon_elem(int pos,int &elem)
{···}
```
适合声明为 inline 的函数：体积小，常被调用，所从事的计算不复杂。

### 2.6 提供重载函数
有时我们需要一个通用的函数，其传入的参数的类型和数量可能不尽相同，此时需要通过**函数重载**机制实现。

参数列表不相同的多个函数，可以拥有相同的函数名称。编译器会将调用者提供的实际参数拿来和每个重载函数的参数比对，找出其中最合适的。

```CPP
void display_message(char ch);
void display_message(const string&);
void display_message(const string&, int);
void display_message(const string&, int, int);
```

编译器**无法根据函数的不同的返回类型**来区分两个相同名称的函数。

将一组实现代码不同但工作内容相似的函数加以重载，可以让函数用户更容易使用这些函数，也使得我们不需要对类似函数进行多个命名。

### 2.7 定义模板函数
如果我们希望程序代码的主体不变，仅仅改变其中用到的数据类型，可以通过**函数模板**(function template)将参数列表中的指定参数的类型信息抽离出来。例如：
```CPP
template <typename elemType>
void display_message(const string& msg, const vector<elemType>& vec)
{
	cont << msg;
	for (int i = 0; i < vec.size; i++)
	{
		elemType t = vec[i];
		cout << t << ' ';
	}
}
```

使用函数模板时，只需将 elemType 换为对应的类型。
```CPP
vector<string> ivec;
string msg;
display_message(msg, ivec);
```

### 2.8 函数指针
现在假如有一组“通过vector返回六种数列”的函数如：`const vector<int> *fibon_seq(int size)`，现在需要实现找到指定数列的指定数值的函数。如果我们不想再定义6个不同的函数，那就需要使用**函数指针**实现。
```CPP
const vector<int>* (*seq_ptr)(int)
```
函数指针必须指明函数的返回类型`const vector<int>*`和参数列表`int`。（*seq_ptr)中 \*表示是一个指针变量，seq_ptr 是这个变量的名称。

要取得函数的地址，直接赋值函数的名称即可。

### 2.9 设定头文件
把函数声明放在头文件中，以供多个程序文件调用。

对象的定义需要前加关键字 **extern**。

## 3 泛型编程风格
### 3.1 指针的算术运算
我们希望在一个函数中可以处理任意数据类型，包括 vector 和 array。

当数组被传递给函数，或从函数中返回时，仅有第一个元素的地址会被传递。因此一般都是传入指向数组的指针，以便对 array 进行读取操作。
如：`int min(int *array){···}`

当我们以指针指向 array 第一个元素的方式传入函数时，仍然可以通过下标运算符 [] 访问每个元素，**就如同此 array 是个对象一般。**
```CPP
array[2];
*(array + 2);//两种方式都返回array第三个元素
```

注意，vector 可以为空，array 则不然。

指针的算数运算**必须先假设所有元素存储在连续空间里**，然后才能根据当前的指针，加上元素大小后，指向下一个元素。

**list** 也是一个容器，其连接方式为双向链表。

但由于list存储的元素并不处在一片连续的空间，假设要实现find()函数，原有的指针操作在list容器无法实现。由此引出**Iterator泛型指针**概念，把底层指针的处理放在此抽象层中，让用户无须直接面对指针操作。

### 3.2 了解Iterator(泛型指针)
泛型指针（iterator）（又称迭代器）很像指针，但又有一些新特性。我们需要`iterator`在被定义时有看到如下特性：
- 迭代对象（某个容器）的类型，这可以决定如何访问下一个元素。（比如vector和list的区别）
- iteraotr所指的元素类型，这可以决定iterator提领操作（*）的返回值。

定义iterator：
```CPP
vector<string> svec;
vector<string>::iterator iter = svec.begin();   //双冒号代表此iterator是位于string vector定义内的嵌套类型。
```
对于对const vector进行遍历操作：注意const_iterator指针值是可变的，但其指向的元素不可变。
```CPP
const vector<string> cs_vec;
vector<string>::const_iterator iter = cs_vec.begin();
```
欲通过iterator取得元素值，和指针一样进行提领操作即可。
```CPP
cout << *iter;
```

根据以上规则，重写display函数，使iterator代替subscript（下标）（ps:就是数组后的[]）:
```CPP
template <typename elemType> void display(const vector<elemType>& vec, ostream& os)
{
	vector<elemType>::const_iterator iter = vec.begin();
	vector<elemType>::const_iterator end_it = vec.end();

	for (; iter != end_it; ++iter)
	{
		os << *iter << ' ';
		os << endl;
	}
}
```
现在重新实现`find()`,使其同时支持两种形式：一对指针，或是一对指向某种容器的iterator.

> 假如用户希望赋予equaily运算符不同的意义，需要增强find()的弹性。为此，下一步要将现有的find()演化为泛型算法。

### 3.3 所有容器的共通操作
共通操作：
- `==`和`！=`
- `=`
- `empty()`
- `size()`
- `clear()`

除此之外，每个容器还包括：`begin()`和`end()`;`insert()`和`erase()`.

### 3.4 使用顺序性容器
顺序性容器主要有`vector`,`list`,`deque`（双端队列，类似于vector,但可对头元素进行插入、删除操作）。

上三种容器均有两个特别操作函数：`push_back()`和`pop_back()`.用来在末端插入/删除一个元素。其中list和deque还有针对头部的`push_front()`和`pop_front`

### 3.5 使用泛型算法
使用算法需要包含algorithm头文件。

### 3.6 如何设计一个泛型算法
> 新任务是，在原有的找到小于某个数的函数的基础上，能使用户指定不同的比较操作。为此需要将“比较操作”参数化。

`function object` :某种class的实例对象，这类class对function call运算符做了重载操作（？），**可使function object被当作一般函数来使用。**

`function object Adapter`:函数对象适配器（？）

这里以less<type>为例，取代了原本的函数指针，使用时`less<int>`和bind1st/bind2nd结合使用，如bind2nd会指定值(是val?)绑定至第二操作数。
```CPP
vector<int> filter_ver2(const vector<int>& vec, int val,const less<int>& lt)
{
	vector<int> nvec;
	vector<int>::const_iterator iter = vec.begin();

	while ((iter = find_if(iter, vec.end(), bind2nd(lt, val))) != vec.end())
	{
		nvec.push_back(*iter);
		iter++;
	}
	return nvec;
}
```


### 3.7 使用Map
Map类型是多个一对值，即key-value的组合。

### 3.8 使用Set
Set类型一群key组合而成。

### 3.9 如何使用Iterator Inserter
这节主要讲了三个insertion adapter，用来取代assignment（赋值）操作符，如back_inserter，他用push.back()函数取代赋值操作符，这样就不用一开始就指定好目标容器（可能是用于存放复制后的数据）。

### 3.10 使用iostream Iterator
这节主要讲了两个有关输入输出的Iterator:istream_iterator和ostream_iterator，可支持单一类型的元素读取和写入。

对于输入，其last **iterator**不指定对象即可，这可以代表last是“要读取的最后一个元素的下一位置”。

## 4 基于对象的编程风格
### 4.1 如何实现一个Class
本节以实现一个Stack类为引子介绍如何写一个最简单的Class.实现一个Class主要分为两部分：.h文件和.cpp文件。其中，.h文件包含对该Class的定义；.cpp包含了member function的具体定义。（当然也可以在class主体内定义）

Stack Class定义的主体壳子如下：
```CPP
class Stack
{
public:
	bool push(const string&);
	bool pop(string& elem);
	bool peek(string& elem);
	
	bool empty();
	bool full();

	int size() { return _stack.size(); }

	bool find(const string &value);

	int count(const string &value);
private:
	vector<string> _stack;//习惯上给private变量加下划线
};
```
在.cpp定义member function时，要注意使用如下语法：
```CPP
Stack::pop(); //::类作用域解析符告诉了编译器这是Stack class的一个member。
```

