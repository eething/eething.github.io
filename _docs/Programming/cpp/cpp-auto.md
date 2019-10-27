---
title: "Modern C++ - auto, decltype"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - auto
  - decltype
---

auto
===

타입추론방법
---
* auto
	* reference, const, volatile 제거
* auto&
	* reference 제거
	* const, volatile 유지
* decltype
	* 변수이름 : 변수와 같은 타입 유지
	* 표현식 : 표현식이 lvalue 면 참조

{% highlight cpp %}
void test_auto()
{
	int n = 10;
	int& r = n;
	const int c = n;
	const int& cr = c;

	auto a1 = n;	// int
	auto a2 = r;	// int
	auto a3 = c;	// int
	auto a4 = cr;	// int

	auto& a5 = n;	// int&
	auto& a6 = r;	// int&
	auto& a7 = c;	// const int&
	auto& a8 = cr;	// const int&

	const char* s1 = "hello";
	auto as1 = s1; // const char*

	const char* const s2 = "world";
	auto as2 = s2; // const char*
}

void test_decltype()
{
	int n = 0;
	int* p = &n;
	int x[3] = { 1, 2, 3 };

	// 변수이름 : 같은 타입
	decltype(n)		d1; // int
	decltype(p)		d2; // int*

	// 표현식 : lvalue 이면 참조
	decltype(*p)	d3; // int&, lvalue
	decltype((n))	d4; // int&, lvalue
	decltype(n+n)	d5; // int,  rvalue
	decltype(++n)	d6; // int&, lvalue
	decltype(n++)	d7; // int,  rvalue

	decltype(x[0])	d8; // int&, lvalue
	auto a8 = x[0];	    // int
}

int x = 10;
int& foo(int a, int b)
{
	return x;
}

int main()
{
	auto ret1 = foo(1, 2); // int

    // unevaluated expression
	decltype(foo(1,2)) ret2 = foo(1, 2); // int&

    // C++14
	decltype(auto) ret2 = foo(1, 2); // int&
}
{% endhighlight %}
