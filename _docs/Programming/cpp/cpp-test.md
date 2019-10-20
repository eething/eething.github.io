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

{% highlight cpp %}
int constexpr C_length(const char* str)
{
    return *str ? 1 + C_length(str + 1) : 0;
}

// compile-time 이든 run-time 이든 속도는 이게 더 빠를 듯?
// 하지만 const char* 와 char[] 가 다름에 주의하자
template <int N>
constexpr int T_length(char const (&)[N])
{
    return N - 1;
}

template<int N>
constexpr int twice()
{
    return N * 2;
}

void main()
{
    // 그냥 함수호출하면 run-time 호출을 한다.
    test(C_length("asdf"));
        // push     offset string "asdf"
        // call     C_length
        // add      esp, 4
        // push     eax
        // call     test
        // add      esp, 4

    test(T_length("asdf"));
        // push     offset string "asdf"
        // call     T_length<5>
        // add      esp, 4
        // push     eax
        // call     test
        // add      esp, 4

    // compile-time case 1 : constexpr 변수에 값을 넣을 때
    constexpr int a0 = C_length("asdf");
        // mov      dword ptr [a0],4
    constexpr int a1 = T_length("asdf");
        // mov      dword ptr [a1],4
    test(a0);
    test(a1);
        // push     4
        // call     test
        // add      esp, 4

    // const 변수에 넣으면 어떻게 될까?
    // 이상한 최적화??? 를 수행하여
        // 변수선언 시에는 run-time 에 함수호출
        // 변수사용 시에는 compile-time 에 치환
    const int a2 = C_length("asdf");
        // push     offset string "asdf"
        // call     C_length
        // add      esp, 4
        // mov      dword ptr[a2], eax
    const int a3 = T_length("asdf");
        // push     offset string "asdf"
        // call     T_length<5>
        // add      esp, 4
        // mov      dword ptr[a3], eax
    test(a2);
    test(a3);
        // push     4
        // call     test
        // add      esp, 4

    // compile-time case 2 : template 에서 사용될 때
    twice<C_length("asdf")>();
        // call     twice<4>
    constexpr int a4 = twice<C_length("asdf")>();
        // mov      dword ptr[a4], 8

    // const 는 위에서 봤던 것처럼 된다.
    const int a5 = twice<C_length("asdf")>();
        // call     twice<4>
        // mov      dword ptr[a5], eax

    test(a4);
    test(a5);
        // push     8
        // call     test
        // add      esp, 4
}
{% endhighlight %}


* 연쇄적으로 호출해 보면 어떻게 될까?
	* 그냥 하면 무식하게 run-time 함수콜 3번
	* constexpr 변수에 넣으면 compile-time 에 넣음

{% highlight cpp %}
void main2()
{
    test(test(C_length("asdf")));
        // push     offset string "asdf"
        // call     C_length
        // add      esp, 4
        
        // push     eax
        // call     test
        // add      esp, 4
        
        // push     eax
        // call     test
        // add      esp, 4

    constexpr int ret1 = C_length("asdf");
        // mov      dword ptr[ret1], 4
    constexpr int ret2 = test(ret1);
        // mov      dword ptr[ret2], 5
    constexpr int ret3 = test(ret2);
        // mov      dword ptr[ret3], 6

    // !!! 하악 !!!
    constexpr int ret4 = test(test(C_length("asdf")));
        // mov      dword ptr[ret4], 6

    // template 에서 필요로 하는 부분까지만 compile-time 에 계산하고
    // 정작 template 함수 자체는 run-time 에 ㅋㅋ
    twice<test(C_length("asdf"))>();
        // call     twice<5>
}
{% endhighlight %}


멤버변수를 constexpr 로 선언
===
* constexpr 은 static 에서만 사용 가능
* cosntexpr 선언시 바로 값을 넣어줘야 함. compile-time 이니까
* 선언시 초기값을 넣어준 static const 는 static constexpr 과 동작이 같다.

{% highlight cpp %}
struct Value
{
    const int m_value;
    const int m_value2;
    const int m_value3;
    const int m_value4 = 256;

    //constexpr int x_value = 1024;     ERROR, static 아님
    //static constexpr int sce_value;   ERROR, 초기값 없음
    static constexpr int sce_value = C_length("asdf") * 25; // 100

    static const int sc_value = C_length("asdf") * 50; // 200
    static const int sc_value2;
};

//constexpr int Value::sce_value = 100; ERROR, 선언에 있어야 함

// run-time CRT 초기화
const int Value::sc_value2 = C_length("asdf") * 50; // 200
    // push     offset string "asdf"
    // call     C_length
    // add      esp, 4
    // imul     eax, eax, 32h
    // mov      dword ptr[Value::sc_value2], eax

void main()
{
    // compile-time
    constexpr Value v{ C_length("as"), T_length("asd") };
        // mov      dword ptr[v], 2
        // mov      dword ptr[ebp-10h], 3
        // mov      dword ptr[ebp-0Ch], 0
        // mov      dword ptr[ebp-8], 100h

    // 멤버변수는 값을 메모리에서 가져옴
    test(v.m_value);
        // mov      eax, dword ptr[v]
        // push     eax
        // call     test
        // add      esp, 4
    test(v.m_value2);
        // mov      eax, dword ptr[ebp-10h]
        // push     eax
        // call     test
        // add      esp, 4

    // 기본타입을 constexpr 로 선언하면 변수선언과 사용 둘다 compile-time 에 치환
    constexpr int value = v.m_value;
        // mov      dword ptr[value], 2

    test(value);
        // push     2
        // call     test
        // add      esp, 4

    // static constexpr 변수 는 compile-time 에 치환
    test(v.sce_value);
    test(Value::sce_value);
        // push     64h
        // call     test
        // add      esp, 4

    // static const 변수도 compile-time 치환이 잘 된다.
    test(v.sc_value);
    test(v.sc_value2);
    test(Value::sc_value);    
    test(Value::sc_value2);
        // push     0C8h
        // call     test
        // add      esp, 4
}

{% endhighlight %}



