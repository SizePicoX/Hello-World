# Hello-World
类currency


//#include<>直接从编译器自带的函数库中寻找文件
//#include""是先从自定义的文件中找 ，如果找不到在从函数库中寻找文件


#ifndef CURRENCY_H
#define CURRENCY_H

#include "stdafx.h"
#include "iostream"
using namespace std;

class currency
{
	enum signType
	{
		plus, minus      //正号，负号
	};

	friend ostream& operator << (ostream& out, const currency& x);
	friend void operator >> (currency& p, double x);

public:

	void Show();        //用于调试
	currency(double theamount = 0.0);  //输入总金额，返回符号，美元，美分
	~currency();   //析构函数
	currency add(const currency& p)const;    //将两个对象相加，并返回一个临时对象  //创建临时对象
	currency& increment(const currency& p);  //将a,b两个对象相加，并返回已有的对象a      //不创建临时对象
	void percent(double x);    //返回currency类对象，它的值为调用对象的p%
	void multiply(double x);    //返回一个currency类对象，它的值为调用对象与x的乘积
	void divide(double x);    //返回一个currency类对象，这个对象是调用对象与x相除的值

private:

	void dealData(double theamount);
	currency& try_percent(double x);
	currency& try_multiply(double x);
	currency& try_divide(double x);
	signType sign = plus; unsigned long dollars = 0; unsigned int cents = 0;

};

void currency::Show()
{
	cout << "sign is  " << sign << endl;
	cout << "dollars is  " << dollars << endl;
	cout << "cents is  " << cents << endl;
	cout << '\n';
}

currency::currency(double theamount)    //不使用judgeParameter函数，并且输入数据仅保留两位小数
{
	dealData(theamount);
}

currency::~currency()
{
}

void currency::dealData(double theamount)
{
	if (theamount < 0)
	{
		sign = minus;
		theamount = -theamount;
	}
	dollars = (int)theamount;
	if (theamount - dollars != 0)       //判断小数点后有没有数字
	{
		cents = (int)(((theamount + 0.001) - dollars) * 100);
	}
}

currency currency::add(const currency& p)const
{
	//为什么  currency result 会报错?
	double theamount1, theamount2;
	theamount1 = dollars + cents / 100.0;
	theamount2 = p.dollars + p.cents / 100.0;
	if (sign == minus)
	{
		theamount1 = -theamount1;
	}
	if (p.sign == minus)
	{
		theamount2 = -theamount2;
	}
	double thesum;
	thesum = theamount1 + theamount2;
	currency result(thesum);
	return result;
}

currency& currency::increment(const currency& p)
{
	*this = add(p);            //this指针指向这个函数的调用对象
	return *this;
}

currency& currency::try_percent(double x)
{
	if (x > 0)
	{
		double theamount = dollars + cents / 100.0;
		double t = x / 100.0;
		theamount *= x / 100.0;
		if ((*this).sign == minus)
		{
			theamount = -theamount;
		}
		//currency(*this)(theamount);
		//currency p(theamount);               
		dollars = (int)theamount;  cents = (theamount + 0.001 - dollars) * 100;
		return *this;
	}
	else
	{
		throw  "所输入的百分数必须大于0才有意义\n";
	}
}

void currency::percent(double x)
{
	try
	{
		try_percent(x);
	}
	catch (char* str)
	{
		cout << str;
	}
}

currency& currency::try_multiply(double x)
{
	if (x >= 0)
	{
		double theamount = dollars + cents / 100.0;
		theamount *= x;
		if ((*this).sign == minus)
		{
			theamount -= theamount;
		}
		dollars = (int)theamount;  cents = (theamount + 0.001 - dollars) * 100;
		return *this;
	}
	else
	{
		throw "乘子为负数时没有意义\n";
	}
}

void currency::multiply(double x)
{
	try
	{
		try_multiply(x);
	}
	catch (char* str)
	{
		cout << str;
	}
}

currency& currency::try_divide(double x)
{
	if (x > 0)
	{
		double theamount = dollars + cents / 100.0;
		theamount /= x;
		if ((*this).sign == minus)
		{
			theamount -= theamount;
		}
		dollars = (int)theamount;  cents = (theamount + 0.001 - dollars) * 100;
		return *this;
	}
	else
	{
		throw "所输入的被除数应当大于0\n";
	}
}

void currency::divide(double x)
{
	try
	{
		try_divide(x);
	}
	catch (char* str)
	{
		cout << str;
	}
}

ostream& operator << (ostream& out, const currency& x)
{
	enum signType
	{
		plus, minus
	};
	if (x.sign == minus)
	{
		out << "-";
	}
	out << "$" << x.dollars << ".";
	if (x.cents < 10)
	{
		out << 0 << x.cents << endl;
	}
	else
	{
		out << x.cents << endl;
	}
	return out;      //使之能够连续输出
}

void operator >> (currency& p, double x)
{
	currency test(x);
	p = test;
}

#endif        // !_CURRENCY_H
#pragma once

