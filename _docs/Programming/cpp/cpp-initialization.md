---
title: "Modern C++ - 초기화"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - initialization
---

Object Initilization
===
* C++98/03 의 문제점
	* 변수 종류에 따라 초기화 방법이 다름
	* 배열 초기화 불가
	* 클래스 멤버
	* 동적 메모리 할당

* Uniform Initialization
	* brace init
	* 객체의 종류에 상관없이 중괄호 {} 를 사용해서 초기화
	* 데이터 손실 방지 prevent narrow

{% highlight cpp %}
class Test
{
	int x[3] { 1, 2, 3 };
};

int main()
{
	Test t;
	int* p = new int[3] { 1, 2, 3 };
	vector<int> v { 1, 2, 3 };

	// direct initialization
	int n2(0);
	int n1    { 0 };
	int a1[3] { 1, 2, 3 };
	Str s1    { 1, 2 };
	Cls c1    { 1, 2 };

	// copy initialization
	int n2    = 0;
	int n2    = { 0 };
	int a2[3] = { 1, 2, 3 };
	Str s2    = { 1, 2 };
	Cls c2    = { 1, 2 };

	int no = 1.2;     // ok
	int ne = { 1.2 }; // error
	char co = 300;    // ok
	char ce = { 300 };// error
}
{% endhighlight %}


초기화 종류
===

Default Initialization
---
* Test t1;
* new Test;


Value Initialization
---
* Test();
* Test{};
* Test t1{};
* new Test();
* new Test{};
* Test::Test : data() {}
* Test::Test : data{} {}


Default VS Value
---
{% highlight cpp %}
int n1;   // default initialization, 쓰레기값
int n2{}; // value initialization,   0
int n3(); // 함수 선언

int* p1 = new int; // default, 쓰레기값
int* p2 = new int(); // value, 0
int* p3 = new int{}; // value, 0

class Test1
{
	int x;
	Test1() = default;
};
class Test2
{
	int x;
	Test2() {}
};

Test1 t1;   // default init, 쓰레기값
Test1 t2{}; // value init, 0
Test2 t3{}; // value init, 쓰레기값
{% endhighlight %}


Direct Initialization
---
* Test t1(1, 2);
* Test t1{ 1, 2 }; // non-class type with a single brace-enclosed initializer
* Test(1, 2) // prvalue???
* Test(t1)
* new Test(1,...) // ???
* Test::Test(int a) : data(a) {}
* [a]() { };
* static_cast<Test>(t1)	// prvalue???


Copy Initialization
---
* Test t1 = t0;
* Test t2 = { t0 }; // (until C++11)
* foo(t0);
* return t0;
* throw t0;
* catch (Test t0)
* Test arr[2] = { t1, t2 };


List Initialization
---
* direct-list-initialization
	* Test t1 { 1, 2 };
	* Test { 1, 2 }
	* new Test { 1, 2 }
	* Class { Test data{ 1, 2 }; };
	* Class::Class() : data{ 1, 2 } {}
* copy-list-initialization
	* Test t2 = { 1, 2 };
	* foo( { 1, 2 } );
	* return { 1, 2 };
	* object[ { 1, 2 } ] // operator[ x ] 의 x 를 초기화
	* object = { 1, 2 }
	* U( { 1, 2 } ) // U::U(initializer_list)
	* Class { Test data = { 1, 2 }; };

* std::initilizer_list
{% highlight cpp %}
#include <initializer_list>

void foo( initializer_list<int> list)
{
	auto p = begin(list); // 상수반복자
	*p = 20; // error

	while( p != end(list) )
		cout << *p++ << endl;

	for (auto n : list)
		cout << n << endl;
}

int main()
{
	initializer_list<int> s = { 1, 2, 3, 4, 5 };
		// cl  int *first, *last
		// g++ int *first, count

	foo( { 1, 2, 3 } );
}
{% endhighlight %}

* 생성자 파라미터로 받기
{% highlight cpp %}
class Test
{
public:
	Test(int a, int b) {}
	explicit Test(double a, double b) {}
	Test(initializer_list<int>) {}
};

int main()
{
    Test t1( 1, 2 );    // Test(int, int)
    Test t2( 1, 2, 3 ); // error

	Test t3( { 1.1, 2.2 } ); // error explicit
		// 변환생성자로 임시객체 생성, 복사생성자

	Test t4{ 1, 2 }; // init -> i, i
	Test t5 = { 1.1, 2.2 }; // init -> d, d, error explicit

	Test t6{1, 2, 3};    // init
	Test t7 = {1, 2, 3}; // init
}
{% endhighlight %}


