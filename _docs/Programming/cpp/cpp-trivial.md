---
title: "Modern C++ - Trivial"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
---

Trivial
---

* Trivial : 하는 일이 없는...
	* 생성자가 없거나 default
	* 가상함수테이블 없어야 함

{% highlight cpp %}
// Trivial Default Constructor
struct Base {};

class Trivial : public Base // Base 는 Trivial 해야 함
{
	int n;
	Base t; // 객체형 멤버는 Trivial 한 것만 허용
public:
	B() = default;
	void foo() {}
};

class NonTrivial
{
	int n = 10; // 초기화 X
public:
	C() {} // 사용자정의 생성자 X

	virtual void foo() {} // 가상함수 X
	// 생성자가 가상함수테이블 만드는 "일"을 함
};

int main()
{
	cout << is_trivially_constructible<Trivial>::value << endl; // true
	cout << is_trivially_constructible<NonTrivial>::value << endl; // false
}
{% endhighlight %}


Trivial 의 용도
---
* 깊은복사를 해야 하는지 판단하기 위해

{% highlight cpp %}
template<typename T>
enable_if<is_trivially_copyable<T>::value, void>::type
copy(T* dest, T* src, size_t sz)
{
	memcpy(dest, src, sizeof(T) * sz);
}

template<typename T>
enable_if<!is_trivially_copyable<T>::value, void>::type
copy(T* dest, T* src, size_t sz)
{
	while(sz--)
	{
		new (dest) T(*src);
		++dest;
		++src;
	}
}

class Trivial
{
	Trivial() {}
	Trivial(const Test&) = default;
};

class NonTrivial
{
	NonTrivial() {}
	NonTrivial(const Test&) {}
};

int main()
{
	Trivial t1[10];
	Trivial t2[10];

	copy(t2, t1, 10);

	NonTrivial nt1[10];
	NonTrivial nt2[10];

	copy(nt2, nt1, 10);
}
{% endhighlight %}
