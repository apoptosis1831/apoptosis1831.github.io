---
layout: post
title: "[STUDY] node, 리액트 - 2"
subtitle: 
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] node, 리액트 - 2

### 코드 안에 비밀정보 설정

코드 안에 적혀있는 비밀 정보를 타인에게 안 보이게 설정해 주는 작업이 필요할 수 있다. 현재 내 코드에선 index.js에서 mongoDB 를 접속하는 부분에 `mongodb+srv://[아이디]:[비밀번호]]@movieappproject.jrw7u.mongodb.net/myFirstDatabase?retryWrites=true&w=majority'` 이렇게 보안이 필요한 정보가 들어있다. 이를 숨겨주기 위해서 아래와 같은 작업을 해준다.

우선 config 폴더를 생성하여 `dev.js` 생성해준다.

```javascript
module.exports = {
    mongoURI:'mongodb+srv://[아이디]:[패스워드]@movieappproject.jrw7u.mongodb.net/myFirstDatabase?retryWrites=true&w=majority'
}
```

여기서 알고 넘어가야할 점은 개발할때 두가지 환경이 있다는 것이다.

1. LOCAL환경 (development 모드) : 말 그대로 개발할때 local에서 작업하는 환경
2. DEPLOY환경 (production 모드) : 배포한 후에 작업할 때 환경. Deploy한 이후에는 dev.js 정보는 가져오지 못한다. 그러므로 heroku 같은 웹사이트에서 정보를 가져오게 해야한다.

위의 2번에 해당할 `pord.js`도 생성한다.

```javascript
module.exprots={
    mongoURI : process.env.MONGO_URI
}
```

이때 `MONGO_URI` 는 웹사이트의 Config vars 와 동일하게 해주어야 한다.

모드가 달라질때 마다 module.exports 를 다르게 해줄 `key.js`도 생성해준다.

```javascript
if(process.env.NODE_ENV==='production'){
    module.exports = require('./prod');
} else{
    module.exports = require('./dev');
}
```

`process.env.NODE_ENV`는 production 모드일때 'production'으로 나오며, development 모드일때 'development'로 나온다.

그리고 마지막 `index.js`도 변경해준다.

```javascript
const config = require(‘./config/key');
// 중략
const mongoose = require('mongoose')
mongoose.connect(config.mongoURI, {
    useNewUrlParser:true, useUnifiedTopology:true
}).then(()=>console.log('MongoDB Connected...'))
.catch(err => console.log(err))
```

최종적으로 github에도 해당 정보를 감춰주기 위해 .gitignore 파일에 dev.js를 추가해준다.



### bcrypt로 비밀번호 암호화하기

저번 포스팅에서 진행했던 Postman으로 user 정보를 mongoDB로 전달하기에서 넘어온 db의 user 정보들(비밀번호 포함)이 모두 암호화 되지 않고 그대로 노출되는 것을 확인할 수 있다.

![postman](/assets/images/etc/postman1.jpg "postman 캡쳐화면")

![mongoDB](/assets/images/etc/mongoDB_beforeBcrypt.jpg "mongoDB 화면")

이를 해결하기 위해 bcrypt를 사용해보자.

`npm install bcrypt --save` : bcrypt 다운로드

index.js에서 user.save()전에 user의 패스워드를 암호화 해주어야 한다. 이를 진행하기 위해 User.js에서 `userSchema.pre()` 를 사용해준다. pre() 메서드는 mongoose에서 가져온 것이며 user정보를 저장하기 전에 먼저 동작하는 함수이다.

```javascript
const bcrypt = require('bcrypt');
const saltRounds = 10;

//(중략)

userSchema.pre('save', function (next) { // 새로 저장할때 마다 작동
    var user = this;

    if (user.isModified('password')) { //password 가 변경될때만 시행
        // 비밀번호를 암호화 시킨다.
        bcrypt.genSalt(saltRounds, function (err, salt) {
            if (err) return next(err)
            bcrypt.hash(user.password, salt, function (err, hash) {
                if (err) return next(err)
                user.password = hash;
                next()
            })
        })
    }

})
```

![mongoDB](/assets/images/etc/mongoDB_result.jpg "암호화 후 mongoDB 화면")


---

참조

- [인프런 강의](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%85%B8%EB%93%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EA%B8%B0%EB%B3%B8/dashboard)