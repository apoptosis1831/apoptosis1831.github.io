---
layout: post
title: "[STUDY] node, 리액트 - 1"
subtitle: 
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] node, 리액트 - 1

### npm init

`npm init` 명령어를 통해 **package.json** 파일을 생성할 수 있다. 아래와 같은 내용을 가진 파일이며 npm을 통해 설치된 패키지 목록을 관리하고 프로젝트 정보와 기타 실행 스크립트를 작성하는 파일이다.

```json
{
  "name": "boiler-plate",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "backend" : "nodemon index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "kwonJH",
  "license": "ISC",
  "dependencies": {
    "body-parser": "^1.20.0",
    "express": "^4.18.0",
    "mongoose": "^6.3.1"
  },
  "devDependencies": {
    "nodemon": "^2.0.16"
  }
}
```

### npm install express --save

서버를 만들 수 있게 도와주는 라이브러리인 `express`를 설치해준다. 여기서 --save 가 있어야 package.json에 등록이 된다.

프로젝트 안에 **node_modules**라는 폴더가 하나 생긴 것을 확인할 수 있다.

### index.js 만들기

backend 시작점이라고 할 수 있는 **index.js** 파일을 만들어준다.

```javascript
const express = require('express') // express 모듈 가져온다
const app = express() // 새로운 express app을 만든다
const port = 5000 // 백서버 포트 번호

app.get('/',(req,res)=>res.send('안녕!')) // 루트 디렉터리에 오면 hello world 출력 

app.listen(port, ()=>console.log(`Example app listening on port ${port}!`)) // app이 port에 listen을 하면 console.log 출력
```

> `npm run start` 명령어로 해당 코드를 실행해줄 수 있다.


#### Model 과 Schema

- `Model` : Schema를 감싸주는 역할을 한다

- `Schema` : 정보들의 기준을 지정해준다. 만약 스키마에 어긋나는 데이터가 있으면 에러를 발생시킨다.

models 폴더를 하나 생성하여 User.js 파일을 만들어주어 User 스키마를 저장해준다.

```javascript
const mongoose = require('mongoose');

const userSchema = mongoose.Schema({
    name : {
        type : String,
        maxlength : 50
    },
    email: {
        type : String,
        trim : true,
        unique : 1
    },
    password : {
        type : String,
        minlength : 5
    },
    lastname : {
        type: String,
        maxlenth :50
    },
    role : {
        type : Number,
        default : 0
    },
    image : String,
    token : {
        type : String
    },
    tokenExp: {
        type : Number
    }
})

const User = mongoose.model('User',userSchema)

module.exports = {User};
```

> `trim` : 데이터 사이사이의 빈칸을 없애주는 역할을 한다
>
> `mongoose.model(모델 이름, 위에서 만들어준 스키마)` 형식으로 모델 생성해줄 수 있다.



### GIT과 GIT HUB

* `GIT` : 분산 버전 관리 시스템으로 여러 명이 하나의 프로젝트를 진행할때 각자의 업무들을 관리해주는 역할을 한다. 쉽게 말해 소스코드를 관리할 수 있는 TOOL 이다.

* `GIT HUB` : 클라우드 서비스로 git으로 관리하는 코드들을 git hub에 올릴 수 있다. git을 사용하는 서비스이다.

`git --version` : git 버전 확인

`git init` : git 저장소를 해당 디렉터리에 만들어준다

`git status` : git의 상태를 확인할 수 있다

`git add` : working directory 에서 staging area(git repository에 가기 전에 대기하는 공간)로 이동시켜준다.

`git commit -m "커밋 메세지"` : staging area에서 git repository-LOCAL 로 올려준다

`git push` : 최종적으로 git repository-REMOTE 로 데이터를 올려준다. 여기서 LOCAL과 git hub서버가 안전하게 통신할 수 있게 ssh 설정이 필요하다.

> 이때 **node_modules**는 git에 올리지 않아도 된다. 왜냐하면 `npm install` 명령어로 다운로드가 바로 가능하기 때문이다.
>
> `.gitignore` 파일을 만들어줘서 node_modules 내용을 추가해주면 해당 내용은 add되지 않게 된다.
>
> 여기서 만약 .gitignore을 파일을 만들어주기 전에 `git add` 를 했다면 `git rm --cached node_modules -r` 명령어로 add 해준 정보를 모두 지운 후 다시 `git add` 해준다.

### 회원가입 (body-parser, postman 사용)

서버와 클라이언트 통신하는 방법을 간단히 보기 위해서 `body-parser`이라는 dependency가 필요하다. node.js의 모듈이며 클라이언트의 post request 데이터의 body로부터 파라메터를 편리하게 추출한다.

`npm install body-parser --save` 명령어를 통해 간단하게 설치 가능하다.

여기서 부가적으로 `postman` 도 설치를 해주는데 이는 로그인을 시행할때 client가 현재 없으므로 사용하는 것이다.

```javascript
const express = require('express') // express 모듈 가져온다
const app = express() // 새로운 express app을 만든다
const port = 5000 // 백서버 포트 번호
const bodyParser = require('body-parser');

const {User} = require("./models/User")

// application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({extended : true}));

// application/json
app.use(bodyParser.json());

const mongoose = require('mongoose')
mongoose.connect('mongodb+srv://kjh:abcd1234@movieappproject.jrw7u.mongodb.net/myFirstDatabase?retryWrites=true&w=majority', {
    useNewUrlParser:true, useUnifiedTopology:true
}).then(()=>console.log('MongoDB Connected...'))
.catch(err => console.log(err))

app.get('/',(req,res)=>res.send('안녕! ????')) // 루트 디렉터리에 오면 hello world 출력 

app.post('/register', (req,res) => {
    // 회원가입할때 필요한 정보들을 client에서 가져오면
    // 그것들을 데이터베이스에 넣어준다
    
    const user = new User(req.body)

    user.save((err,userInfo)=>{
        if(err) return res.json({success : false, err})
        return res.status(200).json({
            success:true
        })
    })
})

app.listen(port, ()=>console.log(`Example app listening on port ${port}!`)) // app이 port에 listen을 하면 console.log 출력
```

> `user.save()` : mongoDB의 method이며 정보들이 user모델에 저장이 되는 명령어이다. 만약 저장할때 에러가 있으면 client 쪽에 json형태로 에러를 전달하고, 에러가 없으면 res.status(200)을 return 해준다. 이것의 결과는 아래와 같다

![post-man](/assets/images/etc/post-man-result-1.jpg)


### nodemon

nodemon은 소스의 변화를 감지해서 변화된 부분을 반영해주는 도구이다.

`npm install nodemon --save-dev` 를 통해 간단히 설치가 가능하다. 참고려 여기서 save뒤에 dev가 붙은 이유는 development 모드라는 의미이다. 이는 local에서 프로그램을 가동할때만 사용하겠다는 뜻이며 package.json에서도 devDependencies가 따로 생성되는 것을 확인할 수 있다.

시작할때 nodemon으로 기동 시켜주기 위해서 package.json에서 scripts에 `"backend" : "nodemon index.js"`를 추가 해주고 서버를 실행시킬때 `npm run backend` 명령어를 실행해 nodemon으로 서버를 켜준다.





---

참조

- [인프런 강의](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%85%B8%EB%93%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EA%B8%B0%EB%B3%B8/dashboard)