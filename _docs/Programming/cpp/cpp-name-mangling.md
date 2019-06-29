---
title: "Modern C++ - Name Mangling"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
---

Name Mangling
---
* 컴파일러가 컴파일시간에 심볼 이름을 변경
* 오버로딩, namespace, template 등...
* C는 함수 오버로딩을 제공하지 않아 Name Mangling 없음

* C++ 로 컴파일
	* 확장자 .cpp (cl, gcc, g++)
	* 확장자 .c (g++)

* C 로 컴파일
	* 확장자 .c (cl, gcc)
	* 확장자 .cpp 에서 extern "C"
		* C++ 문법이라 C 에서는 에러

{% highlight cpp %}
int foo(int) {}
	// g++ _Z6fooi
	// cl  ?foo@YAHH@Z

#ifdef __cplusplus
extern "C" {
#endif

	int foo(int) {}

#ifdef __cplusplus
}
#endif

double foo(double) {}
	// g++ _Z6food
	// cl  ?foo@YANN@Z

#ifdef __cplusplus
extern "C" {
#endif

    double foo(double) {} // error 오버로드 불가

#ifdef __cplusplus
}
#endif
{% endhighlight %}
