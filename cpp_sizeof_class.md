title: C++中类的大小（Sizeof of Class in C++）
date: 2015-09-20 16:59:44
tags: C++
---

#  介绍
作为面向对象的语言，C++有很多面向对象的特性。对于一个类，如果能够准确判断其占用大小，说明对面向对象有了深入的理解。sizeof是一个操作符，返回一个变量或者对象的大小。

在求职面试的过程中，很多情况下，会涉及到这个问题。所以，这里列举了不同情况下C++中类的大小，默认是在64位机器上的结果。

<!--more-->

#  空类
空类，就是除了构造函数和析构函数之外，没有其他的成员函数或者成员变量。当然，如果我们没有显式地写出构造函数和析构函数，编译器会替我们完成。

``` bash

	class A{
	public:
		A(){}
		~A(){}
	}; 

	int main(){
		A a;
		cout<<sizeof(a)<<endl;
		return 0;
	}
```

运行代码，结果输出为1。对于空类，还是会分配一个字节的位置来标识该类的对象。

#  含有不同数据类型的类
含有不同数据类型的时候，要注意数据的对齐规则。

```
	class A{
	public:
		A(){}
		~A(){}
		int num;
		char ch;
		double n;
	}; 

	int main(){
		A a;
		cout<<sizeof(a)<<endl;
		return 0;
	}
```

运行代码，结果输出为16（32位条件下也是16）。这个涉及到数据对齐，对齐规则是：占空间小的数据类型向占空间大的数据类型看齐，对齐的数目是2的幂次。比如，成员变量ch，由于只占了一个字节，所以要补成4个字节，所以，总共占用的空间是4+（1+3）+8=16。

#  含有虚函数的类
虚函数是个C++多态的重要组成，因为虚函数的存在，编译器会默认生成一个虚函数表和一个指向该表的指针。
```
	class A{
	public:
		A(){}
		~A(){}
		virtual void f(){
			//nothing
		}
		int num;
		char ch;
		double n;
	}; 

	int main(){
		A a;
		cout<<sizeof(a)<<endl;
		return 0;
	}
```
运行代码，结果输出为24（32位条件下是20）。因为虚函数的存在，编译器会默认生成一个指向虚函数表的指针，在64位机器上，指针的大小为8个字节。所以，整个对象的大小是 8+4+（1+3）+8=24。可以看到这里编译器很智能地把int 和char类型的还是对齐成4，这样节省了空间。


#  含有static类型变量的类
在类中添加static类型的变量会发生什么呢？
```
	class A{
	public:
		A(){}
		~A(){}
		virtual void f(){
			//nothing
		}
		int num;
		char ch;
		double n;
		static int s_int;
	}; 

	int main(){
		A a;
		cout<<sizeof(a)<<endl;
		return 0;
	}
```
运行代码，结果输出为24，和上一个类的大小相同。这是因为static类型是在静态存储区，并不属于该类的一个成员。
