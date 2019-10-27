---
title: "Python 기초 - 기초"
date: 2019-10-28 09:30:00 +0900
tags:
  - programming
  - python
---


변수
===
* 변수명
	* 숫자, 알파벳, 한글 언더바(_)
	* 숫자로 시작 안됨

* 숫자
	* int : 314
	* float : 3.14
	* 복소수 : 3+14j

* 문자열
	* '3.14'
	* "3.14"

* List
	* []
	* ['pi', 3.14]

* Comment
	* #한줄주석
	* 여러줄주석
{% highlight python %}
	'''
	어쩌고
	저쩌고
	'''
	"""
	궁시렁
	궁시렁
	"""
{% endhighlight %}

연산자
===
* 숫자
	* ** 제곱
	* % 나머지
	* // 몫

* 문자열
	* "A" + "B" # AB
	* "A" * 3 # AAA

* 인덱싱
	* string / list
	* [begin:end]
	* data = ['a', 'b', 'c', 'd', 'e']
	* print(data[1:3]) # ['b', 'c']

입출력
===
* 출력
	* print(1, "hello", 3+4j)
* 입력
	* var input()

형 변환
===

{% highlight python %}
strVar = '123'
numVar = int('123')
print(strVar, numVar);
print(type(strVar)) # <class 'str'>
print(type(numVar)) # <class 'int'>
{% endhighlight %}

if, 논리자료형, 비교연산자
===
{% highlight python %}
	print(True and 3==3)
	print(False or 3!=4)
	print(not 3>=4)

	x = input()
	if x in ['a', 'b', 'c']:
		print('abc')
	elif x in ['x', 'y', 'z']:
		print('xyz')
	else:
		print('error')
{% endhighlight %}

반복문
===

{% highlight python %}
	sum = 0
	for i in [1, 2, 3]:
		print(i)
		sum = sum + i

	range(a, b)
		a 이상 b 미만
		a, a+1, ..., b-1

	for i in range(2, 5): # 2, 3, 4
		print(i)

	i = 10
	while i>0:
		print(i)
		i = i - 1
		if i<5:
			break
{% endhighlight %}
