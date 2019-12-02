---
title: "C++ Thread - C++11 Thread 관리"
date: 2019-12-02 15:30:00 +0900
tags:
  - programming
  - cpp
  - thread
---

Sleep
---
{% highlight cpp %}
#include <iostream>
#include <thread> // include <chrono>

foo()
{
	for (int i = 0; i < 10; ++i)
	{
		std::cout << i << std::endl;

		std::this_thread::sleep_for(std::chrono::duration<int>(1));

		// C++14 User Defined Literals
		// std::this_thread::sleep_for(1s);
	}
}

int main()
{
	thread t(foo)
	t.join();
	return 0;
}
{% endhighlight %}
