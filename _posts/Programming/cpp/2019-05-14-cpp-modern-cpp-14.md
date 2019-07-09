---
title: "Modern C++14"
date: 2019-05-14 03:30:00 +0900
tags:
  - programming
  - cpp
  - c++14
---

C++14
===


리턴타입 추론
---
* auto
	* C++11 에서는 lambda 에서만 사용가능
	* C++14 에서는 모든 함수에서 사용 가능
	* 선언만 할 수도 있지만 정의를 한 이후에 호출 가능
	* Recursion 시에는 최소한 하나의 return 문 이후에 재귀호출 가능
{% highlight cpp %}
auto fact(int i)
{
	if (i==1)
		return i;
	return fact(i-1) * i;
}

auto fact_error(int i)
{
	if (i!=1)
		return fact(i-1) * i;
	return i;	
}
{% endhighlight %}

* decltype(auto)
	* [auto 와 decltype 의 타입추론방식은 다르다.]({% link _docs/Programming/cpp/cpp-auto.md %})
	* auto 를 쓰지만 decltype 방식을 쓰고 싶을 때 사용
	* C++11 의 trailing return type 를 생략 가능하게 해줌
{% highlight cpp %}
int x = 0;
auto f1()					{ return (x); }
auto f2() -> decltype(x)	{ return x; }
auto f3() -> decltype((x))	{ return x; }
decltype(auto) f4()			{ return x; }
decltype(auto) f5()			{ return (x); }

int main()
{
	f1(); // int  f1();
	f2(); // auto f2() -> int
	f3(); // auto f3() -> int&
	f4(); // int  f4();
	f5(); // int& f5();
}
{% endhighlight %}

constexpr function 에서의 제약 완화
---
* 변수 - 아래사항 빼고 다 사용 가능
	 * static
	 * thread_local
	 * 초기화 안된 변수
* if, switch, 루프문 사용 가능
* goto 사용불가
* C++11 에서는 (non-static)멤버함수는 constexpr 을 써도 const 로 취급됨
* C++14 에서는 (non-static)멤버함수가 constexpr 이지만 const 가 아닐 수 있음


Variable template
---

{% highlight cpp %}
template<typename T>
constexpr T pi = T( 3.141592653589793238462643383 );

template<>
constexpr int pi<int> = 0;

template<>
constexpr const char* pi<const char*> = "pi";

int main()
{
	cout.precision( 100 );
	cout << pi<int> << endl;			// 0
	cout << pi<const char*> << endl;	// pi
	cout << pi<float> << endl;			// 3.1415927410125732421875
	cout << pi<double> << endl;			// 3.141592653589793115997963468544185161590576171875
}
{% endhighlight %}


 Aggregate
---
* C++11 에서는 클래스의 member initializer 가 있는 경우엔 Aggregate 가 아니었음
* C++14 에서는 Aggregate, 값을 넣지 않으면 member initializer 로 초기화


Binary literals
---
* 0b 0B 


Digit separators
---
* 단지 사람이 보기 편하기 위함이므로 아무렇게나 막(?) 찍어도 됨
	* 1'0000'000'00'0
	* 1.23'456'7
	* 0b0101'0010


Generic lambdas
---
{% highlight cpp %}
auto lambda = [] (auto x, auto y) { return x + y; };
{% endhighlight %}


Lambda capture expressions
---
* C++11 에서는 캡쳐 방식이 값-복사 이나 참조 방식만 있었음
* C++14 에서는 임의의 수식을 받을 수 있음
	* 값-이동 방식도 가능해져서, 외부에 이름이 없는 것도 가능

{% highlight cpp %}
auto l1 = [value = 1] { return value; };

std::unique_ptr<int> ptr(new int(10));
auto l2 = [value = std::move(ptr)] { return *value; };
{% endhighlight %}


Attributes
---
* [Attributes]({% link _docs/Programming/cpp/cpp-attributes.md %})
* ```[[deprecated]]```


STL
---
* Shared mutexes and locking
	* 아몰랑

* Heterogeneous lookup in associative containers
	* map, set 등의 컨테이너에서 검색을 하고 싶을 때
	* 혹은 std::less, std::greater 를 사용할 때,
	* 정확히 같은 타입이 아니어도 찾을 수 있음
	* 예를들면 string 과 const char*
	* operator< 으로 비교할 수 있으면 됨	
	* key 가 객체인 경우 검색을 위한 객체를 생성하지 않아도 됨

* Standard user-defined literals
{% highlight cpp %}
auto str	= "hello"s;	// string

auto time	= 1h + 30s;	// chrono::seconds
						// "h", "min", "s", "ms", "us", "ns"

auto cf		= 1if;		// complex<float>
auto cd		= 1i;		// complex<double>
auto cld	= 1il;		// complex<long double>
{% endhighlight %}

* Tuple addressing via type
{% highlight cpp %}
tuple<int, int, double> tp(1, 2, 3.14);
double a = get<2>(tp);		// 3.14
double b = get<double>(tp);	// 3.14
int i = get<int>(tp);		// compile-time error, 1? 2?
{% endhighlight %}

* Smaller library features
	* std::make_unique
	* std::integral_constant::operator()
	* std::integer_sequence
	* std::cbegin, cend, rbegin, rend, crbegin, crend
	* std::exchange
	* std::equal, std::mismatch, and std::is_permutation
	* std::is_final
	* std::quoted
