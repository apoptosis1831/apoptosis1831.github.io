---
layout: post
title: "[STUDY] 서버 만들기(Node.js)-2"
subtitle: http 모듈로 서버 만들기
categories: study
tags: [Node.js]
typora-root-url: ../





---



## [STUDY] 서버 만들기(Node.js)-2



### http 모듈로 서버 만들기-1

가상 클라이언트가 웹 페이지를 요청했다고 가정하고 보여줄 응답을 넣어준다.

```javascript
const http = require('http');

http.createServer((req, res) =>{
    res.writeHead(200, {'Content-Type': 'text/html; charset=utf-8'});
    res.write('<h1>Node.js로 서버 만들기</h1>');
    res.end('<p3>3장 http모듈 공부중입니다.</p>')
})
.listen(8080, () =>{
    console.log('8080포트에서 서버 연결중 ..');
});
```

#### 코드 해석

- `.createServer()` : 말 그대로 서버를 만드는 함수. 이 함수 인자로 **콜백함수**를 넣고 이 콜백함수에는 요청에 대한 응답, 즉 어떤 이벤트를 받았을 때 실행해야 하는 작업을 작성해주면 된다. 이 콜백 함수의 **파라미터**에 두 가지 객체를 넣는데, 하나는 요청에 관한 정보를 담는 **req**, 다른 하나는 응답에 관한 정보를 담고 **res**라고 표기한다.

- `res.wirteHead()` : 응답에 대한 정보(헤더)를 기록하는 함수. **파라미터**로 **요청코드(여기서는 성공을 알리는 200)** 와 **콘텐츠의 타입**을 넣어준다.

- `res.write()` : **파라미터**로 **클라이언트에 보낼 데이터**를 넣어준다.

- `res.end()` : 응답을 종료하는 메서드. 여기에 넣은 파라미터까지 전달하고 응답을 종료한다.

- `.listen()` : createServer()함수 뒤에 .listen()을 붙여 **클라이언트와 연결할 포트번호**와 **서버가 연결되면 실행할 콜백함수**를 넣는다.

#### 실행화면

![simple_server2_실행화면](/assets/imgaes/etc/simple_server2_exe.jpg)

### http 모듈로 서버 만들기-2

```javascript
const http = require('http');

const server = http.createServer((req,res)=>{
    res.writeHead(200, {'Content-Type': 'text/html; charset=utf-8'});
    res.write('<h1>Node.js로 서버 만들기</h1>');
    res.end('<p>3장 http 모듈 공부중입니다.</p>')
})
.listen(8080);


server.on('listening', ()=>{
    console.error(error);
});
```

#### 코드 해석

- listen() 메서드에 콜백을 넣는 대신 `listening 이벤트 리스너`를 붙여 사용할 수도 있고, `오류를 핸들링해주는 이벤트 리스너`를 붙여줄 수도 있다.

- 오류 처리를 할 때 주의할 점은 오류가 발생해도 꼭 응답 콜백 함수를 작성해주어야 한다. 만약 오류가 발생한 후 실행할 응답이 없다면 서버는 응답이 오길 계속 기다리게 되고 결국에는 timeout 오류를 발생한다.

- `res.end()` 함수는 요청에 대한 응답을 보내주는 함수인데 비슷한 것으로는 `res.sendFile`, `res.end`, `res.json`이 있다.

### http 모듈로 서버 만들기-3

* fs_test.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>Node.js로 서버 만들기</h1>
    <p>3장 http 모듈 공부중입니다.</p>
</body>
</html>
```

* fs_test.js

```javascript
const http=require('http');
const fs=require('fs').promises;

