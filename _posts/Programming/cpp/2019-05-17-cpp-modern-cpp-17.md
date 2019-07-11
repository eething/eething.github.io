---
title: "Modern C++17"
date: 2019-05-17 03:30:00 +0900
tags:
  - programming
  - cpp
---

C++17
===

제거됨
---
* 삼중자
* std::auto_ptr
* std::random_shuffle
* 오래된 function adaptors // <functional>
* register // 전에 이미 deprecated 되었었음


새로운 문법
---
* static_assert

* template template parameter 에서 class 대신 typename 사용가능
{% highlight cpp %}
template<typename T> class Test {};

template<typename U, template<typename> /*class*/ typename C = Test>
class TTP
{
	C<U> data;
};
{% endhighlight %}120

* auto 타입추론 :braced-init-list
{% highlight cpp %}
auto x1 = { 1 };	// std::initializer_list<int>
auto x2 = { 1, 2 };	// std::initializer_list<int>
auto x3 = { 1, 2.0 };	// error: cannot deduce element type
auto x5{ 1 };		// int
auto x6{ 1, 2 };	// error: not a single element
{% endhighlight %}

* nested namespace
{% highlight cpp %}
// 기존방식
namespace A { namespace B { namespace C { ... } } }

// C++17
namespace A::B::C { ... }
{% endhighlight %}

Attribute
	* namespace 와 enum 에 attribute 사용 가능
	* [[fallthrough]]
	* [[maybe_unused]]
	* [[nodiscard]]

* 문자 리터럴 추가
	* u8 : UTF-8 character literals
	* C++11 부터 있던 것은 UTF-8 string literals
	* 1바이트, ASCII만 저장가능

* Hexadecimal floating-point literals
	* 0xDEADp12
	* 0X0p-12


모든 non-type 템플릿 인자에 대한 상수 평가[20]

* Fold expressions

* if constexpr
* Structured binding
	* auto [a, b] = t0;

* switch문에서의 if와 switch 초기화

* prvalue 으로 Copy Initilization 이나 Direct Initialization 을 하면
	* prvalue의 복사 생성자나 이동 생성자가 호출되지 않음
	* copy elision 참고

* over-aligned 메모리 할당
	* [하.. 뭔소린지-_-](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0035r4.html)


* CTAD, Class Template Argumaent Deduction
	* 생성자 추론 가이드

* inline 변수
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
	* VisualStudio 2017 /std:c++latest 에서는 아직 199711
	* gcc 는 컴파일러 옵션에 따라 199711, 201103, 201300, 201402 등등...

* Exception 도 함수 타입(시그니쳐?) 에 포함됨
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


라이브러리
---
* std::string_view
	* read-only
	* 소유권 없는 레퍼런스

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
	* std::uncaught_exception 를 대체

* try_emplace, insert_or_assign
	* std::map std::unordered_map

* std::size, std::empty, std::data

* contiguous iterators
	* random access iterators 의 재정의?
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

컴파일러 지원
---
* gcc 7
* Clang 5
* Visual Studio 2017 15.7 (MSVC 19.14) // 100% 는 아닌듯?
