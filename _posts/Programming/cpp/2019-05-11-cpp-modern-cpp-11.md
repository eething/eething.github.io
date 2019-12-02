---
title: "Modern C++11"
date: 2019-05-11 03:30:00 +0900
tags:
  - programming
  - cpp
  - c++11
---

C++11
===

타입 추론, 초기화
---
* [auto, decltype]({% link _docs/Programming/cpp/cpp-auto.md %})
	* // auto func(int a, float b) -> decltype(a + b) { return a + b; }

* Initialization
	* Uniform Initialization
	* initializer_list
	* [Initialization]({% link _docs/Programming/cpp/cpp-initialization.md %})

* POD (Plain Old Data)
	* [Trivial]({% link _docs/Programming/cpp/cpp-trivial.md %})


const expression
---
* static_assert
* constexpr


새로운 타입
---
* nullptr
* long long int
* enum class


Move Semantics
---
* [Value Categories]({% link _docs/Programming/cpp/cpp-value-categories.md %})
* RValue...


Class Object
---
* sizeof(Test::member);
* override, final
* 생성자, 소멸자
	* default, delete, delegate constructor
	* [Constructor]({% link _docs/Programming/cpp/cpp-constructor.md %})


* 형변환 연산자에서의 explicit
	* [safe bool]({% link _docs/Programming/cpp/cpp-safe-bool.md %})

* [union]({% link _docs/Programming/cpp/cpp-union.md %})


template
---
* extern
* using
* 템플릿의 괄호처리
	* Outer<Inner<int> > // C++03
	* Outer<Inner<int>> // C++11
* Variadic template


새로운 기능
---
* Range-based for
* [Lambda]({% link _docs/Programming/cpp/cpp-lambda.md %})
* [Literal]({% link _docs/Programming/cpp/cpp-literal.md %})
* alignof, alignas
{% highlight cpp %}
alignas(alignof(float)) unsigned char c[sizeof(float)]

alignas(4) struct Test
{
	char a;
	int b;
};
{% endhighlight %}

* 멀티태스킹 메모리 모델
	* TLS

* Allow garbage collected implementations
	* 뭔지 잘 모르겠다.. 추후 작성...

* [Attributes]({% link _docs/Programming/cpp/cpp-attributes.md %})


STL
---
* thread
* tuple
* hash table
* regular expression
* smart pointer
	* unique_ptr, weak_ptr
* random?
* wrapper
* type traits
* 함수객체의 리턴형에 대한 방법?
