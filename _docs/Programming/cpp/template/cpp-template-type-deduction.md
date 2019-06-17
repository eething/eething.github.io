
타입 알아내는 방법
====

* typeid
	* C++ 표준
	* const, volatile, reference 인식 불가
* boost::type_index
	* type_id_with_cvr<T>().pretty_name()
	* 변수는 decltype() 사용
{% highlight cpp %}
#include <iostream>
#include <boost\type_index.hpp>
using namespace std;
using namespace boost::typeindex;

template<typename F> void foo(const F f)
{
    cout << "F : " << typeid(F).name() << endl;
    cout << "f : " << typeid(f).name() << endl;
}

template<typename B> void bar(const B b)
{
    cout << "B : " << type_id_with_cvr<T>().pretty_name() << endl;
    cout << "b : " << type_id_with_cvr<decltype(a)>().pretty_name() << endl;
}

int main()
{
    foo(3); // F : int, f : int
    bar(3); // B : int, b : const int
}
{% endhighlight %}



template argument type deduction
====

* type decuction rule
	* 컴파일러가 함수 인자를 보고 타입 결정
	* 함수 인자의 타입과 완전히 동일한 타입으로 결정되지 않음
	* 값 타입 foo(T t)
		* const, volatile, reference 를 제거
		* 인자가 가진 const 만 제거
	* 참조 타입 Bar(T& t)
		* reference 만 제거한다.
		* const, volatile 은 유지
		* const T& 의 경우 const 제거
	* forwarding reference (T&& t)
		* lvalue, rvalue 모두 전달 받음
	* 배열
		* argument decay 발생
{% highlight cpp %}
template<typename T> void foo(T t)
{
    cout << type_id_with_cvr<T>().pretty_name() << endl;
}

template<typename T> void bar(T& t)
{
	cout << type_id_with_cvr<T>().pretty_name() << endl;
	cout << type_id_with_cvr<decltype(t)>().pretty_name() << endl;
}

template<typename T> void car(const T& t)
{
	cout << type_id_with_cvr<T>().pretty_name() << endl;
	cout << type_id_with_cvr<decltype(t)>().pretty_name() << endl;
}

int main()
{
	int n = 0;
	int & r = n;
	const int c = n;
	const int& cr = c;
	const char* s1 = "hello";
	const char* const s2 = "world";

	foo(n);  // T = int
	foo(c);  // T = int
	foo(r);  // T = int
	foo(cr); // T = int
	foo(s1); // T = char const*
	foo(s2); // T = char const*

	bar(n);  // T = int,       t = int&
	bar(c);  // T = int const, t = int const&
	bar(r);  // T = int,       t = int&
	bar(cr); // T = int const, t = int const&

	car(n);  // T = int,       t = int const&
	car(c);  // T = int,       t = int const&
	car(r);  // T = int,       t = int const&
	car(cr); // T = int,       t = int const&
}
{% endhighlight %}


= Array Name =
{% highlight cpp %}
int n1;        // 변수이름 = n, 타입 = int
int *pN = &n1;
int n2 = n1;   // 모든 변수는 동일한 타입의 변수로 초기화(복사) 될 수 있다.

int arr1[3] = { 1, 2, 3 }; // 변수이름 = arr1, 타입 = int[3]
int arr2[3] = arr1;        // error

// 배열의 이름은 첫번째 요소의 주소로 암시적 형변환 된다.
int *pArr0 = arr1;         // 배열의 요소의 주소
int (*pArr1)[3] = &arr1;   // 배열의 주소
// pArr0 + 1 => pArr0 + 4  // sizeof(int)
// pArr1 + 1 => pArr1 + 12 // sizeof(int[3])
*pArr0 = 10;
(*pArr1)[0] = 10;
{% endhighlight %}

* 크기가 다른 배열은 다른 타입
{% highlight cpp %}
template<typename T> void foo(T a, T b) {}
template<typename T> void bar(T& a, T& b) {}

foo("22", "333"); // T = const char*
bar("22", "333"); // T = const char[] error
{% endhighlight %}

* argument decay
{% highlight cpp %}
template<typename T> void foo(T t)
{
	cout << type_id_with_cvr<T>().pretty_name() << endl;
	cout << type_id_with_cvr<decltype(t)>().pretty_name() << endl;
}

template<typename T> void bar(T& t)
{
	cout << type_id_with_cvr<T>().pretty_name() << endl;
	cout << type_id_with_cvr<decltype(t)>().pretty_name() << endl;
}

int main()
{
	int x[3] = { 1, 2, 3 };
	int y[3] = x;    // error
	int *p = x;      // ok
	int (&r)[3] = x; // ok

	//foo(x); // T = int[3] ? foo(int a[3]) => error

	foo(x);	// foo( int* )
		// int*, int*
	bar(x);	// bar(int (&a)[3])
		// int [3], int (&) [3]
}
{% endhighlight %}

class template type deduction
====
* C++17 부터 지원
* 생성자의 인자를 보고 결정
* class template type deduction guide
{% highlight cpp %}
// C++17
template<typename T>
class Test
{
    T data;
    Test() {}
    Test(T t) {}
    template<typename C> Test(C& c) {}
}

// user define deduction guide, C++17
Test() -> Test<int>; 

template <typename C>
Test(C& c) -> Test< typename C::value_type >;
// C = list<int>, C::value_type = int

int main()
{
    Test<int> t1;

    Test t2(10); 
    Test t3;

    //list<int> l = { 1, 2, 3 };
    list l = { 1, 2, 3 }; // C++17
    Test t4(l);
}
{% endhighlight %}

Object Generator Idioms
====
* 클래스 템플릿의 객체를 생성하는 함수 템플릿
{% highlight cpp %}
template<typename T> void foo(T t) {}

template<typename T, typename U> struct pair
{
    T first;
    U second;
    pair(const T& t, const U& u) : first(t), second(u) {}
}

template<typename T, typename U>
pair<T, U> make_pair(const T& t, const U& u)
{
    return pair<T, U>(t, u);
}

int main()
{
    pair<int, double> p( 1, 3.14 );
    foo(p);

    foo( pair<int, double>(1, 3.14) );
    foo( make_pair(1, 3.4) );

    foo( pair(1, 3.4) ); // C++17, 우왕 편하다+_+
}
{% endhighlight %}

Identity
====
* 함수템플릿에서 컴파일러에 의한 타입 추론을 막음
* 사용자가 반드시 타입을 정하도록 함
{% highlight cpp %}
template<typename T> struct identity
{
     typedef T type;
}

template<typename T> void foo(T t) {}
template<typename T> void bar(typename identity<T>::type t) {}

int main()
{
    identity<int>::type n; // int

    foo(3);
    foo<int>(3);

    bar(3); // error
    bar<int>(3);
}
{% endhighlight %}
