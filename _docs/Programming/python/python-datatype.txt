---
title: "Python 기초 - 자료형"
date: 2019-10-28 09:30:00 +0900
tags:
  - programming
  - python
---

시퀀스 자료형
===

* 문자열, 리스트, 튜플
* 인덱싱, 슬라이싱, +, *, in, len
{% highlight python %}
	a = "01234"
	b = [0, 1, 2, 3, 4]
	c = (0, 1, 2, 3, 4)

	b[1] # [1]
	b[-1] # 4
	b[2:4] # [2, 3]	
	b[:-2] # [0, 1, 2]

	print(3 in b) # True
	print(5 in c) # False

	print(len(a))
	print(b + [1, 2, 3])
	print(c * 3)
{% endhighlight %}

* count
{% highlight python %}
	my_seq = [1, 2, 1, 2, 1]
	print(my_seq.count(2)) # 2
{% endhighlight %}

list
===

* append, insert, remove
{% highlight python %}
	l = []
	l.append(1) # [10]
	l.append('b') # [10, 'b']

	l.insert(1, 'a') # [10, 'a', 'b']

	l.append('a') # [10, 'a', 'b', 'a']
	l.remove('a') # [10, 'b', 'a']
{% endhighlight %}

* sort
{% highlight python %}
	l1 = [1, 3, 2]
	l2 = ['c', 'a', 'b']

	l1.sort() # [1, 2, 3]
	l2.sort() # ['a', 'b', 'c']
{% endhighlight %}

* pop
{% highlight python %}
	my_list = [1, 2, 3, 4, 5]
	print(my_list.pop(2)) # 3, [1, 2, 4, 5]
	print(my_list.pop(-2)) # 4, [1, 2, 5]
	print(my_list.pop()) # 5, [1, 2]
{% endhighlight %}

str
===
* split
{% highlight python %}
	my_str1 = "1 2 3 4 5"
	print(my_str1.split()) # ['1', '2', '3', '4', '5']

	my_str2 = "a,b,c,d,e"
	print(my_str2.split(',')) # ['a', 'b', 'c', 'd', 'e']
{% endhighlight %}

* join
{% highlight python %}
	my_list = ['a', 'b', 'c']
	print(''.join(my_list)) # 'abc'
	print(' + '.join(my_list)) # 'a + b + c'
{% endhighlight %}

tuple
===
* 값 변경 불가
{% highlight python %}
	my_list = ['l', 'i', 's', 't']
	my_list[1] = 'a' # 변경 가능
	print(my_list) # ['l', 'a', 's', 't']

	tuple_0 = ()
	tuple_1 = (1,) # 콤마 찍어서 튜플임을 알려주기
	tuple_2 = (1, 2, 3, 4, 5)
	tuple_3 = 1, 2, 3, 4, 5

	tuple_3[1]
	tuple_3[2:4]
	print(3 in tuple_3)
	print(len(tuple_3))
	
	tuple_2 + tuple3
	tuple_2 * 3

	값 추가, 삭제, 변경 불가
	tuple_3[3] = 44 # Error
	tuple_3.append(6) # Error
	tuple_3.remove(6) # Error
{% endhighlight %}

dictionary
===
* Key - Value
{% highlight python %}
	dict_0 = {}
	dict_1 = {'key':'value', 'name':'ithing'}
	print(dict_1['key'])
	print(dict_1['name'])
	
	dict_1['age'] = 100
	del dict_1['name']
	print(dict_1) # {'key':'value', 'age':100}
{% endhighlight %}

* Key : 변할 수 없는 자료형
	* 리스트는 불가능 튜플은 가능
{% highlight python %}
	datas = { [1, 2, 3]: 'number' } # Error
	datas = { (1, 2, 3): 'number' } # OK
{% endhighlight %}
