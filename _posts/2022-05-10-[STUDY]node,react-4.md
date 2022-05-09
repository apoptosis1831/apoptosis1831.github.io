---
layout: post
title: "[STUDY] node, 리액트 - 4"
subtitle: auth기능, logout기능 구현
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] node, 리액트 - 4

### auth기능 만들기

`auth기능`이 필요한 이유가 무엇일까? 사이트의 여러가지 페이지 중 로그인된 유저들만 이용가능한 페이지가 있으며, 로그인이 필요없이 사용가능한 페이도 있다. 이를 구분해주기 위해 auth기능이 필요하다.

그렇다면 어떻게 만들까? 우선 `토큰`이 서버와 클라이언트 쪽 두곳 모두에 저장되어 있어야 한다. 우선 `서버의 경우는 DB`에 저장되며 `클라이언트의 경우는 쿠키`에 저장되게 해놓았다. 여기서 이 두개의 토큰이 동일한지 계속 체크를 하는 것이다.

만약 유저가 A페이지에서 B페이지로 이동할때 B로 이동가능한지를 서버에게 쿠키를 전달함으로써 확인한다. 여기서 토큰은 `인코드` 되어 있으므로 `디코드` 해주어야 한다. (디코드때는 이전 포스팅에서의 'secretToken'이라는 문자열이 필요하다)

우선 쿠키에 저장된 토큰을 서버로 가져와 복호화 해준다.

```javascript
    app.get('/api/users/auth', auth, (req,res)=>{
```

index.js에서 `auth` 라우트를 추가해주는데 여기서 매개변수로 `auth`의 이름을 가진 미들웨어를 넣어준다. 그 다음에 middleware폴더를 프로젝트에 추가하여 auth.js파일을 만들어준다. 그리고 이곳에서 인증처리를 할 예정이다.

이 순서는 아래와 같다.

1. 클라이언트 쿠키에서 토큰을 가져온다. 이때 cookie-parser를 이용한다.

```javascript
    let token = req.cookies.x_auth;
```

2. 토큰을 복호화 한 후, 유저를 찾는다. 그리고 이는 User 모델에서 함수를 만들어줘야한다.

```javascript
userSchema.statics.findByToken = function(token, cb){
    var user =this;
    // 토큰을 디코드 한다
    // ‘secretToken’은 토큰 만들때 (user._id + ‘sercretToken’ 조합으로 만들때 사용한 값)
    jwt.verify(token, 'secretToken', function(err,decoded){
        // 유저 아이디 이용해서 유저를 찾은 다음에 클라이언트에서 가져온 token과 db에 보관된 토큰이 일치하는지 확인
        // decoded : user ID
        user.findOne({"_id" : decoded, "token":token}, function(err,user){
            if(err) return cb(err);
            cb(null, user);
        })
    })
}
```

`verify()`함수로 복호화를 실행한다. 이때 `secretToken`은 토큰을 만들때 user의 id와 조합할때 사용한 문자열이다. 콜백함수의 매개변수로 들어온 `decoded`엔 우리가 찾고자 하는 `user의 id`가 들어가게 된다. 이후, decoded와 token으로 DB에서 user를 찾는다.

이렇게 만들어준 함수는 auth.js에서 사용한다.

```javascript
User.findByToken(token, (err,user)=>{
        if(err) throw err;
        if(!user) return res.jsoin({isAuth:false, error:true});
        req.token = token;
        req.user = user;
        next();
    })
```

이때 해당하는 user가 없으면 res로 isAuth : false라는 결과를 반환해주며, user가 있으면 `req`에 token정보와 user정보를 넣어준 후 next()해준다. 여기서 `next()`를 넣어준 이유는 무엇일까? 만약 next()구문이 없다면 미들웨어(index.js에서 사용한 auth)에 갇히게 된다.

index.js에서 anth.js로부터 넘어온 미들웨어인 auth를 이용해 나머지 동작을 수행해준다.

```javascript
app.get('/api/users/auth', auth, (req,res)=>{
        // 여기까지 미들웨어를 통과해 왔다는 얘기는 authentication 이 true라는 말
        res.status(200).json({
            _id : req.user._id,
            isAdmin : req.user.role === 0 ? false : true,
            // role 0 일반유저 / role 0 아니면 관리자
            isAuth : true,
            email: req.user.email,
            name : req.user.name,
            lastname : req.user.lastname,
            role : req.user.role,
            image : req.user.image
        })
    })
```

이때 이 함수가 동작했다는 의미는 auth의 **미들웨어에서 정상값들이 return 되었다**는 뜻이며, 이는 `authentication이 true`라는 말이다.

### 로그아웃 기능 만들기

로그아웃은 DB에서 해당 **user의 token을 지워주는 방식**으로 구현 가능하다.

우선 다른 라우트와 동일하게 index.js에서 해당 라우트를 만들어준다.

```javascript
app.get('/api/users/logout', auth, (req,res)=>{
        User.findOneAndUpdate({ _id: req.user._id},
            {token : ""}
            ,(err,user)=>{
                if(err) return res.json({success : false, err});
                return res.status(200).send({
                    success : true
                })
            })
    })
```

`findOneAndUpdate()`함수는 user를 찾아서 업데이트를 해주는 함수이다. 이때 auth 미들웨어에서 가져온 req정보로 인해 req.user._id 같은 변수들이 사용가능한 것이다.

마지막으로 로그아웃한 결과를 한번 확인해보자.

![postman-logout-send](/assets/images/etc/postman_logout.jpg)

로그인한 상태에서 logout 페이지에서 request를 보낼 경우 DB상에서의 변화는 아래와 같다.

![mongodb-logout-receive](/assets/images/etc/mongodb_logout.jpg)

마지막 항목이 token이 지워진 것을 확인할 수 있다.

---


참조 : [인프런 강의](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%85%B8%EB%93%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EA%B8%B0%EB%B3%B8/dashboard)

