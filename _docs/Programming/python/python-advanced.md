---
title: "Python 기초 - 함수, 모듈, 클래스"
date: 2019-10-28 09:30:00 +0900
tags:
  - programming
  - python
---


함수, 메서드
===
* 내장함수
{% highlight python %}
	print()
	len()
	int()
	str()
	sum((1, 2, 3, 4, 5))
	max((1, 2, 3, 4, 5))
	min([1, 2, 3, 4, 5])
{% endhighlight %}

* 사용자 지정 함수
{% highlight python %}
	def plus(a, b):
		c = a + b
		return c
	print(plus(1,2))
{% endhighlight %}

* 전역변수, 지역변수
	* ㅇㅇ

* Method
	* 특정 자료에 대해 특정 기능을 하는 코드
	* 는 개뿔 걍 멤버함수라는 거...
{% highlight python %}
	my_list.append()
	my_list.count()
	my_list.pop()
{% endhighlight %}

모듈
===
* cal.py
{% highlight python %}
	def plus(a, b)
		return a + b
{% endhighlight %}

* main.py
{% highlight python %}
	import cal
	print(cal.plus(1, 2))
{% endhighlight %}

* 패키지
	* 폴더로 관리
{% highlight python %}
	import user.cal
	print(cal.plus(3, 4))

	from user.cal import plus
	print(plus(5,6))
{% endhighlight %}

객체
===
* 필드
* 메서드
{% highlight python %}
	class Animal:
		name = "Dongmul"
		age = 0
		def sleep(self):
			print("zzz")

	# 상속
	class Dog(Animal):
		name = "Baduk"
		age = 10
		def bow(self):
			print("DangDang!")

	coco = Dog()

	coco.name = "coco"
	coco.sleep()
	coco.bow()
{% endhighlight %}