* STL 사용시 주의사항
	* () 와 {} 가 다르게 동작한다.
{% highlight cpp %}
template<typename T, typename Ax = allocator<T>>
class vector
{
	T* buff;
public:
	vector(size_t sz, T v = T() ) {}
	vector(initializer_list<T> s) {}
};
int main()
{
	vector<int> v = { 1, 2, 3 } ;
	vector<int>(5, 3); // { 3, 3, 3, 3, 3 }
	vector<int>{5, 3}; // { 5, 3 }
}
{% endhighlight %}



Aggregate Initialization
---
* Test t1 = { 1, 2 };
* Test t2 { 1, 2 }; // C++11
* Test t3(1, 2); // C++20
* Test t4 = { .v1 = 1, .v2 { 2 } }; // C++20
* Test t5 { .v1 = 1, .v2 { 2 } }; // C++20

* Aggregate Type : {} 로 초기화 가능한 것
	* 배열
	* 구조체 (class, struct, union)
		* 생성자가 없어야 함
			* C++11 : default, delete 허용
			* C++17 : 
			* C++20 : 
		* default member initializer 없어야 함 (since C++11 until C++14)
		* 상속 없어야 함
			* C++17 : virual, private, protected 상속이 불가, public 은 됨, 또 있나??
		* 가상함수 없어야 함

{% highlight cpp %}
struct Base {};
struct Test : Base
{
    int x, y;

    Test() = default;
};

int main()
{
    Test t1;
    Test t2 { Base(), 1, 2 };
    Test t3 = { Base(), 1, 2 };
}
{% endhighlight %}

* Designated Initializers
	* C 에만 있고 C++ 에서는 없어졌으나 C++20 에서 부활하는 듯
	* 변수 순서 맞춰줘야 함, 중간에 빼먹는 것은 가능
	* Narrowing conversions 불가

{% highlight cpp %}
struct A
{
	int v1;
	int v2;
	int v3 = 3;
	int v4;

	/* 아직 제대로 설명이 된 곳을 찾기 힘들고 테스트 환경도 없음-_-
	union
	{
		int u_i;
		doubld u_d;
	}
	*/
};

A a { .v2 = 2, .v1 = 1 }; // error, 순서 맞춰줘야 함
A b { .v1 = 1, .v4 = 4 }; // ok, v2 = 0, v3 = 3;

union u
{
	int a;
	const char* b;
};

u f = { .b = "asdf" };
u g = { .a = 1, .b = "asdf" }; // error, union은 하나만...
{% endhighlight %}


Reference Initialization
---
* Test &t1 = t0;
* Test &t2(t0);
* Test &t3 = { 1, 2 };
* Test &t4 { 1, 2 } ;

* Test &&t1 = t0 ;
* Test &&t2 ( t0 ) ;
* Test &&t3 = { 1, 2 };
* Test &&t4 { 1, 2 } ;

* void foo( Test & arg ); or void foo( Test && arg );
	* foo( t0 )
	* foo( { 1, 2 } )

* Test& foo() or Test&& foo()
	* return t0;

* Test& data; or Test&& data;
	* Class::Class(...) : data(t) {}


초기화 리스트, default member initializer
---
* 초기화 리스트 먼저 체크
* 그다음 default member initializer

{% highlight cpp %}
int c = 0;
class Test
{
public:
	int data = ++c;
	int x[3];

	Tes t() {}
	Test(int n) : data(n) {}
	Test(double) {}
};

int main()
{
	Test t1;      // data = 1
	Test t2(10);  // data = 10
	Test t3(1.2); // data = 2
}
{% endhighlight %}


explicit
---
* 변환생성자 불가
* copy initialization 불가

{% highlight cpp %}
class Test
{
	int x, y;
public:
	explicit Test() {}
	explicit Test(int a) : x(a) {}
	explicit Test(int a, int b) : x(a), y(b) {}
};
int main()
{
	Test t1(1);
	Test t2 = 2; // error, explicit
	Test t3(1, 2);
	Test t4 = (1, 2); // error
	Test t5{1, 2};
	Test t6 = {1, 2}; // error, explicit

	Test t7;
	Test t8{};
	Test t9 = {}; // error, explicit
}
{% endhighlight %}
