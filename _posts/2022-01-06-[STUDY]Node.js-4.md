---
layout: post
title: "[STUDY] express와 미들웨어(Node.js)-4"
subtitle: express와 미들웨어
categories: study
tags: [Node.js]
typora-root-url: ../





---



## [STUDY] express와 미들웨어(Node.js)-4



### 미들웨어란 ?

미들웨어는 요청과 응답 사이에 express자체에 있는 기능 외에 추가적인 기능을 제공한다. 미들웨어는 `app.use()` 메서드를 통해 사용한다. **app.set()**과의 차이점은 app.set()은 전역으로 사용된다는 것이다.



```javascript
const express = require('express');
const app = express();

app.get('/', function(req,res){
    res.send('Hello World!');
});

const myLogger = function(req, res){
    console.log('LOGGED');
};

app.use(myLogger);

app.listen(8080);
```

위 코드를 실형 시키면 접속하는 횟수만큼 콘솔 화면에 'LOGGED'라는 메시지가 떠야하는데 뜨지 않는다. 그 이유는?

app.get('/')이 수행되고 res.sned()가 끝나고 응답을 종료해버리기 때문에 myLogger까지 도달하지 않는다.

```javascript
const express = require('express');
const app = express();

app.get('/', function(req,res,next){
    res.send('Hello World!');
    next();
});

const myLogger = function(req, res, next){
    console.log('LOGGED');
    next();
};

app.use(myLogger);

app.listen(8080);
```

위의 코드와 같이 next()를 넣어주면 LOGGED가 잘 출력되는 것을 확인할 수 있다. next()는 다음 미들웨어로 넘어가는 역할을 하기 때문에 순서를 잘 배치를 해주고 next()를 통해 흐름을 잘 제어해주어야 한다.

`next()`는 다음 미들웨어로 가는 역할을 하지만 몇가지 인자를 넣어 다른 기능을 하게 할 수도 있다.

- next() : 다음 미들웨어로 가는 역할을 한다
- next(error) : 오류 처리 미들웨어로 가는 역할을 한다.
- next('route') : 많이 사용하지는 않지만 next()로 같은 라우터에서 분기처리를 할 때 사용한다.




```javascript
const express = require('express');
const app = express();

app.use(function(err,req,res,next){
    console.error(err.stack);
    res.status(500).send('Something broke');
});

app.listen(3000);
```

> 오류 처리를 위한 미들웨어 함수는 총 4개의 파라미터 `err, req, res, next` 를 가진다.

보통 express로 서버를 만들 때 다음과 같은 순서로 구조를 짜게된다.

> 1. express를 불러온다.
>
>       `const express = require('express');`
>
>       `const app = express();`
>
> 2. 포트를 설정해준다.
>
>       `app.set('port', process.env.PORT || 3000);`
>
> 3. 공통적으로 사용하는 미들웨어를 장착해준다.
>
>       `app.use(~...)` // 공통 미들웨어 morgan, cookie-parser, express.json, express.urlencoded, session 등
>
> 4. 라우터를 구성한다.
>
>       `app.get(~...)`
>
> 5. 404처리 미들웨어를 구성한다.
>
>       `app.get((req,res,next)=> res.status(404)...)` // 404미들웨어
// res.status상태 코드의 400 500번대를 너무 자세히 보여주면 해킹의 위협이 있다.
>
> 6. 오류 처리 미들웨어를 구성한다.
>
>       `app.use((err,req,res,next) => ...)` // 오류 처리 미들웨어
>
> 7. 생성된 서버가 포트를 리스닝한다.
>
>       `app.listen(app.get('port'));`




### 자주 사용하는 미들웨어

#### express.static

css, js 등 static 파일은 static끼리 모아 따로 폴더를 지정해 놓는 것이 좋다.

> **static파일 (정적 파일)**: 이미지, css, 스크립트 파일과 같이 그 내용이 고정되어 있어 응답을 할 때 별도의 처리없이 파일 내용 그대로를 보여주면 되는 파일

