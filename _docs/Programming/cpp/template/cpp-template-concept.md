---
title: "C++ 템플릿 - Concept"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - template
---

Concept
===
* C++20
* 템플릿 파라미터에 제약조건을 추가
* g++ concept.cpp -fconcepts

{% highlight cpp %}
struct Point
{
	int x, y;
	bool operator<(const Point& p) { return true; }
};

// concept 생성
template<typename T>
concept bool LessThanComparable = requires(T a, T b)
{
	{ a < b } -> bool;
};

// template 뒤에 붙이기
template<typename T> requires LessThanComparable<T>
T Min1(T a, T b)
{
	return a < b ? a : b;
}

// 함수선언 뒤에 붙이기
template<typename T>
T Min2(T a, T b) requires LessThanComparable<T>
{
	return a < b ? a : b;
}

// template 없이...
LessThanCompareable Min3(LessThanComparable a, LessThanComparable b)
{
	return a < b ? a : b
}

int main()
{
	Point p1, p2;
	Min(p1, p2);
}
{% endhighlight %}
