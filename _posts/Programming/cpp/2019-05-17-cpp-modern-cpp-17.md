---
title: "Modern C++17"
date: 2019-05-17 03:30:00 +0900
tags:
  - programming
  - cpp
---

C++17
===

���ŵ�
---
* ������
* std::auto_ptr
* std::random_shuffle
* ������ function adaptors // <functional>
* register // ���� �̹� deprecated �Ǿ�����


���ο� ����
---
* static_assert

* template template parameter ���� class ��� typename ��밡��
{% highlight cpp %}
template<typename T> class Test {};

template<typename U, template<typename> /*class*/ typename C = Test>
class TTP
{
	C<U> data;
};
{% endhighlight %}120

* auto Ÿ���߷� :braced-init-list
{% highlight cpp %}
auto x1 = { 1 };	// std::initializer_list<int>
auto x2 = { 1, 2 };	// std::initializer_list<int>
auto x3 = { 1, 2.0 };	// error: cannot deduce element type
auto x5{ 1 };		// int
auto x6{ 1, 2 };	// error: not a single element
{% endhighlight %}

* nested namespace
{% highlight cpp %}
// �������
namespace A { namespace B { namespace C { ... } } }

// C++17
namespace A::B::C { ... }
{% endhighlight %}

Attribute
	* namespace �� enum �� attribute ��� ����
	* [[fallthrough]]
	* [[maybe_unused]]
	* [[nodiscard]]

* ���� ���ͷ� �߰�
	* u8 : UTF-8 character literals
	* C++11 ���� �ִ� ���� UTF-8 string literals
	* 1����Ʈ, ASCII�� ���尡��

* Hexadecimal floating-point literals
	* 0xDEADp12
	* 0X0p-12


��� non-type ���ø� ���ڿ� ���� ��� ��[20]

* Fold expressions

* if constexpr
* Structured binding
	* auto [a, b] = t0;

* switch�������� if�� switch �ʱ�ȭ

* prvalue ���� Copy Initilization �̳� Direct Initialization �� �ϸ�
	* prvalue�� ���� �����ڳ� �̵� �����ڰ� ȣ����� ����
	* copy elision ����

* over-aligned �޸� �Ҵ�
	* [��.. ���Ҹ���-_-](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0035r4.html)


* CTAD, Class Template Argumaent Deduction
	* ������ �߷� ���̵�

* inline ����
{% highlight cpp %}
// Test.h
class Test
{
	static const int a = 1;
    inline static int b = 2;
};
//extern Test t;
inline Test t;

// Test.cpp
//int Test::b = 2;
//Test t;
{% endhighlight %}


* __has_include
{% highlight cpp %}
#include "stdafx.h"
#include <iostream>

int main()
{
#if __has_include(<iostream>) // true
	cout << "<iostream>" << endl;
#endif
#if __has_include("iostream") // true
	cout << R"("iostream")" << endl;
#endif
#if __has_include(<stdafx.h>) // false
	cout << "<stdafx.h>" << endl;
#endif
#if __has_include("stdafx.h") // true
	cout << R"("stdafx.h")" << endl;
#endif
}
{% endhighlight %}


* __cplusplus => 201703L
	* VisualStudio 2017 /std:c++latest ������ ���� 199711
	* gcc �� �����Ϸ� �ɼǿ� ���� 199711, 201103, 201300, 201402 ���...

* Exception �� �Լ� Ÿ��(�ñ״���?) �� ���Ե�
{% highlight cpp %}
void foo_noex() noexcept//(true)
{}

//void foo_ex() throw(int)
void foo_ex() noexcept(false)
{}

void foo()
{
	void (*p1)() noexcept;//(true);
	p1 = foo_noex;
	p1 = foo_ex; // error, except -> noexcept

	void (*p2)() noexcept(false);
	p2 = foo_noex;
	p2 = foo_ex;

	void (**pp1)() noexcept = &p1;
	void (**pp2)() noexcept = &p2; // error, except -> noexcept
}
{% endhighlight %}


���̺귯��
---
* std::string_view
	* read-only
	* ������ ���� ���۷���

* std::optional
{% highlight cpp %}
#include <optional>

std::optional<int> foo( std::optional<float> input = nullopt )
{
	if( input )
		return static_cast<int>(*input);

	return {}; // nullopt;
}

int main()
{
	auto x = foo(3.14f);
	if( x )
		cout << *x << endl;;
}
{% endhighlight %}

* std::any

* std::uncaught_exceptions
	* std::uncaught_exception �� ��ü

* try_emplace, insert_or_assign
	* std::map std::unordered_map

* std::size, std::empty, std::data

* contiguous iterators
	* random access iterators �� ������?
	* *(iter + 3) == *(&(*iter) + 3)

* boost::filesystem

* Parallel versions of STL algorithms

* Additional mathematical special functions, including elliptic integrals and Bessel functions[44]

* std::variant
	* a tagged union container

* std::byte

* std::conjunction, std::disjunction, std::negation
{% highlight cpp %}
#include <type_traits>

template<typename A, typename B, typename C>
void foo(A a, B b, C c)
{
	if( conjunction<is_same<A, B>, negation<is_same<B, C>>>::value )
		cout << "conjunction" << endl;

	if( disjunction<is_same<A, B>, is_same<B, C>>::value )
		cout << "disjunction" << endl;
}

int main()
{
	foo( 1, 2, "str");
}

{% endhighlight %}

�����Ϸ� ����
---
* gcc 7
* Clang 5
* Visual Studio 2017 15.7 (MSVC 19.14) // 100% �� �ƴѵ�?
