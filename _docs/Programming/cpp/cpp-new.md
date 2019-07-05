---
title: "Modern C++ - new"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - new
---

new 와 delete 의 동작
---
* new
	1. operator new
	1. static_cast
	1. 생성자 호출

* delete
	1. 소멸자 호출
	1. operator delete

{% highlight cpp %}
void* operator new(size_t sz)
{
	cout << "operator new" << endl;
	return malloc(sz);
}

void* operator new(size_t sz, int n, const char* tag)
{
	cout << "operator new : " << tag << endl;
	return malloc(sz * n);
}

void operator delete(void* p) noexcept
{
	cout << "operator delete" << endl;
	free(p);
}

int main()
{
	Test* t0 = new Test;
	Test* t1 = new (3, "Tag") Test;
		// p = operator new(sizeof(Test), 3, "Tag");
		// t1 = static_cast<Test*>(p);
		// t1.Test();

	delete t1;
		// t1.~Test();
		// operator delete(t1);
}
{% endhighlight %}


Placement new
---
* 메모리할당 없이 생성자호출만 함
{% highlight cpp %}
//void* operator new(size_t sz, Test* ptr)
operator new(size_t sz, void* ptr) // C++ 표준
{
	return ptr;
}

class Test
{
public:
	Test(int a, int b) {}
};

int main()
{
	Test t;
	//t.Test();	// 생성자 호출을 하고 싶지만 error
	t.~Test();	// 소멸자 호출은 가능

	//Test *pTest = malloc(sizeof(Test));
	Test *pTest = static_cast<Test*>(operator new(sizeof(Test))); 
	new (pTest) Test(1, 2);
	
	// Test* p = new Test[10](1, 2);
	// C++ 기본문법으로는 객체 배열에
	// 기본생성자가 아닌 생성자를 호출하는 방법이 없음
	Test* p = static_cast<Test*>(operator new(sizeof(Test) * 10));
	for (int i = 0; i < 10; ++i)
	{
		new (&p[i]) Test(1, 2);
	}

	for (int i = 9; i >= 0; --i)
	{
		p[i].~Test();
	}
	operator delete(p);
}
{% endhighlight %}
