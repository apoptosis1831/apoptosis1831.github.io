---
layout: post
title: "[STUDY] movie-app-3"
subtitle: making Favorite Button
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] movie-app 만들기 3

### Favorite 버튼 구현 순서

메인 화면에서 보이는 Grid중 하나의 영화 포스터를 클릭하면 나오는 movieDetail 페이지에서 "좋아요" 버튼을 하나 추가해보기로 하자. 이 버튼을 누르면 해당 영화를 좋아하는 사람들의 수가 버튼에 출력된다. 또한 나중에 만들 Favorite 페이지에 사용자가 좋아요를 누른 영화들이 table 형태로 보여지게 된다. 만약 좋아요를 미리 누른 영화일 때 한번 더 버튼을 누르면 좋아요 가 취소되며 숫자도 줄고 Favorite 페이지에서도 삭제된다.

이를 구현하는 순서는 아래와 같다.

1. Favorite Model 만들기 (uerFrom, movieId, movieTitle 등등 유저 및 영화 정보가 들어간 Model)
2. Favorite 버튼 UI 만들기
3. 해당 영화가 몇명의 유저가 좋아요를 눌렀는지 그 숫자 정보 얻기
4. 내가 이 앵화를 이미 favorite 에 넣었는지에 대한 정보 얻기
5. 데이터 화면에 보여주기

### Favorite Model 만들기

기존 User 스키마와 비슷한 형태로 `Favorite 스키마 모델`을 만들어 준다.

server > models > Favorite.js 

```javascript
const { Schema } = require('mongoose');
const mongoose = require('mongoose');

const favoriteSchema = mongoose.Schema({
    userFrom : {
        type : Schema.Types.ObjectId,
        ref : 'User'
    },
    movieId : {
        type : String
    },
    movieTitle : {
        type : String
    },
    moviePost : {
        type : String
    },
    movieRunTime : {
        type : String
    }
},{ timestamps : true})

const Favorite = mongoose.model('Favorite', favoriteSchema);

module.exports = { Favorite }
```

필요한 필드를 넣어준다. 여기서 `ref : 'User'` 는 User 모델의 데이터를 모두 가져오는 것을 의미한다.

`timestamps : true` 는 생성된 시간을 자동으로 처리해주는 부분이다.

### Favorite 버튼 만들어주기

Favorite 버튼 컴포넌트를 다른 파일로 따로 만들어 준 후에 MovieDetail 에서 import 하여 이를 사용하는 형태로 코드를 작성한다.

Sections > Favorite.js 파일을 새로 생성한다. useEffect 에서 mongoDB에 있는 데이터를 얻어오는 코드가 들어가야 하며 이는 서버쪽에 요청을 보내는 것을 의미한다. 이때 fetch 나 Axios 둘 다 사용 가능하다. (아래 코드에선 Axios 사용)

```javascript
const movieId = props.movieId;
    const userFrom = props.userFrom;
    let movieTitle ;
    let moviePost ;
    let movieRunTime;
    if(props.movieInfo){
        movieTitle = props.movieInfo.title;
        moviePost = props.movieInfo.backdrop_path;
        movieRunTime = props.movieInfo.runtime;
    }

let variables = {
        userFrom :userFrom,
        movieId : movieId,
        movieTitle : movieTitle,
        moviePost : moviePost,
        movieRunTime : movieRunTime
    } // MovieDetail 에 prop으로 가져와야함

// useEffect() 안에 내용
Axios.post('/api/favorite/favoriteNumber', variables)
        .then(response => {
            setFavoriteNumber(response.data.favoriteNumber)
            if(response.data.success) {
                
            } else{
                alert('숫자 정보를 가져오는데 실패 했습니다.')
            }
        })
```

MovieDetail.js 에서 데이터들을 넘겨주는 것은 아래와 같다.

```javascript
<Favorite movieInfo = {Movie} movieId = {movieId} userFrom = {localStorage.getItem('userId')}/>
```

여기서 `LocalStorage` 는 로그인할 당시 로그인이 성공하면 LoginPage에서 'userId'로 setItem 해준 것이다. console > Application 에서 확인 가능하다. (아래 사진 및 LoginPage.js 코드 참조)

![localStorage](/assets/images/etc/movie-app/LocalStorage.png)

```javascript
if (response.payload.loginSuccess) {
                window.localStorage.setItem('userId', response.payload.userId);
                if (rememberMe === true) {
                  window.localStorage.setItem('rememberMe', values.id);
                } else {
                  localStorage.removeItem('rememberMe');
                }
                props.history.push("/");
              } else {
                setFormErrorMessage('Check out your Account or Password again')
              }
```

이제 서버 부분에서의 처리 과정을 작성해준다.

routes > favorite.js 파일을 새로 생성한다.

```javascript
const {Favorite} = require('../models/Favorite')
// DB 접근을 위해 Favorite 모델 가져온다

router.post('/favoriteNumber', (req, res)=>{

    // mongoDB 에서 favorite 숫자를 가져오기
    Favorite.find({"movieId" : req.body.movieId})
    .exec((err, info)=> {
        if(err) return res.status(400).send(err)
        // 그다음에 프론트에 다시 숫자 정보를 보내주기
        res.status(200).json( {success : true , favoriteNumber : info.length })
        // 성공 여부와 favoriteNumber 를 프론트엔드로 전달
    })    

})
```

이때 post 첫번째 매개변수로 '/api/favorite/favoriteNumber' 형식으로 모든 url 은 필요가 없는데 이는 어떻게 구현해준 것일까?

