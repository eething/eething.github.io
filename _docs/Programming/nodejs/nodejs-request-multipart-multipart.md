---
title: "Node.js - Multipart 생성하기 (multipart 를 활용)"
date: 2020-04-11 15:30:00 +0900
tags:
  - programming
  - nodejs
  - http
  - request
---

코드분석을 통해 알아보는 multipart 구조 분석
===

* npm 설치
	* npm i request, request-promise
* 분석 코드
	* request/request.js
	* request/lib/multipart.js
* 참고자료
	* [참고자료 1](https://github.com/request/multipart)
* 결론부터 말하자면
	* multipart/related 용도임
	* 어거지로 form-data 화 할 수 있긴 하다-_-
* multipart/related 란???
	* 아직 모름 ㅋ

HTTP 패킷 구성
---
* header
	* Content-Type: multipart/related; boundary=myboundary
	* Content-Length: xxx
* body
{% highlight text %}
--myboundary\r\n
key1: value1\r\n
key2: value2\r\n
\r\n
body1\r\n
--myboundary--
{% endhighlight %}

코드 예제
---
{% highlight javascript %}
const rp = require('request-promise');
const fs = require('fs');

async function upload(options) {

	let options = {
		url: 'https://localhost/upload/',
		method: 'POST',
		multipart: [ { key1: 'value1', body: 'body1' }, ... ],

	/*
		headers: {
			'Content-Type': 'multipart/form-data; boundary=myboundary',
			Content-Length: xxx
		}
	*/
	};

	try {
		let res = await rp(options);
		console.log(res);
	} catch (e) {
		console.log(e);
	}
}
{% endhighlight %}

multipart 로 body 를 만드는 과정
---

* request/request.js
	* self._multipart = new Multipart(self)
	* self._multipart.onRequest(options.multipart)

* request/lib/multipart.js - onRequest(options)
	* var parts = options.data, options
	* self.setHeaders(chunked) // multipart/related
	* self.body = self.build(parts, chunked)
		* \r\n // preambleCRLF
		* for
			* --myboundary\r\n
			* for
				* key: value\r\n
			* \r\n
			* body\r\n
		* --myboundary--
		* \r\n // postambleCRLF

* options - multipart 구성
	* [여기](https://github.com/request/multipart)를 보면 설명이 있지만 좀 이상하다?
	* object 도 되는 것처럼 되어 있지만 무적권 array 여야 함
	* body 는 무적권 있어야 함

{% highlight javascript %}
var options = {
	// related
	multipart: [],

	// form-data
	multipart: {}, // 이거 에러

	// optional
	contentType: 'multipart/related; boundary=myboundary', // 작동안함
	boundary: '', // 작동안함
	preambleCRLF: true,
	postambleCRLF: true
}

// related
multipart: [{key: 'value', body: 'body'}]

/* 뭥미???
// form-data
multipart: { key: 'value' }
multipart: { key: ['value', 'value'] }
multipart: {
	key: {
		value: 'value',
		options: { filename: '', contentType: '', knownLength: 0 }
	}
*/
}
{% endhighlight %}


실제 수행 결과
---
{% highlight javascript %}
options = {
	multipart: [
		{ key1: 'value1', key2: 'value2', body: 'body1' },

		{ keyarr: [ 'this', 'is', 'array'], body: 'body2' },

		{ body: 'bodyonly' },

		{
			'Content-Disposition': 'form-data; name="key3"',
			body: 'value3'
		},

		{
			'Content-Disposition': 'form-data; name="file"; filename="fn.jpg"',
			'Content-Type': 'image/jpeg',
			//body: fs.createReadStream('fn.jpg')
			body: 'this is fn.jpg'
		}
	],
	preambleCRLF: true,
	postambleCRLF: true
};
{% endhighlight %}

* 결과
{% highlight text %}
\r\n
--myboundary\r\n
key1: value1\r\n
key2: value2\r\n
\r\n
body1\r\n
--myboundary\r\n
keyarr: this,is,array\r\n
\r\n
body2\r\n
--myboundary\r\n
\r\n
bodyonly\r\n
--myboundary\r\n
Content-Disposition: form-data; name="key3"\r\n
\r\n
value3\r\n
--myboundary\r\n
Content-Disposition: form-data; name="file"; filename="fn.jpg"\r\n
Content-Type: image/jpeg\r\n
\r\n
this is fn.jpg\r\n
--myboundary--\r\n
{% endhighlight %}
