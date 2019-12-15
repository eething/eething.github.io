---
title: "C++ 템플릿 - 컴파일 2"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - template
  - template specilization
---

클래스 템플릿의 멤버함수 특수화
---
* 멤버함수도 일반함수의 특수화 규칙을 따른다.
* 부분특수화 안됨, 오버로딩?
* 전체특수화의 경우 inline 필요
* 부분특수화 한 클래스의 멤버함수를 전체특수화 할 수 없음


클래스 선언
---
{% highlight cpp %}
template <typename T>
class TEST
{
public:
	template <typename U>
	void foo1() {}	// 정의까지 함

	template <typename U>
	void foo2(U) {}	// 정의까지 함

	template <typename U>
	void bar1();	// 선언만 함

	template <typename U>
	void bar2(U);	// 선언만 함
};
{% endhighlight %}


부분특수화 후 전체특수화
---
* 응 그냥 다 앙대
{% highlight cpp %}
// template <typename T> template <> void TEST<T>::foo1<int>() {}
// template <typename T> template <> void TEST<T>::foo2<int>(int) {}
// template <typename T> template <> void TEST<T>::bar1<int>() {}
// template <typename T> template <> void TEST<T>::bar2<int>(int) {}
{% endhighlight %}


부분특수화 후 부분특수화
---
{% highlight cpp %}
// C2768, 명시적 템플릿 인수를 잘못 사용하였습니다. 함수에 부분특수화 <U> 안됨
// template <typename T> template <typename U> void TEST<T>::foo1<U>() {}
// template <typename T> template <typename U> void TEST<T>::foo2<U>(U) {}
// template <typename T> template <typename U> void TEST<T>::bar1<U>() {}
// template <typename T> template <typename U> void TEST<T>::bar2<U>(U) {}

// C2995, 함수템플릿이 이미 정의되었습니다.
// template <typename T> template <typename U> void TEST<T>::foo1() {}
// template <typename T> template <typename U> void TEST<T>::foof2U) {}

// OK
template <typename T> template <typename U> void TEST<T>::bar1() {}
template <typename T> template <typename U> void TEST<T>::bar2(U) {}
{% endhighlight %}

전체특수화 후 전체특수화
---
* LNK 2005로 인한 inline 필요
* inline은 위 클래스에서의 선언에 붙여주면 안됨
* 템플릿 함수를 특수화한 것이므로 특수화 된 곳에서 붙여줘야 함
{% highlight cpp %}
// LNK 2005, inline 필요
// template <> template <> void TEST<int>::foo<bool>() {}
// template <> template <> void TEST<int>::foofoo<bool>(bool) {}
// template <> template <> void TEST<int>::bar<bool>() {}

// foo1 와 bar1 같은 경우 파라미터가 없어서 <bool> 가 반드시 있어야 함
// template <> template <> inline void TEST<int>::foo1() {}
// template <> template <> inline void TEST<int>::bar1() {}

template <> template <> inline void TEST<int>::foo1<bool>() {}
template <> template <> inline void TEST<int>::foo2<bool>(bool) {}
template <> template <> inline void TEST<int>::foo2(bool) {}
template <> template <> inline void TEST<int>::bar1<bool>() {}
template <> template <> inline void TEST<int>::bar2<bool>(bool) {}
template <> template <> inline void TEST<int>::bar2(bool) {}
{% endhighlight %}


전체특수화 후 부분특수화
---
{% highlight cpp %}
// C2768, 명시적 템플릿 인수를 잘못 사용하였습니다. 함수에 부분특수화 <U> 안됨
// template <> template <typename U> void TEST<int>::foo1<U>() {}
// template <> template <typename U> void TEST<int>::foo2<U>(U) {}
// template <> template <typename U> void TEST<int>::bar1<U>() {}
// template <> template <typename U> void TEST<int>::bar2<U>(U) {}

// OK
template <> template <typename U> void TEST<int>::foo1() {}
template <> template <typename U> void TEST<int>::foo2(U) {}
template <> template <typename U> void TEST<int>::bar1() {}
template <> template <typename U> void TEST<int>::bar2(U) {}
{% endhighlight %}
