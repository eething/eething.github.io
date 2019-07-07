---
title: "Modern C++ -생성자"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
---

생성자 초기화 순서
---
* 초기화리스트의 순서가 아닌, 변수가 선언된 순서로 초기화됨





C++11
---
* default, delete
{% highlight cpp %}	
class Test
{
	Test() = default;
	Test(const Test& rhs) = delete;
};
{% endhighlight %}

* 위임생성자
{% highlight cpp %}
class Test
{
	Test(int a) : n(a) {}
	Test() : Test(10) {}
}
{% endhighlight %}
