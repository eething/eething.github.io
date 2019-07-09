---
title: "Modern C++14"
date: 2019-05-14 03:30:00 +0900
tags:
  - programming
  - cpp
---

C++14
===


����Ÿ�� �߷�
---
* auto
	* C++11 ������ lambda ������ ��밡��
	* C++14 ������ ��� �Լ����� ��� ����
	* ���� �� ���� ������ ���Ǹ� �� ���Ŀ� ȣ�� ����
	* Recursion �ÿ��� �ּ��� �ϳ��� return �� ���Ŀ� ���ȣ�� ����
{% highlight cpp %}
auto fact(int i)
{
	if (i==1)
		return i;
	return fact(i-1) * i;
}

auto fact_error(int i)
{
	if (i!=1)
		return fact(i-1) * i;
	return i;	
}
{% endhighlight %}
