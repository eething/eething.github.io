---
title: "Modern C++ - safe bool"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - explicit
  - safe bool
---

safe bool
===
* ```if (cin)``` 을 사용하고 싶음
	* bool
		* cin << 2
		* shift 연산이 되어버림
	* void *
		* delete cin
	* 함수포인터
		* 다른 함수 포인터에 넣으면?
	* 멤버 함수 포인터
		*  side effect 가 가장 적음
	* explicit operator bool()
		* C++11

{% highlight cpp %}
void func() {}

class istream // basic_istream
{
public:
	bool fail() { return false; }

	// 1 operator bool
	operator bool() { return fail() ? false : true; }

	// 2 operator void*
	operator void*() { return fail() ? 0 : this; }

	// 3 함수 포인터
	typedef void(*F)();
	operator F() { return fail() ? 0 : &func; }

	// 4 멤버 함수 포인터
	// safe bool, side effect 가 가장 적음
	struct Dummy { void func() {} };
	typedef void(Dummy::*DF)();
	operator DF() { retukrn fail() ? 0 : &Dummy::func; }

    // C++11
    explicit operator bool() {}
};
istream cin;

int main()
{
	int n = 2;
	cin >> n;	// 'a'

	if (cin)	// C++98/03 cin.operator void*()
	{			// C++11    cin.operator bool 
		cout << "success" << endl;
	}

	// operator bool
	cin << n;	// 1(true) << 2

	// operator void*
	cin << n;	// error, void* << 2
	delete cin;	// ???

	// operator F
	delete cin; // error
	void (*f)() = cin; // ???

	// operator DF
	void(istream::Dummy::*f)() = cin; // Dummy 의 존재를 알기 어려움
}
{% endhighlight %}
