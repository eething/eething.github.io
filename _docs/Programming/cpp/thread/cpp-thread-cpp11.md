---
title: "C++ Thread - C++11 Thread"
date: 2019-12-02 15:30:00 +0900
tags:
  - programming
  - cpp
  - thread
---

Thread 생성 방법
---
* 함수 포인터
{% highlight cpp %}
#include <thread>

void foo(int a, int b) {}

void bar(float f) {}

int main()
{
	thread t1(foo, 1, 2);
	thread t2(bar, 3.14f);

	t1.join();
	t2.join();

	return 0;
}
{% endhighlight %}
* 람다
{% highlight cpp %}
#include <thread>

int main()
{
	thread t1([](int a, int b)
		{
		}, 1, 2);

	t1.join();

	return 0;
}
{% endhighlight %}
* 함수 객체
{% highlight cpp %}
#include <thread>

class Foo
{
public:
	Foo(int a, int b) {}

	void operator()() const	{}
};

class Bar
{
public:
	Bar(int a, int b) {}

	void bar() const {}
};

int main()
{
	Foo f(1, 10);
	thread t1(f);

	thread t2( Foo(2, 20) );

	thread t3{ Foo(3, 30) };

	Bar b(4, 40);
	thread t4 { &Bar::bar, &b }

	t1.join();
	t2.join();
	t3.join();
	t4.join();

	return 0;
}
{% endhighlight %}