```javascript
const express = require('express');

const app = express();
app.set('port', process.env.PORT || 8080);

app.use(express.static(__dirname+'/public'));

app.get('/',(req, res)=>{
    res.sendFile(__dirname + '/index2.html');
});

app.listen(app.get('port'),() => {
    console.log(app.get('port'), '번 포트에서 서버 실행중..');
});
```

static은 express안에 기본적으로 포함되어 있기 때문에 별도의 설치없이 꺼내서 사용만 해주면 된다.

static() 안에 static폴더로 지정해줄 파일의 경로를 입력한다. (위의 코드에선 public이라는 폴더를 하나 생성해서 static폴더로 지정해 주었다.)

static폴더를 따로 지정해주면 지정한 파일이 바로 클라이언트로 가는 것이 아닌 static 미들웨어를 거친 후 도착하게 된다. 이렇게 지정했으면 이제부터 static파일들을 불러 html에 띄울 수 있다.

* index2.html

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
    <p>메인 페이지입니다.</p>
    <img src = "./sample.png" />
</body>
</html>
```

웹 페이지에 띄우고 싶은 이미지 파일을 public폴더에 저장한 후 index2.html에 이미지를 추가한다.

html파일 내에 public/ 이라는 경로를 따로 명시해주지 않아도 자동으로 서버에서 static폴더에서 해당 파일을 찾은 후 띄어주게 된다. 경로를 다 볼 수 없으니 보안에도 도움이 된다.

#### router

routere도 일종의 미들웨어이며, 클라이언트로부터 요청이 왔을 때 서버에서 어떤 응답을 보내주어야 할지 결정해준다.

express에서 router는 'app.get', 'app.post', 'app.use' 등 처럼 express 모듈을 담은 변수 뒤에 http 메서드가 붙은 것인데 이를 이용해 요청이 들어오는 경로와 응답을 만들 수 있다.

>app.use('/경로', 미들웨어);
>
>app.get('/경로', 미들웨어);
>
>app.post('/경로', 미들웨어);
>
>app.put('/경로', 미들웨어);
>
>app.delete('/경로', 미들웨어);

첫번째 파라미터로 넣어준 경로로 요청이 들어오면 두번째 파라미터의 미들웨어를 실행한다. 첫번째 파라미터의 경로는 서버 자원을 가리키는 URI 문자열인데 회원가입 경로이면 /join.html처럼 만들어준다. 로그인 경로면 /login.html처럼 만들어주면 된다. 두번째 파라미터는 라우팅 로직 함수를 콜백 형태로 구현한 것이고 해당 주소의 요청을 받으면 미들웨어가 어떤 작업을 수행하게 된다.

app.get("/user/:id", function(req,res){
    res.send("user id: " + req.params.id);
});

위와 같은 코드가 있고 하나의 클라이언트에서 GET 메서드로 /user/1 이라는 URI요청이 들어왔다고 생각해보자. 이 요청에 대해 응답으로 req.params.id에 접근하여 얻은 고객의 아이디를 문자열로 보내게 된다.

```javascript
const express = require('express');

const app = express();
app.set('port', process.env.PORT || 8080);

app.use(express.static(__dirname+'/public'));

app.get('/',(req,res)=>{
    const output = `
    <h2>express로 웹 만들기</h2><br>
    <p>메인 페이지 입니다.</p><br>
    <img src="./sample.png" width="400px" height="200px"/>
    `
    res.send(output);
});

app.get('/user/:id', (req,res)=>{
    res.send(req.params.id+ "님의 개인 페이지 입니다.");
});

