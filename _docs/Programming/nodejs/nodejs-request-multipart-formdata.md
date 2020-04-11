---
title: "Node.js - Multipart 생성하기 (formData 를 활용)"
date: 2020-04-11 15:30:00 +0900
tags:
  - programming
  - nodejs
  - http
  - request
---

코드분석을 통해 알아보는 multipart 구조 분석
===
* 코드
	* npm i request, request-promise
	* request/request.js
	* form-data/lib/form_data.js
* 참고자료
	* [참고자료 1] (https://gist.github.com/tanaikech/40c9284e91d209356395b43022ffc5cc)


HTTP 구성
---
* header
	* Content-Type: multipart/form-data; boundary=myboundary
	* Content-Length: xxx
* body
```
--myboundary\r\n
Content-Disposition: form-data; name="key"\r\n
\r\n
value\r\n
Content-Disposition: form-data; name="file"; filename="fn.jpg"\r\n
Content-Type: image/jpeg\r\n
\r\n
?? JFIF쿺?......쒐%cZt6쐂春찭?溶友\r\n
--myboundary--\r\n
```

코드 예제
---
{% highlight javascript %}
const rp = require('request-promise');
const fs = require('fs');

async function upload(options) {

	let options = {
		url: 'https://localhost/upload/',
		method: 'POST',
		formData : {
			key: 'value',
			file: focreateReadStream
		},

	/*	formData 를 쓰면 header 는 알아서 만들어 준다.
		headers: {
			'Content-Type': 'multipart/form-data; boundary=ㅡmybound',
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

formData 로 body 를 만드는 과정
---

* request/request.js
	* requestForm = new FormData() // form-data
	* requestForm.append
		* append(formKey, formValue)
		* append(formKey, formValue.value, formValue.options)
		* for(...) append(formKey, formValue[i]) // formValue[i].value, formValue[i].options

* form-data/lib/form_data.js
	* if (typeof options == 'string') options = { filename: options };
	* append(_multiPartHeader(field, value, options))
		* --myboundary--\r\n
		* Content-Disposition: form-data; name="key";// filename="fn"
			* `filename="${filename}"`	
				1. path.normalize(options.filepath).replace( /\\/g, '/');
				2. ```path.basename(options.filename || value.name || value.path);```
				3. path.basename(value.client._httpMessage.path) // if value.readable && value.hasOwnProperty('httpVersion')
		* // Content-Type: ...
			1. options.contentType
			2. mime.lookup(value.name)
			3. mime.lookup(value.path)
			4. value.headers['content-type'] // if value.readable && value.hasOwnProperty('httpVersion')
			5. ```mime.lookup(options.filepath || options.filename)```
			6. application/octet-stream // if value is object
		* \r\n
	* append(value)
	* append(_multiPartFooter())
		* \r\n or \r\n--myboundary--\r\n

* 코드를 보면 formData 를 대략 이런 식으로 만들어서 넣어주면 되는 듯 하다.
* 사실은 아님 ㅋ
{% highlight javascript %}
formData = {
	...

	file: {
		value: {
			name: 'sample.png',
			path: 'a/b/c/sample.png',
			readable: true,
			httpVersion: '???',
			headers: { 'content-type': 'image/png', },
			client: {
				_httpMessage: {
					path: ''
				},
			},
		},
		options: {
			filepath: 'a/b/c/sample.png',
			filename: 'sample.png',
			contentType: 'image/png',
			header: 'string'
		}
	}
};
{% endhighlight %}




실제 수행 결과
---
* contentDisposition 이나 contentType 을 만들때 value.name, value.path 등을 참조하려고 하지만 의미가 없다.
* value 가 object 인 경우 에러가 남

{% highlight javascript %}
formData: {
	// 단순 key - value: string
	key1: ['key1 has value string',

	// value: Array
	key11: ['key11', 'has', 'value', 'array'],

	/*
	// 실제로 해보면 에러가 난다.
	// source.on is not a function
	// value 는 string, Buffer 등만 되는 듯?
	key2: { data: 'key2 has value object' },

	key3: {
		name: 'filename3.png',
		data: 'key3 has value object with name'
	},
	*/

	// options: string
	key4: {
		value: 'key4 has value { value: string, options: string }',
		options: 'filename4.png', // -> { filename: 'filename4.png' }
	},

	// options: object
	key5: {
		value: 'key5 has value { value: string, options: { filename, contentType } }',
		options: {
			filename: 'filename5.unknown',
			contentType: 'image/png'
		}
	},

	/* 에러
	key6: {
		value: {
			name: 'value6.jpg',
			data: 'key6 has value.name and options.filename'
		},
		options: {
			filename: 'options6.png',
		}
	}
	*/
}
{% endhighlight %}

* 결과
{% highlight javascript %}
--myboundary\r\n
Content-Disposition: form-data; name="key1"\r\n
\r\n
key1 has value string\r\n

--myboundary\r\n
Content-Disposition: form-data; name="key11"\r\n
\r\n
key11\r\n
--myboundary\r\n
Content-Disposition: form-data; name="key11"\r\n
\r\n
has\r\n
--myboundary\r\n
Content-Disposition: form-data; name="key11"\r\n
\r\n
value\r\n
--myboundary\r\n
Content-Disposition: form-data; name="key11"\r\n
\r\n
array\r\n


	/* 에러 : source.on is not a function
	--myboundary\r\n
	Content-Disposition: form-data; name="key2\r\n
	Content-Type: application/octet-stream\r\n
	??? : { data: 'key2 has value object' }\r\n
	--myboundary\r\n
	Content-Disposition: form-data; name="key3"; filename="value3.png"\r\n
	Content-Type: image/png\r\n
	\r\n
	??? : { name: 'filename3.png', data: 'key3 has value object with name' }\r\n
	*/

--myboundary\r\n
Content-Disposition: form-data; name="key4"; filename="filename4.png"\r\n
Content-Type: image/png\r\n
\r\n
key4 has value { value: string, options: string }\r\n

--myboundary\r\n
Content-Disposition: form-data; name="key5"; filename="filename5.unknown"\r\n
Content-Type: image/png\r\n
\r\n
key5 has value { value: string, options: { filename, contentType } }\r\n

	/* 에러 : source.on is not a function
	--myboundary\r\n
	Content-Disposition: form-data; name="key6"; filename="options6.png"\r\n
	Content-Type: image/jpeg\r\n
	\r\n
	??? : { name: 'value6.jpg', data: 'key6 has value.name and options.filename' }\r\n
	*/

--myboundary--\r\n
{% endhighlight %}
