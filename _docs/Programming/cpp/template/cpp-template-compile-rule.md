---
title: "C++ 템플릿 - 컴파일"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - template
---

템플릿 컴파일
===
* 템플릿 헬이 펼쳐져서 컴파일 혹은 링크가 안되는 상황이 발생하여 정리해봄
* 함수 템플릿과 클래스 템플릿이 컴파일(템플릿 인스턴스화)되는 방식이 다름


함수 템플릿의 특수화
---
	* 여러 cpp 에 include 되면 링크시 중복 에러(LNK2005)
	* inline 을 붙여주면 OK
		* 선언이나 정의 둘중에 한개만 해줘도 된다
	* Precompiled Header 에 넣으면 한개만 생성되서 OK
{% highlight cpp %}
// func.h
template <typename T> void foo(T t) {}

// LNK2005, 여러 cpp 에 정의가 됨
// Precompiled Header 에 넣으면 한개만 생성되서 OK
template <> void foo(int t) {}

// OK
template <> inline void foo(int t) {}
{% endhighlight %}


클래스 템플릿의 특수화
---
* "class { ... };" 는 클래스의 선언 이다.
* 클래스 자체를 특수화
	* template <> class TEST<int> { ... };
	* 특수화 된 클래스는 아예 다른 클래스라고 생각하면 됨


클래스 템플릿 멤버함수의 특수화
---
* 클래스 자체를 특수화 하는 것과는 달리 특정 함수만 특수화가 가능
	* 나머지는 기본 템플릿 유지
* 생각보다 복잡해 지므로 주의깊게 테스트 해볼 것
* 멤버함수가 선언만 되있는지 정의까지 되있는지에 따라 컴파일 결과가 달라짐
* 함수처럼 특수화된다고 보면 됨

* 헤더 하나에 다 때려박는 일반적인 방식
	* 어지간하면 이렇게 써야 함
	* 멤버함수를 정의까지 한 경우
		* 템플릿 특수화를 컴파일러가 알아서 잘 수행해줌
	* 멤버함수를 선언만 해준 경우
		* 위의 함수처럼 취급함
		* inline 이면 OK
		* 선언이나 정의 둘중에 한개만 해줘도 된다
		* Precompiled Header 에 넣으면 한개만 생성되서 OK
{% highlight cpp %}
// test.h
template  <typename T>
class TEST
{
	void foo() {}	// 정의까지 함
	void bar() ;	// 선언만 함
};

// OK
template <>
void TEST<int>::foo()
{
}

// LNK2005
// 기본 템플릿에서 선언만 해준 경우
// 여러 cpp 에서 instantiation 을 시도하여 여러번 정의
template <>
void TEST<int>::bar()
{
}
{% endhighlight %}

* 클래스 멤버함수 특수화 정의를 cpp 로 분리하는 방식
	* 클래스 선언시, 멤버함수가 선언만 있는 경우 // bar 
		* 멤버함수 정의가 어딘가에 있다고 가정하고 빌드 시도
		* LNK2005 에러가 나지 않고 잘 컴파일 됨
		* 정의가 아무데도 없으면 LNK2019 (외부 기호 확인 불가)
	* 클래스 선언시, 멤버함수가 정의까지 있는 경우 // foo
		* 기본적으로, 특수화 버전이 아닌 기본 템플릿이 사용됨
		* caller 와 같은 파일에 특수화 정의를 하면 잘 컴파일 됨
		* 다른 파일에 정의를 하면 기본 템플릿이 호출됨
			* instantiation 이 안되기 때문
		* 아래와 같이 선언을 해주면 특수화 버전을 찾음
			* template <> void TEST<int>foo();
			* caller cpp 에서 선언을 인식하는 것이 중요
		* 그럼에도 불구하고 없을 수 있음
			* 명시적 instantiation 을 해줘야 함
			* template void TEST<int>::foo();
		* 문제점
			* cpp 여러군데에 넣으면 에러가 나지 않고
			* 어느 것이 instantiation 이 될지 알 수 없음
			* clang 에서는 이 방식이 에러가 남
				* -Winstantiation-after-specialization
				* explicit instantiation of A that occurs after an explicit specialization has no effect
		* 그냥 이 방식은 안쓰는 것이 좋겠다-_-

{% highlight cpp %}
// test.h

template <typename T>
class TEST
{
	void foo() { cout << "basic" << endl; }
	void bar() ;
}

// specialization 을 찾을 수 있도록 선언을 해줌
template <> TEST::foo();
{% endhighlight %}

{% highlight cpp %}
// test.cpp

// LNK2019, 선언, 정의를 해줬으나 instantiation 이 안됨
template <>
void TEST<int>::foo()
{
	cout << "specialization" << endl;
}

// 그래서 명시적 explicit instantiation
// VC++ 에서는 잘되는데 clang 이 에러를 뱉음-_-
template void TEST<int>::foo();

// OK, 적당한 cpp 한곳에만 있으면/있어야 된다.
template <>
void TEST<int>::bar()
{
	cout << "specialization" << endl;
}
{% endhighlight %}

{% highlight cpp %}
// main.cpp

int main()
{
    TEST<int> t;
    t.foo();	// basic
    t.bar();	// specialization

    return 0;
}
{% endhighlight %}

* 결론
	* foo 처럼 정의가 있는 경우
		* explicit declaration 없으면 일반 템플릿을 사용
		* explicit instantiation 으로 특수화 생성 (clang 에러)
		* specialization 정의
			* header 에 있으면 그것을 호출
			* cpp 에 있으면 instantiation 필요
			* cpp 여러 곳에 있으면 무엇이 불릴지 알 수 없음
	* bar 처럼 선언만 해준 경우
		* explicit declaration 필요 없음
		* explicit instantiation 필요 없음
		* specialization 정의
			* 반드시 cpp 한곳에 있어야 함