app.listen(app.get('port'),() =>{
    console.log(app.get('port'), '번 포트에서 서버 실행중...')
});
```

위의 코드는 localhost:8080/ 에 접속할 때와 localhost:8080/user/roadbook으로 접속했을 때 다른 html화면을 띄어준다.

> **응답을 위한 함수**
>
> app.get, app.use는 요청을 위한 함수이고 응답을 위한 함수도 몇가지 있다.
>
> * res.send() : 문자열로 응답한다
>
> * res.json() : json객체로 응답한다
>
> * res.render() : Jade. Pug와 같은 템플릿을 렌더링하여 응답한다
>
> * res.sendFile() :파일로 응답한다



### express.json 과 express.urlencoded

클라이언트에서 post, put 요청 시 들어온 정보를 가진 req.body에 접근하기 위해 필요한 미들웨어이다. 요청 정보가 url에 들어온 것이 아니라 request body에 들어 있는데, 이 값을 읽을 수 있는 구문으로 파싱하고 req.body로 옮겨주는 역할을 하는 것이 express.json 과 express.urlencoded이다.

> app.use(express.json());
>
> app.use(express.urlencoded({extended : true}));

**express.json**은 req.body가 json형태일 때, **express.urlencoded**는 폼에 대한 요청일 때 사용한다.

**express.urlencoded**의 extended옵션은 **false**로 설정하면 node.js에 내장된 **queryString**을 사용하고, **true**일땐 **npm의 qs모듈**을 사용한다.

둘 다 url 쿼리 스트링을 파싱해주지만 qs는 보안이 추가적으로 필요할 때 사용한다.



### cookie-parser

클라이언트가 요청을 보낼 때 마다 키-쌍(name=gildong)으로 이루어진 쿠키를 보내고 서버에서는 클라이언트가 보낸 쿠키를 읽어 사용자가 누군지 식별하게 된다.

`res.writeHead({'Set-Cookie': 'name=gildong'});`

처음에 한 번만 서버에서 res.writeHead() 메서드를 통해 'Set-Cookie'에 값을 넣어준다. 그러면 브라우저에 키-쌍으로 이루어진 쿠키가 헤더에 저장된다. 이렇게 한번 설정되고 난 후에는 브라우저에서 자동으로 쿠키를 매번 요청할 때 마다 서버에게 보낸다.



```javascript
const http= require('http');

http.createServer((req,res)=>{
    res.writeHead(200, {'Set-cookie':'name=roadbook'});
    console.log(req.headers.cookie);
    res.end('Cookie --> Header');
})
.listen(8080,() => {
    console.log('8080포트에서 서버 연결중..');
});
```

![image](/assets/images/etc/cookie.jpg)

위에 캡쳐처럼 클라이언트에게 Response Header로 해당 쿠키가 잘 전달된 것을 확인할 수 있다.

마찬가지로 요청 헤더에도 브라우저를 끄기 전까지 요청을 보낼 때마다 해당 쿠키 값도 같이 보내주게 된다. 브라우저를 끄기 전까지만 쿠키가 살아있는 이유는 쿠키의 expire를 지정해주지 않았기 때문에 기본 값으로 브라우저를 끄게 되면 쿠키값이 없어진다. 이렇게 쿠키 값을 통해 쿠키가 있을 때는 사용자 관련 화면을, 없을 때는 로그인 화면을 보내주는 식으로 로그인을 구현할 수 있게 된다.

클라이언트가 쿠키와 함께 요청을 보내면 **req.headers.cookie**를 통해 쿠키 값에 접근을 할 수 있게 된다. req.headers.cookie에 저장된 값은 **문자열**인데, 이를 자바스크립트에서 사용하기 위해서는 객체로 파싱하는 과정이 필요하다. Cookie-parser없이 파싱하려면 따로 복잡한 함수를 만들어주어야 한다.

Cookie-parser를 사용하면 따로 파싱하는 함수를 만들어줄 필요 없이 express의 req 객체에 cookies속성이 부여되므로 **res.cookies.쿠키명**을 통해 쿠키 값에 접근을 할 수 있게 된다.





### session

실제 정보는 서버에만 저장해두고 브라우저에는 암호화된 키 값만 보내고 그 키 값으로 실제 값에 접근할 수 있도록 하는 것을 **세션**이라고 한다.

```javascript
const http = require('http');

const session = {};
const sessKey = new Date();
session[sessKey] = {name: 'roadbook'};

