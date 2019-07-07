---
title: "Modern C++ - Literal"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - literal
---

Literal
===

{% highlight cpp %}
int main()
{
	float a = 3.14f;
	long b = 10l;

	std::chrono::seconds time = 1h + 5min + 10s;
	cout << time.count() << endl;
}
{% endhighlight %}

문자열 리터럴
---

* sadf
u8"utf8 \u2018."
u"unicode16 : \u2018."
U"unicode32 : \U00002018."

R"(The String Data \ Stuff " )"
R"delimiter(The String Data \ Stuff " )delimiter"




* std::literals::string_literals::operator""s
	* C++14 : string, u16string, u32string, wstring
	* C++20 : u8string

{% highlight cpp %}
#include <iostream>
#include <string>

using namespace std;

int main()
{
	// literals, string_literals 가 inline 이라
		// std 만 있어도 잘됨
		// std 가 없으면 아래 3개 中 아무거나
	// using namespace std::literals;
	// using namespace std::string_literals;
	// using namespace std::literals::string_literals;

	string s1 = "abc\0def";
	string s2 = "a\0\0bc"s;
	cout << s1.size() << " : " << s1 << endl; // 3 : abc
	cout << s2.size() << " : " << s2 << endl; // 5 : a  bc
}
{% endhighlight %}


User Define Literal
---

* 사용자타입은 _ (언더바) 붙여줘야 함
* 인자 타입은 다음만 허용
	* unsigned long long
	* const char *

{% highlight cpp %}
class Meter
{
	int value;
public:
	Meter(int n) : value(n) {}
};

Meter operator ""_m(unsigned long long a)
{
	return Meter(static_cast<int>(a));
}

int main()
{
	Meter m2 = 5_m;
}
{% endhighlight %}
