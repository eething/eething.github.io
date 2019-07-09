---
title: "Modern C++14"
date: 2019-05-14 03:30:00 +0900
tags:
  - programming
  - cpp
---

C++14
===


����Ÿ�� �߷�
---
* auto
	* C++11 ������ lambda ������ ��밡��
	* C++14 ������ ��� �Լ����� ��� ����
	* ���� �� ���� ������ ���Ǹ� �� ���Ŀ� ȣ�� ����
	* Recursion �ÿ��� �ּ��� �ϳ��� return �� ���Ŀ� ���ȣ�� ����
{% highlight cpp %}
auto fact(int i)
{
	if (i==1)
		return i;
	return fact(i-1) * i;
}

auto fact_error(int i)
{
	if (i!=1)
		return fact(i-1) * i;
	return i;	
}
{% endhighlight %}

* decltype(auto)
	* auto �� ������ decltype ����� ���� ���� �� ���
	* C++11 �� trailing return type �� ���� �����ϰ� ����
{% highlight cpp %}
int x = 0;
auto f1()					{ return (x); }
auto f2() -> decltype(x)	{ return x; }
auto f3() -> decltype((x))	{ return x; }
decltype(auto) f4()			{ return x; }
decltype(auto) f5()			{ return (x); }

int main()
{
	f1(); // int  f1();
	f2(); // auto f2() -> int
	f3(); // auto f3() -> int&
	f4(); // int  f4();
	f5(); // int& f5();
}
{% endhighlight %}

constexpr function ������ ���� ��ȭ
---
* ���� - �Ʒ����� ���� �� ��� ����
	 * static
	 * thread_local
	 * �ʱ�ȭ �ȵ� ����
* if, switch, ������ ��� ����
* goto ���Ұ�

* C++11 ������ (non-static)����Լ��� constexpr �� �ᵵ const �� ��޵�
* C++14 ������ (non-static)����Լ��� constexpr ������ const �� �ƴ� �� ����


