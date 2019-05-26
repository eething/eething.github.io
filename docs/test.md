---
layout: home
author_profile: true
---

링크1 - test.md
{% link test.md %}

링크2 - docs/test.md
{% link docs/test.md %}

링크3 - /docs/test.md
{% link /docs/test.md %}

링크3 - site.baseurl docs/test.md
{{ site.baseurl }}{% link docs/test.md %}


{% highlight cplusplus %}
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



```
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
