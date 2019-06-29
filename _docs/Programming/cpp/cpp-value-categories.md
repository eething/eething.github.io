---
title: "Modern C++ - Value Categories"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
---

Value Categories
---

![Value Categories](https://docs.microsoft.com/ko-kr/cpp/cpp/media/value_categories.png)

* glvalue (generalized lvalue)
	* identity : polymorphic, name
	* lvalue
		* identity
		* copy
		* lvalue 리턴
	* xvalue (eXpiring value)
		* identity
		* move
		* rvalue 리턴

* rvalue
	* move
	* xvalue (eXpiring value)
		* identity
		* move
		* rvalue 리턴
	* prvalue (pure rvalue)
		* no identity : non-polymorphic, no name
		* move
		* 값 리턴

* lvalue
	* ++a, a += b, *p, a[n]
	* "Hello, World!"
* xvalue
	* std::move(x), a[n], Test().m
* prvalue
	* a++, a + b, a < b
	* static_cast<int>(a)
	* this
	* lambda expression
	* requires expression

{% highlight cpp %}
int x = 0;
int   f1() { return x; }
int&  f2() { return x; }
int&& f3() { return move(x); }

Derived d;
Base   f4() { return d; }
Base&  f5() { return d; }
Base&& f6() { return move(d); }

int main()
{
	f1() = 1; // error
	f3() = 2; // ok
	f3() = 3; // error

	Base b1 = f4(); // move, 임시객체
	Base b2 = f5(); // copy
	Base b3 = f6(); // move

	f4().foo(); // Base::foo, 임시객체
	f5().foo(); // Derived::foo
	f6().foo(); // Derived::foo
}
{% endhighlight %}
