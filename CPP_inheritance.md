---
title: cpp 代码类的多边继承
description: 在C++中，继承是一种面向对象编程的特性，它允许我们定义从一个类继承属性和方法的新类。在新的类中，可以添加新的属性和方法，或者重写已有的方法。
tag:
 - shell
categories:
 - linux
 - code
 - cpp
---

一个子类继承多个父类 - 示例：

	#include <iostream>
	
	class cat
	{
	public:
	        bool sheng() { printf("cat sheng\n"); return true; }
	};
	
	class fox
	{
	public:
	        bool sheng() { printf("fox sheng\n"); return true; }
	};
	
	class native: public cat, public fox
	{};
	
	int main()
	{
	    native na;
		// 通过 static_cast<>() 方法获取类变量
	    static_cast<fox>(na).sheng();
		// 通过 作用域:: 方法获取类变量
	    na.cat::sheng();
	    return 0;
	}


多个子类继承同一个父类 - 示例：

	#include <iostream>
	
	class native
	{
	public:
	        virtual bool sheng() = 0;  // 所有继承类都需要实现基类的纯虚函数
	        std::string name;  // 进行区分的变量
	};
	
	class cat : public native
	{
	public:
	        bool sheng() { printf("%s sheng\n", name.c_str()); return true; }
	};
	
	class fox : public native
	{
	public:
	        bool sheng() { printf("%s sheng\n", name.c_str()); return true; }
	};
	
	int main()
	{
	    cat naCat;
	    fox naFox;
	
		// 设置名称， 以便运行时检查效果
	    naCat.name = "cat";
	    naFox.name = "fox";
	
		// 运行并检查效果
	    naFox.sheng();
	    naCat.sheng();
	
	    return 0;
	}


菱形继承 - 示例：

	#include <iostream>
	
	class native
	{
	public:
	        virtual bool sheng() = 0;
	        std::string name;
	};
	
	class cat : public native
	{
	public:
	        bool sheng() { printf("%s sheng\n", name.c_str()); return true; }
	};
	
	class fox : public native
	{
	public:
	        bool sheng() { printf("%s sheng\n", name.c_str()); return true; }
	};
	
	class eat: public cat, public fox
	{
	};
	
	int main()
	{
	        eat ee;
	        fox efox = static_cast<fox>(ee);
	
	        ee.cat::name = "cat";
	        efox.name = "fox";
	
	        efox.sheng();
	        ee.cat::sheng();
	
	        return 0;
	}










