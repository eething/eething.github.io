---
title: "Modern C++ - SFINAE"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
---

SFINAE
---
* Substitution Failure Is Not An Error
	* 치환(Substitution) 이 실패하면 에러를 내지 않고 다른 적당한 것을 찾는다

{% highlight cpp %}
template<typename T>
typename T::type foo(T t) //int::type foo(int t) -> Substitution Failure
{}

void foo(...) {}

int main()
{
	foo(3); // foo(...)
}
{% endhighlight %}

* 템플릿 인자의 타입을 제한하고 싶은데...
{% highlight cpp %}
template<typename T>
void foo(T t)
{
    static_assert( is_integral<T>::value, "error" );
}
{% endhighlight %}

* 빌드에러 (static_assert) 를 내고 싶은게 아니고 foo(...) 를 호출하게 하고 싶다면?
{% highlight cpp %}
// 리턴타입에서 Substitution Failure
template<typename T>
typename enable_if<is_integral<T>::value>::type foo(T t)
{
    cout << "T" << endl;
}

// 리턴타입이 없는 생성자는?
template<typename T>
void foo(T t,
	typename enable_if<is_integral<T>::value>::type* = nullptr)
{
    cout << "T" << endl;
}

// template 인자로 빼기
template<typename T,
	typename enable_if<is_integral<T>::value>::type* = nullptr>
void foo(T t)
{
    cout << "T" << endl;
}

void foo(...)
{
    cout << "..." << endl;
}

template<typename T>
typename enable_if<is_pointer<T>::value>::type
	print(const T& t)
{
    cout << t << " : " << *t << endl;
}

template<typename T>
typename enable_if<!is_pointer<T>::value>::type
	print(const T& t)
{
    cout << t << endl;
}

int main()
{
    foo(1);
    foo(3.14); // foo(...)

    int n = 10;
    print(n);
    print(&n);
}
{% endhighlight %}

* 함수 찾는 순서 ex) foo( 3.14f );
	1. exactly matching
		* void foo(float)
	2. template
		* template<typename T> void foo(T) {};
	3. promotion
		* void foo(double)
	4. standard conversion
		* void foo(int)
	5. user define conversion
		* struct FLOAT { FLOAT(float) {} };
		* void foo(FLOAT)
	6. variadic argument
		* void foo(...)
