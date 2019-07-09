---
title: "Modern C++14"
date: 2019-05-14 03:30:00 +0900
tags:
  - programming
  - cpp
---

C++14
===


리턴타입 추론
---
* auto
	* C++11 에서는 lambda 에서만 사용가능
	* C++14 에서는 모든 함수에서 사용 가능
	* 선언만 할 수도 있지만 정의를 한 이후에 호출 가능
	* Recursion 시에는 최소한 하나의 return 문 이후에 재귀호출 가능
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
	* auto 를 쓰지만 decltype 방식을 쓰고 싶을 때 사용
	* C++11 의 trailing return type 를 생략 가능하게 해줌
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

constexpr function 에서의 제약 완화
---
* 변수 - 아래사항 빼고 다 사용 가능
	 * static
	 * thread_local
	 * 초기화 안된 변수
* if, switch, 루프문 사용 가능
* goto 사용불가

* C++11 에서는 (non-static)멤버함수는 constexpr 을 써도 const 로 취급됨
* C++14 에서는 (non-static)멤버함수가 constexpr 이지만 const 가 아닐 수 있음


