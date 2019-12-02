---
title: "C++ Thread - C++11 Thread 관리"
date: 2019-12-02 15:30:00 +0900
tags:
  - programming
  - cpp
  - thread
---

Mutex
---
* lock, unlock
{% highlight cpp %}
#include <iostream>
#include <mutex> // include <thread>

int cnt = 0;
std::mutex m;

void foo(int id)
{
	for (int i = 0; i < 10; ++i)
	{
		m.lock();
		std::cout << id << " : " << cnt++ << std::endl;
		m.unlock();
		std::this_thread::sleep_for(1s);
	}
}

int main()
{
	thread t1(foo, 1);
	thread t2(foo, 2);

	t1.join();
	t2.join();
}
{% endhighlight %}

* try_lock


* std::lock_guard
	* RAII
{% highlight cpp %}
void foo(int id)
{
	for (int i = 0; i < 10; ++i)
	{
		{
			std::lock_guard<std::mutex> lock(m);
			std::cout << id << " : " << cnt++ << std::endl;
		}
		std::this_thread::sleep_for(1s);
	}
}
{% endhighlight %}

* std::recursive_mutex
	* lock 을 여러번 걸고 싶을 때 사용

{% highlight cpp %}
#include <iostream>
#include <mutex> // include <thread>

int cnt = 0;
std::recursive_mutex m;

void bar()
{
	std::lock_guard<std::recursive_mutex> lock(m);
}

void foo(int id)
{
	std::lock_guard<std::recursive_mutex> lock(m);
	bar();
}

int main()
{
	thread t1(foo, 1);
	thread t2(foo, 2);

	t1.join();
	t2.join();
}
{% endhighlight %}

* std::scoped_lock // C++17
* std::unique_lock
* std::timed_mutex
* std::recursive_timed_mutex
* std::shared_mutex // C++17




