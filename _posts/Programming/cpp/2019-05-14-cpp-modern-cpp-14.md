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