http.createServer((req,res) => {
    res.writeHead(200, {'Set-cookie': 'session=${sessKey}'});
    res.end('Session-Cookie --> Header');
})
.listen(8080, () => {
    console.log('8080포트에서 서버 연결중 ..');
});
```

세션 저장용 객체 하나를 session이라는 변수에 생성하고, 키 값을 sessKey라는 변수에 생성한다. (여기서는 날짜로 해주었는데, express-session 미들ㅇ웨어를 이용하면 임의의 키 값을 생성할 수 있게 된다.)

session객체에 sessKey라는 키 값을 지정해주고 값을 {name='roadbook'}으로 할당한다. 쿠키값을 바로 보내주는 것이 아니라 세션 값으로 생성한 키 값을 넣어준다.

그렇게 되면 쿠키값이 바로 뜨는 게 아니라 **세션 값**이 보여지게 된다. express-session이 없다면 cookies.session값을 이용해서 세션을 다루어야 하지만 **express-session**을 이용한다면 req객체에 session 속성이 부여되어 **req.session**을 통해 접근할 수 있게 된다.




### 미들웨어 통합 테스트

`npm install morgan cookie-parser express-session`

명령어를 통해 필요한 모듈들을 한 번에 설치할 수 있다.

```javascript
const express = require('express');
const morgan = require('morgan');
const cookieParser = require('cookie-parser');
const session = require('express-session');
const app = express();

/* 포트 설정 */
app.set('port', process.env.PORT || 8080);

/* 공통 미들웨어 */
app.use(express.static(__dirname + '/public'));
app.use(morgan('dev'));
app.use(cookieParser('secret@1234')); // 암호화된 쿠키를 사용하기 위한 임의의 문자 전송
app.use(session({
    secret: 'secret@1234', // 암호화
    resave: false, // 새로운 요청시 세션에 변동 사항이 없어도 다시 저장할지 설정
    saveUninitialized: true, // 세션에 저장할 내용이 없어도 저장할지 설정
    cookie: { // 세션 쿠키 옵션 들 설정 httpOnly, expires, domain, path, secure, sameSite
        httpOnly: true, // 로그인 구현시 필수 적용, javascript로 접근 할 수 없게 하는 기능
    },
    // name: 'connect.sid' // 세션 쿠키의 Name지정 default가 connect.sid
}));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

/* 라우팅 설정 */
app.get('/', (req, res) => {
    if (req.session.name) {
        const output = `  
                <h2>로그인한 사용자님</h2><br>  
                <p>${req.session.name}님 안녕하세요.</p><br>  
            `
        res.send(output);
    } else {
        const output = `  
                <h2>로그인하지 않은 사용자입니다.</h2><br>  
                <p>로그인 해주세요.</p><br>  
            `
        res.send(output);
    }
});

app.get('/login', (req, res) => { // 실제 구현시 post
    console.log(req.session);
    // 쿠키를 사용할 경우 쿠키에 값 세팅 
    // res.cookie(name, value, options)
    // 세션 쿠키를 사용할 경우
    req.session.name = '로드북';
    res.end('Login Ok')
});

app.get('/logout', (req,res) =>{
    res.clearCookie('connect.sid'); // 세션 쿠키 삭제
    res.end('Logout OK');
});

