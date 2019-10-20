---
title: "Modern C++ - constexpr"
date: 2019-10-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - constexpr
---

개요
===
* constexpr 의 작동방식을 정확히 파악해보고자 함
* 컴파일된 어셈블리어를 뜯어보고 확실하게 알고자 함
* Microsoft Visual Studio 2017
	* C++17 옵션은 키지 않음
* 목적 : 문자열 길이를 저장하고자 하다가 생긴 문제를 해결
	* 엔진 초기화가 되지 않은 상태에서
	* 파일 범위의 변수에(std::string) 값을 넣다가 메모리를 건드림
	* char* 혹은 char[] 로 저장하고 compile-time 에 길이를 넣고싶음


constexpr 함수
===
* constexpr 함수가 compile-time 에 실행되는 케이스
	* constexpr 변수에 값을 넣을 때
	* template 에서 값을 필요로 할 때
* 그냥 실행하면 일반함수처럼 run-time 에 실행된다.
