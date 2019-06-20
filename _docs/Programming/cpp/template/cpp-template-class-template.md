---
title: "C++ 템플릿 - Class Template"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - template
---


Class Template
---

{% highlight cpp %}
template<typename T>
class Test
{
    static int si;
    T first, second;

    Test( T a = T(), T b = T() ) : first(a), second(b) {} // C++98/03
    Test( T a = {}, T b = {} ) : first(a), second(b) {}   // C++11

    T getFirst() const;

    template<typename U> T foo(const U& c);
};

template<typename T> int Test<T>::si = 0;

template<typename T>
T Test<T>::getFirst() const
{
    return first;
}

template<typename T> template<typename U>
T Test<T>::foo(const U& c)
{
    return second;
}
{% endhighlight %}



Generic Copy Constructor
---
{%  highlight cpp %}
template<typename T>
class Test
{
	T first, second;
public:
	Test( T a = {}, T b = {} ) : first(a), second(b) {}

	template<typename U>
	Test(const Test<U>& rhs); // 일반화된 복사 생성자

	template<typename> friend class Test;
};

template<typename T> template<typename U>
Test<T>::Test(const Test<U>& rhs)
    : first(rhs.first), second(rhs.second) // private 접근
{
}

int main()
{
    Test<int> t1(1, 2);
    Test<int> t2 = t1;
    Test<double> t3 = t1;
}
{% endhighlight %}



Friend Function
---

{% highlight cpp %}
template<typename T> class Test
{
	T first, second;
public:
	Test( T a = {}, T b = {} ) : first(a), second(b) {}

	// Link Error
	// 템플릿이 아닌 일반함수
	// Test::operator<<
    //friend ostream& operator<<(ostream& os, const Test<T>& t);

	//N:N 관계
    // Test<int> - <<(ostream, const Test<int>)
    // Test<int> - <<(ostream, const Test<double>)
    template<typename U>
    friend ostream& operator<<(ostream& os, const Test<U>& t);

    //1:1 관계
    friend ostream& operator<<(ostream& os, const Test<T>& t)
    {
        return os << t.first << ", " << t.second;
    }
};

template<typename T>
ostream& operator <<(ostream& os, const Test<T>& t)
{
    return os << t.first << ", " << t.second;
}

int main()
{
    Test t(1, 2);
    cout << t << endl;
}
{% endhighlight %}