/* 서버와 포트 연결.. */
app.listen(app.get('port'), ()=>{
    console.log(app.get('port'), '번 포트에서 서버 실행중..')
});
```

![console화면](/assets/images/etc/console.jpg)

위 코드 실행하면 위에 이미지처럼 콘솔에 로그가 찍힌다. 이는 morgan을 장착해주었기 때문에 요청 메서드, url, 상태코드, 응답 속도 등의 로그가 표시된 것이다.

/login 페이지로 접속 시 쿠키 세션을 사용해 req.session.name이라는 값을 넣어주었다. 브라우저를 닫을 때까지 req.session.name 값은 유효하다.

안전하게 쿠키를 전송하기 위해 쿠키를 서명해야 하기 때문에 secret 값이 필요하다. cookie-parser의 인자 값과 쿠키 세션의 값을 동일하게 설정해주어야 한다.

express-session을 사용하지 않고 세션을 구현하면 따로 `const session = {};` 과 같이 세션 정보를 저장할 공간을 따로 마련해주어야 한다. 하지만 express-session을 사용해서 **req.session**을 가지게 되면, req.session은 **방금 요청을 보낸 사람의 고유한 저장 공간** 같은 곳으로 사용할 수 있다. `req.session.name='로드북'` 이란 값을 넣어주었다면 요청을 보낸 사람의 req.session.name만 '로드북' 이라는 값을 가지게 된다.

![first화면](/assets/images/etc/first.jpg)

> localhost:8080 으로 처음 접속했을 때 화면



![login_ok화면](/assets/images/etc/login_ok.jpg)

> localhost:8080/login 으로 접속했을 때 화면



![login화면](/assets/images/etc/login.jpg)

> 로그인 url 접속 후 localhost:8080/ 으로 접속했을 때 화면



![logout화면](/assets/images/etc/logout_ok.jpg)

> localhost:8080/logout 페이지 접속 화면



localhost:8080/ 페이지로 접속했을 때 req.session.name값이 있으면 (/login페이지로 먼저 접속해 req.session.name 값이 생성되어 있을 경우) "로그인한 사용자님" 이라는 제목을 출력하고,

req.session.name 값이 없다면 "로그인하지 않은 사용자입니다"라는 제목을 출력한다.

위의 코드에서 사용된 미들웨어(morgan, cookie-parser 등) 에서는 next()가 필요 없다. 이들은 모두 next()가 내부에 저장되어 있기 때문에 자동으로 다음 미들웨어로 넘어간다. 단 static의 경우 next()가 없기 때문에 static을 거쳐야 하는 router라면 공통 미들웨어의 순서를 잘 설정해주어야 한다.



### 정리

1. Node.js는 **모듈 시스템**을 사용한다.
2. 모듈은 부품과 같은 역할을 하고 여러 모듈을 조립해서 하나의 앱을 만들 수 있다.
3. 모듈을 내보낼 때는 `exports`, 불러올 땐 `require`
4. 모듈의 종류에는 기본 모듈, 확장 모듈, 일반 모듈, 네이티브 모듈, 지역 모듈, 전역 모듈이 있다.
5. Node.js는 global객체를 통해 전역 객체를 사용하고, exports, require, module 등은 global 객체에 포함되어 있다.
6. 기본 모듈에서 자주 쓰이는 모듈에는 global, process, os, utils, fs, url 등이 있다.
7. http 모듈을 사용해서 request 와 require을 구현할 수 있다.
8. `http.createServer()`로 서버를 만드는데 필요한 인자로는 `request`, `response`, `콜백 함수`가 있다.
9. `express 모듈`은 http와 fs 모듈을 사용하여 구현을 더 간단하게 만들어주는 Node.js의 백엔드 프레임워크다.
10. express 모듈은 **connect컴포넌트** 기반으로 동작하며 connect 컴포넌트는 **미들웨어**를 뜻한다. 미들웨어는 기본적인 서비스 외에 다른 부가적인 서비스를 이용할 수 있게 해주는 소프트웨어다.
11. **http 요청 메서드**에는 **GET, POST, PUT, PATCH, DELETE** 등이 있다.
12. express에서 미들웨어는 `app.use()` 함수를 통해 사용한다.
13. **static 파일**이란 정적 파일을 의미하며 css, script, 이미지 등과 같이 내용이 고정되어 응답할 때 별도의 처리가 피료 없는 파일을 말한다.
14. express에서 **라우터**는 일종의 미들웨어이고 요청 uri에 따라 어떤 응답을 보내주어야 하는지 결정한다.
15. 자주 사용하는 미들웨어로는 **static, morgan, cookie-parser, express-session** 등이 있다.



참조 : Node.js로 서버 만들기 (저자 박민경)