http.createServer(async (req,res)=>{
    try{
        const f = await fs.readFile('/Users/kwonjh/VSCODE/roadbook-nodejs/chapter03/sample/fs_test.html');
        res.writeHead(200, {'Content-Type': 'text/html; charset=utf-8'});
        //200이면 요청 성공
        res.end(f); //요청 종료
    } catch(err){
        console.error(err);
        res.writeHead(500,{'Content-Type':'text/html; charset=utf-8'});
        //500이면 서버에 오류 발생
        res.end(err.messeage); // 오류메시지와 함께 요청 종료
    }
})
.listen(8080, ()=>{
    console.log('8080포트에서 서버 연결중..')
});
```

응답 콜백에 html요소를 직접 넣어주는 것이 아니라 html 파일을 따로 만들어 파일시스템(fs) 모듈로 읽어 전송한다.

**readFile()**의 파라미터는 순서대로 파일위치, 텍스트 타입, 콜백함수이다.

### 요청객체(req), 응답객체(res)

| 종류 | 메서드 | 설명 |
| --- | --- | --------------- |
| req | req.body | POST 방식으로 들어오는 요청 정보 파라미터를 가진다|
|  | req.query | GET 방식으로 들어오는 요청 쿼리 스트링 파라미터를 가진다|
|  | req.params | 개발자가 붙인 라우터 파라미터 정보를 가진다|
|  | req.headers | HTTP 헤더 정보를 가진다|
|  | req.route | 현재 라우트에 대한 정보를 가진다|
|  | req.cookies | 클라이언트가 전달할 쿠키값을 가진다|
|  | req.ip | 클라이언트의 ip 주소 값을 가진다|
|  | req.path | 클라이언트가 요청한 경로를 가진다 |
|  | req.host | 요청 호스트 이름을 반환한다 |
|  | req.xhr Ajax | 요청 호스트 이름을 반환한다 |
|  | req.protocol | 현재 요청의 프로토콜(http, https등)
|  | req.secure | 현재 요청이 보안된 요청이면 true 반환 |
|  | req.url | url경로와 쿼리스트링 반환|
| res | res.send | 클라이언트에게 응답을 보낸다 |
|  | res.sendFile | 인자로 넣은 경로의 파일을 클라이언트에 전송한다 |
|  | res.json | 클라이언트에게 json형태의 응답을 보낸다 |
|  | res.render | 템플릿 엔진을 사용하여 뷰를 렌더링한다 |
|  | res.locals | 뷰를 렌더링하는 기본 문맥을 포함한다 |
|  | res.end | 인자로 넣은 응답을 마지막으로 보내고 응답을 종료한다 |
|  | res.status | HTTP응답 토드를 설정한다 |
|  | res.set | 응답 헤더를 설정한다 |
|  | res.cookie | 클라이언트에 저장될 쿠키를 설정한다 |
|  | res.redirect | 인자로 넣은 URL으로 redirect한다 (기본 응답값 = 302)|
|  | res.type | 헤더의 Content-Type을 설정한다 |

### REST를 통한 페이지 생성

```javascript
const http = require('http');

http.createServer((req,res) => {
    if(req.url === '/') {
        res.write('Hello');
        res.end();
    }
})
.listen(8080, () => {
    console.log('8080포트에서 서버 연결')
});
```

- `req.url`이 / 라는 뜻은 localhost:8080 뒤의 주소가 /라는 뜻이다. 즉, 기본 페이지라는 뜻이다. 여러 url을 설정해주고 req.url이 기본 페이지일 때 index.html을 띄우고, req.url이 회원가입 페이지일 때 singUp.html을 띄우는 식으로 req.url을 이용하여 여러 페이지를 생성할 수 있게 된다.

- 이렇게 요청을 보낼 때, 주소를 통해 내용을 표시하는 것을 **REST(REpresentational State Transfer)** 라고 한다.

- 예를 들어 내가 만든 웹 도메인 주소가 www.myServer.com/ 이라고 해보자. www.myServer.com/user로 접속하면 사용자 정보에 관련된 자원을 요청하는 것이라고 파악할 수 있을 것이다. www.myServer.com/post라고 하면 무언가 게시하는 자원을 요청하는 것이라고 파악할 수 있다.

- 이렇게 뒤에 붙는 주소에 따라 요청을 정의할 수 있도록 주소체계를 구조화하여 만든 웹서버를 **RESTful하다** 라고 한다. 이는 라우팅을 통해 구현할 수 있는데, express모듈을 이용해서 서버 만드는 법을 배울때 알아본다.





참조 : Node.js로 서버 만들기 (저자 박민경)
