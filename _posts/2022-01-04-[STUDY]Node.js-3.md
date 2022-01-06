---
layout: post
title: "[STUDY] 서버 만들기(Node.js)-3"
subtitle: express모듈 사용해 서버 만들기
categories: study
tags: [Node.js]
typora-root-url: ../





---



## [STUDY] 서버 만들기(Node.js)-3



### express 모듈을 사용해 서버 만들기-1

`express`는 프레임워크로 Request와 response를 완전하게 통제할 수 있고 가볍고, 빠르고, 무료라는 장점이 있다. npm을 통해 아주 간단하게 설치할 수 있다. Node.js의 http와 connect 컴포넌트를 기반으로 동작하는데, 여기서 connect 컴포넌트를 `미들웨어`라고 한다.

```javascript
const express = require('express');
const app =express()

app.get('/', (req,res)=>{
    res.send('Hello world');
});

app.listen(8080,()=>
console.log('8080포트에서 서버 실행중'));
```

#### 코드 해석

- `send()` 메서드 : http 모듈의 res 객체 메서드 write() 대신 문자열 데이터 전달한다. 이 함수 하나로 응답을 보내고, 종료하는 기능까지 가능하다.
- express는 기존 http 모듈을 상속받았기 때문에 http 모듈의 기능도 모두 사용가능하다.



### express 모듈을 사용해 서버 만들기-2

* index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>express로 웹 만들기</title>
</head>
<body>
    <h2>express로 웹 만들기</h2>
    <p>메인 페이지 입니다.</p>
</body>
</html>
```

* express_study2.js

```javascript
const express= require('express');

const app = express();
app.set('port',process.env.PORT || 8080);

app.get('/',(req,res)=>{
    res.sendFile(__dirname+'/index.html');
});

app.listen(app.get('port'),()=>{
    console.log(app.get('port'),'번 포트에서 서버 실행중..');
});
```

#### 코드 해석

- html 파일을 보낼 때 fs 모듈에서의 .readFile() 대신 **sendFile()** 사용가능하다.
- http 모듈 사용해서 응답 보낼때, writeHeader를 통해 Content-Type과 Charset정보까지 보내줘야 했지만 express의 **res객체의 sendFile메서드**를 사용하면 자동으로 자질구레한 정보를 클라이언트에 보내준다.
- 요청 주소를 여러 개 더 추가할 때 http모듈에서는 if else를 사용해서 요청주소를 분리해줘야 했지만 express를 사용하면 **app.get**등의 메서드로 깔끔하게 주소를 분리할 수 있다.
- **app.set('port',포트번호)**을 통해 서버가 실행될 포트를 지정한다. (process.env.PORT는 process.env객체에 기본 포트번호가 있다면 해당포트를 사용한다는 뜻.)
- **app.get(키)** 함수로 데이터를 가져온다. app.get(주소, 라우터)은 주소에 대한 GET요청이 올 때 어떤 응답을 할지 적어준다. (여기서는 index.html파일을 불러오라고 해주었다.)
- **app.listen()**을 통해 포트를 연결하고 서버를 실행한다.



### HTTP 요청 메서드

http 요청 메서드는 클라이언트(사용자)가 웹 서버에게 자신의 목적을 알리는 수단을 의미한다.

- **GET** : 리소스를 얻을 때 사용한다
- **HEAD** : 문서의 정보를 얻을 때 사용한다
- **POST** : 리소스를 전송할 때 사용한다
- **PUT** : 내용전체를 갱신할 때 사용한다
- **PATCH** : 내용을 부분적으로 갱신할 때 사용한다
- **DELETE** : 파일을 삭제할 때 사용한다

