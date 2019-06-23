---
title: "Modern C++ - Lambda"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - lambda
---

Lambda Expression & Callable Object
===

inline
---
* inline 제대로 되었는지 확인 방법
	* g++ -S
		* inline.s
	* cl /FAs /Ob1
		* inline.asm

* 함수 포인터
	* inline 함수를 함수포인터 변수에 넣으려면
	* 포인터 변수가 가리킬 메모리 상에 함수가 있어야 함

{% highlight cpp %}
       int Add1(int a, int b) { return a + b; }
inline int Add2(int a, int b) { return a + b; }

int main()
{
    int n = Add2(1, 2); // 치환

    int(*f)(int, int);
    f = &Add2;
    int r = f(1, 2); // 호출
}
{% endhighlight %}


Function Object
---

* 일반함수
	* 자신만의 타입이 없음
	* signature 가 동일하면 같은타입
	* 어떤 함수가 올지 알 수 없기 때문에 inline 치환 불가
* 함수객체
	* 자기자신 타입이 하나이기 때문에 inline 사용 가능

{% highlight cpp %}
inline bool cmp1(int a, int b) { return a > b; }
inline bool cmp2(int a, int b) { return a < b; }

void sort1(int* x, int n, bool (*cmp)(int, int) )
{
	// 함수포인터이기 때문에 inline 사용 불가
	if ( cmp(x[i], x[j]) )
	{
		...
	}
}

struct Less
{
    inline bool operator()(int a, int b) const { return a < b; }
};
struct Greater
{
    inline bool operator()(int a, int b) const { return a > b; }
};

template<typename F>
void sort2(int* x, int n, F cmp)
{
	// 함수객체는 inline 사용 가능
	if ( cmp(x[i], x[j]) )
	{
		...
	}
}

int main()
{
	int arr[5] = { 1, 5, 4, 2, 3 };

	sort1(arr, arr + 5, cmp1);		// sort(int*, int*, bool(*)(int, int))
	sort1(arr, arr + 5, cmp2);		// sort(int*, int*, bool(*)(int, int))

	sort2(arr, arr + 5, Less());	// sort(int*, int*, Less)
	sort2(arr, arr + 5, Greater());	// sort(int*, int*, Greater)
}
{% endhighlight %}




Lambda expression & Closure Object
---

* [] () -> {}
{% highlight cpp %}
auto f1 = [](int a, int b) { return a + b; };
auto f2 = [](int a, int b) -> int { return a + b; };
auto f3 = [](int a, float b) -> float {
				if (a==1)
					return a;
				else
					return b;
            };
{% endhighlight %}

* 내부적으로는 객체를 생성한다.
{% highlight cpp %}
sort(arr, arr + 5, [](int a, int b) { return a > b; } );

/*
class ClosureType
{
public:
	ClosureType(const ClosureType&) = default;
	ClosureType(ClosureType&&) = default;
	ClosureType& operator=(const ClosureType&) = delete;
	~ClosureType() = default;

	bool operator()(int a, int b) const
	{
		return a > b;
	}
	operator bool(*)(int, int)
	{
		return &method;
	}
	static bool method(int a, int b)
	{
		return a > b;
	}
};

sort(arr, arr + 5, ClosureType());
*/
{% endhighlight %}

* 모든 Lambda Expression 은 서로 다른 타입
{% highlight cpp %}
int main()
{
	auto f1 = [](int, int) { return; };
	auto f2 = [](int, int) { return; };

	cout << typeid(f1).name() << endl;
	cout << typeid(f2).name() << endl;
}
{% endhighlight %}

* Lambda 의 용도
	* 함수 인자 function argument
	* 함수 (auto 변수)
	* 리턴 값 high-order function

{% highlight cpp %}
auto f1 = [](int a, int b) { return a + b; };

int(*f2)(int, int)
   = [](int a, int b) { return a + b; };
f2 = [](int a, int b) { return a - b; };

function<int(int, int)> f3 =
    [](int a, int b) { return a + b; };

f1(1, 2); // inline ok
f2(1, 2); // inline X
f3(1, 2); // inline X

//void foo( int(*f)(int, int) )       // inline X
//void foo(function<int(int, int)> f) // inline X
template<typename F> void foo(F f)    // inline ok
{
    f(1, 2);
}
int main()
{
    foo( [](int a, int b) { return a + b; } );
    foo( [](int a, int b) { return a - b; } );
}
{% endhighlight %}


Capture Variable
---
	* [=]() {} // 복사
	* [&]() {} // 참조
	* [=, &a]() {}
	* [&, =a]() {}

{% highlight cpp %}
int a = 0, b = 0;

auto f0 = [](int a, int b) { return a + b; }
auto f1 = [a, b]()         { a = 1; return b; } ); // error
auto f2 = [a, b]() mutable { a = 1; return b; } ); // 복사본을 수정
auto f3 = [&a, b]()        { a = 1; return b; } );

cout << sizseof(f0) << endl; // 1
cout << sizseof(f1) << endl; // 8

/* 내부구현
class ClosureType
{
    int &v1;
    int v2;
public:
    ClosureType(int a, int b) : v1(a), v2(b) {}

    //int operator()() { ... } // mutable, f2
    int operator()() const
    {
        v1 = 1;
        return v2;
    }
};
*/
{% endhighlight %}


* 기본값
	* [a = x, b& = y, c = c, d = 10]() {}
	* 어라 난 이거 안되는데??? C++14 부터 되는 듯? -> 확인필요

* 멤버접근은 this 를 capture
{% highlight cpp %}
class Test
{
	int data = 0;
	void foo()
	{
		//Test* const this;
		auto f1 = [this]() { data = 10; };
		f1();

		// 복사본 캡쳐
		auto f2 = [*this]() { data = 20; }; // error
		auto f3 = [*this]() mutable { data = 20; };
    }
};
{% endhighlight %}

* 가변인자 capture
{% highlight cpp %}
template<typename ... Args> void foo(Args ... args)
{
    int x = 0;
    auto f = [=]()       { auto t = make_tuple(args...); }
    auto f = [args...]() { auto t = make_tuple(args...); }
}
{% endhighlight %}

* etc
{% highlight cpp %}
unique_ptr<int> p(new int);
auto f1 = [p]() {}; // error
auto f2 = [p = move(p)]() {}; // error
{% endhighlight %}


Conversion
---

* Capture 가 있는 Lambda 를 함수포인터 변환시 에러
{% highlight cpp %}
int(*f)(int, int) = [&v](int a, int b)
					{
						v = 10;
						return a + b;
					};
/* 내부구현
ClosureType
{
	int v;
public:
	ClosureType(int i) : v(i) {}
	operator()(int a, int b) const
	{
		v = 10;
		return a + b;
	}

	static int method(int a, int b)
	{
		v = 10; // ERROR, static 에서 member 접근
		return a + b;
	}

	typedef int(*F)(int, int);
	operator F()
	{
		return &ClosureType::method;
	}
};
*/
{% endhighlight %}
