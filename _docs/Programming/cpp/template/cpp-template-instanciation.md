---
title: "C++ 템플릿 인스턴스화"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - template
---

template instantiation
-----

template instantiation
----

template instantiation
---

template instantiation
--

* explicit instantiation (명시적 인스턴스화)
	* 정의를 cpp 에서 함
	* 정의를 숨길 수 있음
	* 원하지 않는 타입 인스턴스화 방지
	{% highlight cpp %}
// test.h
template<typename T>
class Test
{
public:
	foo();
};

template<typename T> T square(T a);
{% endhighlight %}
	* test.cpp
	{% highlight cpp %}
template <typename T>
void Test<T>::foo()
{
	cout << "foo" << endl;
}
template <typename T>
void Test<T>::bar()
{
	cout << "bar" << endl;
}

//template class Test<int>; // foo, bar 모두 인스턴스화
//template void Test<int>::foo();
template void Test<int>::bar();


template<typename T> T square(T a) { return a * a; }
//template int square<int>(int); // 정석
//template int square<>(int);
template int square(int);
{% endhighlight %}
	* main.cpp
{% highlight cpp %}
#include "test.h"
int main()
{
	Test<int> t1;
	t1.foo();
	t1.bar();

	cout << square( 3 ) << endl;
}
{% endhighlight %}

* implicit instantiation (암시적 인스턴스화)
	* 필요하면(필요한것만) 컴파일러가 알아서 만들어 줌
{% highlight cpp %}
int main()
{
	square(3);
	square<int>(3)

	Test<int> t1;
	t1.foo(); // foo 만 인스턴스화 함(lazy)
}
{% endhighlight %}



* lazy instantiation
	* 클래스 템플릿은 사용하지 않은 멤버함수는 인스턴스화 하지 않음
{% highlight cpp %}
template<typename T> class Test
{
    C1 c;
    static C2 c2;
}
template<typename T> C2 Test<T>::c2; // 인스턴스화 되지 않음

template<typename T> void foo(T t) { *t = 10; }

int main()
{
    cout << "main" << endl;
    Test<int> t; // C1()

    if (false) // 실행시간 조건문
        foo(0); // error
    if constexpr (false) // static if, 컴파일시간, C++17
        foo(0); // 인스턴스화 안함

}   // ~C1()
{% endhighlight %}
