---
layout: post
title: "[STUDY] node, 리액트 - 3"
subtitle: 로그인 기능 구현, 토큰 생성
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] node, 리액트 - 3

### 로그인 기능 구현

로그인 기능을 구현하기 위해선 크게 3가지 단계를 거쳐야 한다.

1. 요청된 이메일을 DB에서 찾는다.
2. 이메일이 DB에 있다면 비밀번호가 맞는지 확인한다.
3. 비밀번호도 맞다면 user를 위한 token을 생성한다.

우선 index.js에서 만들어준 '/register' 일때 post해주는 함수 밑에 '/login'을 추가해주어 로그인 기능을 하는 함수를 만들어준다

/login 기능의 app.post 에게 요청된 email 주소를 `User.findOne`(mongoDB에서 제공하는 method) 함수 이용하여 디비에 email 주소가 있는지 찾는다.

```javascript
app.post('/login', (req,res)=>{
    // 요청된 이메일을 데이터베이스에서 있는지 찾는다
    User.findOne({ email : req.body.email}, (err,user)=>{
        if(!user){
            return res.json({
                loginSucess : false,
                message : '제공된 이메일에 해당하는 유저가 없다'
            })
        }
// 중략
```

만약에 user 정보가 없다면 null이 반환된다. 그리고 이때 loginScess: false와 message를 띄운다.

요청된 이메일을 찾아다면 이제는 비밀번호를 비교해줘야 한다. User.js에서 패스워드를 비교해주는 `comparePassword`라는 함수를 구현해준다. 이때 이 함수명은 index.js에서 사용할 함수명과 동일해야 한다.

```javascript
userSchema.methods.comparePassword = function(plainPassword, cb){
    // plainPassword 1234567    암호화된 비밀번호 : wkjlqvjlwijvoqjifewjo머시기저시기
    bcrypt.compare(plainPassword, this.password, function(err, isMatch){
        if(err) return cb(err)
        cb(null, isMatch)
    })
}
```

comparePassword 함수의 첫번째 매개변수인 plainPassword 는 유저가 입력한 패스워드이다. `bcrypt.compare`함수로 plainPassword와 this.password(암호화된 패스워드)를 비교해준다. 이때 두개가 동일하다면 isMatch로 true를 반환해준다.

```javascript
user.comparePassword(req.body.password, (err,isMatch)=>{
            if(!isMatch)
            return res.json({loginSucess: false, message : "비밀번호가 틀렸습니다"})
```

index.js에서 isMatch가 false일때 user 정보가 없었을때 처럼 동작하게 구현해준다.

### jsonwebtoken으로 토큰 생성하기

비밀번호가 맞다면 이제 토큰을 생성해줘야 한다. 토큰 생성을 위해 우선 `npm install jsonwebtoken --save` 로 라이브러리를 다운 받는다.

User.js에서 `generateToken`함수를 구현해주자.

```javascript
userSchema.methods.generateToken = function(cb){
    var user = this;
    // jsonwebtoken 이용해서 토큰 생성하기
    var token = jwt.sign(user._id.toHexString(), 'secretToken')
    this.token = token
    user.save(function(err,user){
        if(err) return cb(err)
        cb(null, user)
    })
}
```

`sign함수`의 첫번째 매개변수인 `user._id`는 mongoDB에서 확인할 수 있는 값이다. (아래 몽고디비 결과 사진 참조) 두번째 매개변수는 `임의의 값`이며 이 둘을 함쳐 토큰을 생성해준다. 토큰을 해설할때 'secretToken'값을 넣으면 user._id 가 나온다. 이렇게 만들어준 토큰을 userSchema의 token필드에 넣어준다.

cb(null, user) 에서 `user`가 index.js의 generateToken 함수의 매개변수인 `user`로 데이터가 넘어가게 된다. 

### cookie-parser로 토큰을 쿠키에다 저장하기

```javascript
// 비밀번호 맞다면 토근을 생성하기
user.generateToken((err,user)=>{
    if(err) return res.status(400).send(err);
    
    //토큰을 저장한다. 어디에? 쿠키, 로컬스토리지
    res.cookie("x_auth", user.token)
    .status(200)
    .json({loginSucess: true, userId : user._id
})
```

이후 index.js로 넘어온 user의 토큰을 쿠키에 저장해줄 수 있는데 이를 진행하기 위해선 cookie-parser 가 필요하다. `npm install cookie-parser --save`로 간단하게 다운로드 받고 `res.cookie()` 를 사용하여 "x_auth"의 이름을 가진 쿠키를 저장한다.

모든 작업이 끝났다면 서버를 동작시킨 후 postman을 이용하여 서버에게 올바른 이메일과 올바른 패스워드를 보내보자.

![postman-send](/assets/images/etc/post_man_token_storage.jpg)

loginSucess 가 true로 떴으며 userId가 출력되는걸 보니 잘 동작했다는걸 육안으로 확인할 수 있다.

![mongoDB-recieve](/assets/images/etc/mongodb_token_storage.jpg)

mongoDB에서도 토큰까지 잘 저장된 것을 확인할 수 있다.

---

참조 : [인프런 강의](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%85%B8%EB%93%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EA%B8%B0%EB%B3%B8/dashboard)