---
title: "Test Title 안녕"
date: 2019-05-27 03:30:00 +0900
author_profile: false
---

layout: posts
author_profile: true
author_profile: false

date 테스트
===

* date_to_string
  * {{ page.date | date_to_long_string }}
* date_to_rfc822
  * {{ page.date | date_to_rfc822 }}
* date_to_string
  * {{ page.date | date_to_string }}
* date_to_xmlschema
  * {{ page.date | date_to_xmlschema }}

링크 테스트
===

링크0
---
[어버버]({% link _posts/2019-01-01-test-post.md %})
[베이스]({{ site.baseurl }}{% link _posts/2019-01-01-test-post.md %})

링크1 - test.md
---
* 일케하면 되나?
* [어버버]({% link _posts/2019-01-01-test-post.md %})
* [베이스]({{ site.baseurl }}{% link _posts/2019-01-01-test-post.md %})
* [포스트]({% post_url 2019-01-01-test-post %})

```
* [포스트]({% post_url 2019-01-01-test-post %})
```


목차 테스트
===

* 1 - 탭으로 띄기
	* a
	* b
	* c
* 2 - 숫자...
	1. a
	1. b
	1. c
* 3 - 4줄띄기
    * a
    * b
    * c

코드 테스트
===

{% highlight cpp %}
#include <iostream>
using namespace std;

template<typename T>
class Test
{
    typedef T typeT;
	using usingT = T;

    typeT v1 = 1;
	usingT v2 = 2;
public:

    void foo()
    {
        if (false)
            return;

        for (int i = 0; i < 10; ++i)
        {
            cout << "foo" << endl;
        }
    }

	void bar() const
	{
		cout << "bar" << endl;
	}
};
int main()
{
    Test<int> ttt;
    ttt.foo();
    ttt.bar();
}
#endif
{% endhighlight %}



```cpp
#include <iostream>
using namespace std;

template<typename T>
class Test
{
    typedef T typeT;
	using usingT = T;

    typeT v1 = 1;
	usingT v2 = 2;
public:

    void foo()
    {
        if (false)
            return;

        for (int i = 0; i < 10; ++i)
        {
            cout << "foo" << endl;
        }
    }

	void bar() const
	{
		cout << "bar" << endl;
	}
};
int main()
{
    Test<int> ttt;
    ttt.foo();
    ttt.bar();
}
#endif
```