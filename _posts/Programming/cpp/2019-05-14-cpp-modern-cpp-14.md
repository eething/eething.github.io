---
title: "Modern C++14"
date: 2019-05-14 03:30:00 +0900
tags:
  - programming
  - cpp
---

C++14
===


����Ÿ�� �߷�
---
* auto
	* C++11 ������ lambda ������ ��밡��
	* C++14 ������ ��� �Լ����� ��� ����
	* ���� �� ���� ������ ���Ǹ� �� ���Ŀ� ȣ�� ����
	* Recursion �ÿ��� �ּ��� �ϳ��� return �� ���Ŀ� ���ȣ�� ����
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
	* [auto �� decltype �� Ÿ���߷й���� �ٸ���.]({% link _docs/Programming/cpp/cpp-auto.md %})
	* auto �� ������ decltype ����� ���� ���� �� ���
	* C++11 �� trailing return type �� ���� �����ϰ� ����
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

constexpr function ������ ���� ��ȭ
---
* ���� - �Ʒ����� ���� �� ��� ����
	 * static
	 * thread_local
	 * �ʱ�ȭ �ȵ� ����
* if, switch, ������ ��� ����
* goto ���Ұ�

* C++11 ������ (non-static)����Լ��� constexpr �� �ᵵ const �� ��޵�
* C++14 ������ (non-static)����Լ��� constexpr ������ const �� �ƴ� �� ����


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
* C++11 ������ Ŭ������ member initializer �� �ִ� ��쿣 Aggregate �� �ƴϾ���
* C++14 ������ Aggregate, ���� ���� ������ member initializer �� �ʱ�ȭ


Binary literals
---
* 0b 0B 


Digit separators
---
* ���� ����� ���� ���ϱ� �����̹Ƿ� �ƹ����Գ� ��(?) �� ��
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
* C++11 ������ ĸ�� ����� ��-���� �̳� ���� ��ĸ� �־���
* C++14 ������ ������ ������ ���� �� ����
	* ��-�̵� ��ĵ� ����������, �ܺο� �̸��� ���� �͵� ����

{% highlight cpp %}
auto l1 = [value = 1] { return value; };

std::unique_ptr<int> ptr(new int(10));
auto l2 = [value = std::move(ptr)] { return *value; };
{% endhighlight %}


[Attributes]({% link _docs/Programming/cpp/cpp-attributes.md %})
---
* [[deprecated]]


STL
---
* Shared mutexes and locking
	* �Ƹ���

* Heterogeneous lookup in associative containers
	* map, set ���� �����̳ʿ��� �˻��� �ϰ� ���� ��
	* Ȥ�� std::less, std::greater �� ����� ��,
	* ��Ȯ�� ���� Ÿ���� �ƴϾ ã�� �� ����
	* ������� string �� const char*
	* operator< ���� ���� �� ������ ��	
	* key �� ��ü�� ��� �˻��� ���� ��ü�� �������� �ʾƵ� ��

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
