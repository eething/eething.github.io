---
title: "Modern C++ - union"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - union
---

* C++03 에서는 union 안에 [NonTrivial]()한 생성자, 소멸자를 가지는 object 를 넣을 수 없었음
* C++11 부터는 가능함, 대신에 컴파일러가 자동생성해 주지 않으므로 직접 만들어 줘야 함

{% highlight cpp %}
#include <new>
struct Point
{
	Point() {}
	Point(int x, int y): m_x(x), m_y(y) {} 
	int m_x, m_y;
};

union U
{
	int z;
	double w;
	Point p; // Invalid in C++03; valid in C++11.
    
	U() {} // Due to the Point member, a constructor definition is now needed.
	U(const Point& pt) : p(pt) {} // Construct Point object using initializer list.
	U& operator=(const Point& pt) { new (&p) Point(pt); return *this; } // Assign Point object using placement 'new'.
};
{% endhighlight  %}