server > index.js 에서 `app.use(‘/api/favorite’, require(‘./routes/favorite'))` 한 줄을 추가해주면 가능하다. 이때 use함수의 두번째 매개변수인 require의 인자로 라우팅을 원하는 해당 파일 실제 경로를 작성해준다. 이 기능이 가능한 이유는 `router` 함수 때문이다. router 함수는 파일 별로 주소를 나눠준다.

요청 받은 후, `bodyParser`를 이용해 `req.body 형태`로 프론트에서 보내준 정보에 접근한다.

`find({"movieId" : req.body.movieId})` 는 Favorite 모델의 movieId 와 요청받은 req.body.movieId 가 동일한 정보를 찾는 과정이다. 이 정보를 찾았다면 res.status(200).json 으로 프론트엔드에게 `성공 여부`와 `favoriteNumber` 데이터 전달한다. (프론트엔드에서 받은 데이터 아래 사진 참조)

![console-clientReceive](/assets/images/etc/movie-app/Console-clientReceive.png)

### 영화를 favorite 에 넣었는지 정보 얻기

앞의 과정과 동일하게 Sections > Favorite.js 와 server > routes > favorite.js 에 /favorited 로 비슷하게 코드 작성한다.

```javascript
Axios.post('/api/favorite/favorited', variables)
        .then(response => {
            if(response.data.success) {
                setFavorited(response.data.favorited)
            } else{
                alert('정보를 가져오는데 실패 했습니다.')
            }
        })
```

```javascript
router.post('/favorited', (req, res)=>{

    // 내가 이 영화를 favorite 리스트에 넣었는지 정보를 DB에서 가져오기

    // mongoDB 에서 favorite 숫자를 가져오기
    Favorite.find({"movieId" : req.body.movieId, "userFrom": req.body.userFrom})
    .exec((err, info)=> {
        if(err) return res.status(400).send(err)
        
        let result = false;
        if(info.length !== 0) {
            result = true
        }

        res.status(200).json( {success : true , favorited : result })
    })    

})
```

특정 user가 특정 영화를 좋아요 눌렀는지 확인해줘야 하기 때문에 `movieId` 와 `userFrom` 정보가 필요하다. 이때 info.length 가 만약 0이 아니라면 좋아요를 이미 눌렀다는 의미이고 이를 나타내는 형태로 result에 true를 넣는다.

result로 넣어준 bool 값은 프론트엔드에서 받아 Favorited state 에 넣어준다. 이때 Favorited state의 initial value 는 false 이다.

```javascript
<Button onClick = {onClickFavorite}>{Favorited ? "Not Favorite" : "Add to Favorite"} {FavoriteNumber} </Button>
```

좋아요 눌렀는지에 따라 버튼 출력도 다르게 해주는데, 만약 Favorited 가 True 라면 이미 누른 영화므로 "Not Favorited" 출력해주고, false 라면 아직 좋아요를 누르지 않은 영화이므로 "Add to Favorite" 출력해준다.

```javascript
const onClickFavorite = () =>{
        // 좋아요 했던 영화일때
        if(Favorited){
            Axios.post('/api/favorite/removeFromFavorite', variables)
            .then(response=>{
                if(response.data.success){
                    setFavoriteNumber(FavoriteNumber-1)
                    setFavorited(!Favorited);
                } else {
                    alert('Favorite 리스트에서 지우는 걸 실패했습니다')
                }
            })
        } else{ // 좋아요 했던 영화가 아닐때
            Axios.post('/api/favorite/addToFavorite', variables)
            .then(response=>{
                if(response.data.success){
                    setFavoriteNumber(FavoriteNumber+1)
                    setFavorited(!Favorited);
                } else {
                    alert('Favorite 리스트에서 추가하는 걸 실패했습니다')
                }
            })
        }
    }
```

이미 Favorited 가 true 이면 좋아요 한 영화이므로 그 상태에서 버튼 누를 때 삭제하는 기능이 동작해야 한다. 그러므로 removeFromFavorite 를 서버로 요청해야한다. 반대로 false 이면 좋아요 안 한 영화이므로 favorite 에 해당 영화를 추가한다. 이때 addToFavorite 동작한다.

```javascript
// movieDetail 페이지에서 좋아요 누를때 (좋아요 삭제)
router.post('/removeFromFavorite', (req, res)=>{
    Favorite.findOneAndDelete({ movieId: req.body.movieId , userFrom: req.body.userFrom })
    .exec( (err,doc)=> {
        if(err) return res.status(400).send(err)
        res.status(200).json( {success: true, doc})
    })
})

// movieDetail 페이지에서 좋아요 누를때 (좋아요 추가)
router.post('/addToFavorite', (req, res)=>{

    const favorite = new Favorite(req.body)
// 인스턴스 만들어주기

    favorite.save((err, doc)=>{
        if(err) return res.status(400).send(err)
        return res.status(200).json({ success: true })
    })

})
```

`favorite.save` 는 `req.body 에 있는 모든 정보들`이 `doc` 에 들어가게 해준다. `Favorite.findOneAndDelete` 는 어떠한 정보를 지울껀지 {} 안에 작성하게 된다. {} 안에 부합하는 DB정보를 찾아서 삭제해준다.

좋아요 버튼 구현 끝!

![movieDetail-after-favorite](/assets/images/etc/movie-app/movieDetail-after-favorite.png)

---


참조

- [인프런 강의 - 영화 사이트 만들기](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%85%B8%EB%93%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EC%98%81%ED%99%94%EC%82%AC%EC%9D%B4%ED%8A%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0/dashboard)