---
layout: post
title: "[STUDY] node, 리액트 - 7(마지막)"
subtitle: react hooks, login, register, logout, auth
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] node, 리액트 - 7 (마지막)

### react hooks

우선 react 코드를 작성할 때, 두가지 컴포넌트 형태로 작성 가능하다.

* `class component` : 많은 기능이 사용 가능하다. 대신 코드가 좀 더 길어지고 복잡해진다. 성능적으로 느려진다.

* `functional component` : 제공되는 기능이 한정적이지만 코드가 짧아지고 class component보다 빠르다.

우리는 functional component에서도 많은 기능을 쓸 수 있게 해주는 hook을 사용할 것이다. 여기서 `hook`이란 react 버전 16.8부터 react 요소로 추가된 것으로, 기존 class 바탕의 코드를 작성할 필요 없이 상태값과 여러 react기능을 사용할 수 있게 해준다. useEffect, userState 같은 메서드를 사용할 수 있다.

### 로그인 페이지

로그인시 유효성체크를 하나하나 할 때 마다 애니메이션이 들어갈 수 있는데 그것은 formilk와 yup 라이브러리를 사용해야 한다. 우리는 간단하게 이메일, 비밀번호, 로그인 버튼 만으로 로그인을 구현해보려고 한다.

우선 LoginPage.js 에서 리턴되는 부분을 작성해준다.

```javascript
return (
    <div style = {{display:'flex', justifyContent : 'center', alignItems: 'center', width:'100%', height:'100vh'}}>
      <form style ={{display: 'flex', flexDirection: 'column'}}
      onSubmit = {onSubmitHandler}
      >
        <label>Email</label>
        <input type = "email" value={Email} onChange={onEmailHandler}/>
        <label>Password</label>
        <input type = "password" value={Password} onChange={onPasswordHandler}/>

        <br/>
        <button type = "submit">
          login
        </button>
      </form>
      </div>
  )
```

label과 input 으로 Email과 password를 보여준다. button으로 login button도 보여준다. 이때 value로 `state`를 받아줘야 하는데 이는 아래 코드처럼 작성해준다.

```javascript
const [Email, setEmail] = useState("") // 안에서 데이터 변경할때 state필요  , 처음 값은 빈칸으로 "" 지정
const [Password, setPassword] = useState("")
```

내부적으로 데이터가 변경할때 state가 필요하기 때문에 `useState`로 `Email(state)`와 state의 값을 변경해줄 수 있는 `setEmail` 을 만들어준다. value로 state를 넣어주고 프로그램을 실행시키면 input에 타이핑을 해도 값이 보이질 않는데 이는 `onCahnge 이벤트`를 발생시켜서 state를 바꿔주어야 해결 가능하다.

```javascript
  const onEmailHandler = (event) => {
    setEmail(event.currentTarget.value)
  } // setEmail 이용해서 state 바궈준다
  const onPasswordHandler = (event)=>{
    setPassword(event.currentTarget.value)
  }
```

이렇게 state를 바꿔주면 value도 바뀌게 된다.

다음으로 submit 버튼의 기능을 구현해보자. input 2개와 button을 감싸고 있는 form에 `onSubmit 이벤트`를 만들어준다.

```javascript
 const onSubmitHandler = (event)=>{
    event.preventDefault();
    let body = {
      email:Email,
      password:Password
    }
    dispatch(loginUser(body))
    .then(response=>{
      if(response.payload.loginSuccess){
        //props.history.push('/')
        navigate('/')
      } else{
        alert('Error')
      }
    })
  }
```

여기서 `event.preventDefault()` 가 없으면 버튼을 눌러줄 때 마다 페이지가 리프레쉬 된다. 이렇게 되면 submitHandler가 해야할 일들을 못하게 된다.

이때 server에 보내고자 하는 값들은 state에서 갖고있다. 이것을 server쪽으로 보내줘야 한다. 보내줄 때 그냥 바로 axios.post('/api/users/login', body).then(response=>{~~}) 형식으로 보내줄 수도 있지만 우리는 `redux`를 사용해서 이 기능을 구현해본다. **redux는 dispatch를 이용해서 action을 취한다.**

_action 디렉터리에 user_action.js를 만들어서

```javascript
import axios from "axios";
import {
    LOGIN_USER,
    REGISTER_USER,
    AUTH_USER
} from './types'

export function loginUser(dataTosubmit){

    const request = axios.post('/api/users/login',dataTosubmit)
    .then(response=>
        response.data
    )

    return {
        type : LOGIN_USER,
        payload : request
    }
}
```

`axios.post`로 서버쪽으로 request를 날려준다. 이때 서버에서 받은 데이터인 response를 모두 request 에 넣어준다. 이것을 return 해서 `reducer로 해당 데이터를 보내준다.` 이를 받은 reducer에선 `previosState, action을 조합해서 다음 state를 만들어준다`.

user_reducer.js 코드는 아래와 같다.

```javascript
import {
    LOGIN_USER, REGISTER_USER, AUTH_USER
} from '../_actions/types'

export default function (state = {}, action) { // state가 previosState
    switch (action.type) {
        case LOGIN_USER:
            return { ...state, loginSuccess: action.payload }
            break;
        case REGISTER_USER:
            return { ...state, register: action.payload }
            break;
        case AUTH_USER:
            return { ...state, userData: action.payload }
            break;
        default:
            return state;
    }			
}
```

export 해주는 function의 첫번째 매개변수가 previosStated이며 두번째가 action이다. 여기서 action은 **type과 payload(response)**가 들어있다.

user_action에서 Return 된 `payload`는 reducer.js에 `loginSuccess`에 들어가게 된다. 여기서 들어온 action의 type으로 분별해준다. 이때 types.js를 만들어줘서

```javascript
export const LOGIN_USER = 'login_user';
export const REGISTER_USER = 'register_user';
export const AUTH_USER = 'auth_user';
```

모든 타입들을 한 파일 안에서 지정하게 해준다. (문자열의 형태로 case나뉘게 해주는 역할)

이렇게 해준 뒤 DB에 등록돼있는 login으로 로그인을 하면

![react login](/assets/images/etc/react-login.jpg)

![redux dev tool](/assets/images/etc/redux_dev_tool_login.jpg)

redux dev tool에서 위와 같이 loginSuccess에 올바른 값이 들어온 것을 확인할 수 있다. 이때 payload로 들어간 값은 서버쪽 index.js에서 login 라우팅 처리 해주는 부분에서 확인이 가능하다.

```javascript
app.post('/api/users/login', (req,res)=>{
    // 요청된 이메일을 데이터베이스에서 있는지 찾는다
    User.findOne({ email : req.body.email}, (err,user)=>{
        if(!user){
            return res.json({
                loginSuccess : false,
                message : '제공된 이메일에 해당하는 유저가 없다'
            })
        }
        // 요청된 이메일이 데이터베이스에 있다면 비밀번호가 맞는 비밀번호 인지 확인
        user.comparePassword(req.body.password, (err,isMatch)=>{
            if(!isMatch)
            return res.json({loginSuccess: false, message : "비밀번호가 틀렸습니다"})
            
            // 비밀번호 맞다면 토근을 생성하기
            user.generateToken((err,user)=>{
                if(err) return res.status(400).send(err);
                
                //토큰을 저장한다. 어디에? 쿠키, 로컬스토리지
                res.cookie("x_auth", user.token)
                .status(200)
                .json({loginSuccess: true, userId : user._id})
            })
        })
    })
})
```

user_action.js에서 payload : request (서버에서 가져온 모든 데이터) 를 통해 이 데이터를 reducer에 action.payload로 넣어줌으로써 state값으로 loginSuccess 가 보이는 것이다.

### 회원가입 페이지

loginPage처럼 registerPage.js도 아래와 같이 작성해준다.

```javascript
import React, { useState } from 'react'
import {useDispatch} from 'react-redux';
import { useNavigate } from 'react-router-dom';
import { applyMiddleware } from 'redux';
import {registerUser} from '../../../_actions/user_action'
import Auth from '../../../hoc/auth'


function RegisterPage() {


  let navigate = useNavigate();
  const dispatch = useDispatch();

  const [Email, setEmail] = useState("")
  const [Password, setPassword] = useState("")
  const [Name, setName] = useState("")
  const [ConfirmPassword, setConfirmPassword] = useState("")

  const onEmailHandler = (event) => {
    setEmail(event.currentTarget.value)
  }
  const onNameHandler = (event)=>{
    setName(event.currentTarget.value)
  }
  const onPasswordHandler = (event)=>{
    setPassword(event.currentTarget.value)
  }
  const onConfirmPasswordHandler = (event)=>{
    setConfirmPassword(event.currentTarget.value)
  }
  const onSubmitHandler = (event)=>{
    event.preventDefault();

    if(Password !== ConfirmPassword){
      return alert('비밀번호와 비밀번호 확인은 같아야 합니다.');
    }

    let body = {
      email:Email,
      password:Password,
      name : Name
    }


    dispatch(registerUser(body))
    .then(response=>{
      if(response.payload.success){
        navigate('/login')
      } else{
        alert("Failed to sign up");
      }
    })
  }
  return (
    
    <div style = {{display:'flex', justifyContent : 'center', alignItems: 'center', width:'100%', height:'100vh'}}>
      <form style ={{display: 'flex', flexDirection: 'column'}}
      onSubmit = {onSubmitHandler}
      >
        <label>Email</label>
        <input type = "email" value={Email} onChange={onEmailHandler}/>

        <label>Name</label>
        <input type = "text" value={Name} onChange={onNameHandler}/>

        <label>Password</label>
        <input type = "password" value={Password} onChange={onPasswordHandler}/>

        <label>Confirm Password</label>
        <input type = "password" value={ConfirmPassword} onChange={onConfirmPasswordHandler}/>

        <br/>
        <button type = "submit">
          회원가입
        </button>
      </form>
      </div>
  )

}

export default RegisterPage;
```

email, name, password, confirm password, 회원가입 button 형식으로 간단하게 회원가입 페이지를 구현한다. 이때 loginPage처럼 `dispatch 이용하여 action을 날려준다.` action 이름은 `registerUser로` 해준다.

그리고 user_action 에서 registerUser function을 만들어준다.

```javascript
export function registerUser(dataTosubmit){

    const request = axios.post('/api/users/register',dataTosubmit)
    .then(response=>
        response.data
    )

    return {
        type : REGISTER_USER,
        payload : request
    }
}
```

type 부분에 해당하는 **REGISTER_USER** 로 types.js에 정의를 해주고 user_reducer 에도 해당 type case를 작성해준다.

```javascript
case AUTH_USER:
            return { ...state, userData: action.payload }

 dispatch(registerUser(body))
    .then(response=>{
      if(response.payload.success){ // register 성공하면 => loginPage로 보내준다
        navigate('/login')
      } else{
        alert("Failed to sign up");
      }
    })
```

회원가입 성공하면 loginPage로 보내준다.

![react register](/assets/images/etc/react-register.jpg)

### logout 기능

로그인 이후 로그아웃 하는 기능도 만들어준다. 로그아웃을 위한 페이지를 하나 만드는게 아니라 landingPage에 버튼 하나를 만들어주자.

```javascript
// (중략)
const onClickHandler = () =>{
    axios.get('/api/users/logout') 
    .then(response=>{
      if(response.data.success){ // 로그아웃 성공 후 loginPage로 이동
        navigate('/login')
      } else{
        alert('로그아웃 하는데 실패했습니다.');
      }
    })
  }

// (중략)

 <button onClick={onClickHandler}>
        로그아웃
      </button>
```

로그아웃의 경우 복잡한 부분이 아니기에 redux 사용 안하고 landingPage에서 바로 logout을 구현했다. 위의 코드에서 response.data.success 의 데이터는 server쪽 index.js에서 역시나 확인 가능하다.

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
    });
```


### Auth 로 인증 체크

여러 페이지들은 user의 인증 상태에 따라 대략 4개의 페이지 종류로 나눠진다. (아무나 진입이 가능한 페이지 / 로그인한 회원만 진입 가능한 페이지 / 로그인한 회원은 진입 못하는 페이지(로그인, 회원가입 페이지) / 관리자만 진입 가능한 페이지)

그리고 이러한 **페이지들에 대한 통제**는 `HOC`로 구현 가능하다. HOC는 Function이며 다른 컴포넌트를 받은 다음에 새로운 컴포넌트를 리턴하는 기능을 갖는다. auth라는 HOC 이면 이곳에 **모든 component(landingPage, loginPage, RegisterPage) 를 넣어주고** HOC에서 backend 에 request를 날려 현재 들어와있는 `유저의 상태정보(로그인된 유저인지, admin 유저인지)` 를 가지고 온다. 그 상태정보로 hoc에서 다른 곳으로 돌려 보내거나 컴포넌트 접근을 허용하게 해준다.

Hoc디렉터리에 auth.js를 만들어준다.

```javascript
import { Axios } from 'axios';
import React, { useEffect } from 'react';
import { useDispatch } from 'react-redux';
import {auth} from '../_actions/user_action'
import { useNavigate} from 'react-router-dom';


export default function(SpecificComponent, option, adminRoute= null){
    //option 
    // null => 아무나 출입이 가능한 페이지
    // true => 로그인한 유저만 출입 가능 페이지
    // false => 로그인한 유저는 출입 불가능한 페이지
    function AuthenticationCheck(){
        const dispatch = useDispatch();
        const navigate = useNavigate();
        useEffect(()=>{
            dispatch(auth()).then(response=>{
                console.log(response)
                // 로그인 하지 않은 상태
                if(!response.payload.isAuth){
                    if(option){ // 로그인한 유저만 출입 가능 페이지
                        navigate('/login');
                    }
                } else{
                    // 로그인한 상태
                    if(adminRoute && !response.payload.isAdmin){
                        // isAdmin FALSE 일때
                        navigate('/');
                    } else{ // 로그인한 유저가 출입 불가능한 페이지(로그인, Register) 들어가려고 할때
                        if(option===false){
                            navigate('/');
                        }
                    }
                }
            })
        }, [])
        return (
            <SpecificComponent/>
        )
    }
    return <AuthenticationCheck/>
}
```

userAction에도 auth function을 만들어준다.

```javascript
export function auth(){

    const request = axios.get('/api/users/auth')
    .then(response=>
        response.data
    )

    return {
        type : AUTH_USER,
        payload : request
    }
}
```

/api/users/auth의 경우 get 메서드 이기에 userAction에서 axios.get으로 서버에게 request를 보내는 것이다.

user_reducer에도 **AUTH_USER** 케이스 만들어준다.

```javascript
case AUTH_USER:
            return { ...state, userData: action.payload }
            break;
```

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

index.js에서 위와 같이 모든 user 정보들을 client에 전해준다.

마지막으로 만들어준 Auth component에 다른 component를 넣어줘야 한다. App.js에서

```javascript
import Auth from './hoc/auth'

function App() {
  // const NewLandingPage = Auth(LandingPage,null, true)
  // const NewLoginPage = Auth(LoginPage,false)
  // const NewRegisterPage = Auth(RegisterPage, false)
  return (
    
    <BrowserRouter>
        <Routes>
          <Route exact path="/" element = {Auth(LandingPage,null)}/>
          <Route exact path="/login" element = {Auth(LoginPage,false)}/>
          <Route exact path="/register" element = {Auth(RegisterPage,false) }/>
        </Routes> 
    </BrowserRouter>
  
  );
}
```

**모든 컴포넌트들을 Auth로 감싸준다.** 감싸줄때 특정 option을 줄 수 있다. auth.js에서 export되는 function의 두번째 매개변수인 `option`은 아래의 세가지 경우로 상태가 나뉘어진다.

- `null` : 아무나 출입 가능한 페이지
- `true` : 로그인한 유저만 출입 가능한 페이지
- `false` : 로그인한 유저는 출입 불가능한 페이지

만약 admin 유저만 들어가길 원하는 페이지가 있을 때에는 function의 세번째 매개변수인 adminRoute에 true를 준다. (디폴트는 Null이다)

우선 landingPage는 아무나 출입 가능하다. (option 은 null)

loginPage와 registerPage는 로그인한 유저는 출입이 불가능하다. (option 은 false)

페이지는 이동할 때 마다 auth.js 에서 dispatch를 작동해서 계속 서버에 request를 준다. 그에 해당하는 response 값에 따라 hoc 처리하게 된다. 다시 auth.js 코드를 보자.

```javascript
export default function(SpecificComponent, option, adminRoute= null){
    //option 
    // null => 아무나 출입이 가능한 페이지
    // true => 로그인한 유저만 출입 가능 페이지
    // false => 로그인한 유저는 출입 불가능한 페이지
    function AuthenticationCheck(){

        const dispatch = useDispatch();
        const navigate = useNavigate();

        useEffect(()=>{
            
            dispatch(auth()).then(response=>{
                console.log(response)

                // 로그인 하지 않은 상태
                if(!response.payload.isAuth){
                    if(option){ // 로그인한 유저만 출입 가능 페이지
                        navigate('/login');
                    }
                } else{
                    // 로그인한 상태
                    if(adminRoute && !response.payload.isAdmin){
                        // isAdmin FALSE 일때
                        navigate('/');
                    } else{ // 로그인한 유저가 출입 불가능한 페이지(로그인, Register) 들어가려고 할때
                        if(option===false){
                            navigate('/');
                        }
                    }
                }
            })


        }, [])

        return (
            <SpecificComponent/>
        )
    }

    return <AuthenticationCheck/>
}
```

console.log(response) 로 `response`를 확인해 보면 _id, isAdmin, isAuth, email 등등 모든 정보가 출력되는 것을 확인할 수 있다.

![auth-user-console](/assets/images/etc/auth_user_console.jpg)

여기서 받은 정보인 `isAuth로 분기처리`를 해준다. 로그인을 하지 않은 상태에서 로그인한 사람만 들어가는 페이지에 접속하면 loginPage로 이동하게 해준다. 로그인한 상태에서 adminroute가 true이고 유저가 admin이 아닐 때 landingPage로 이동하게 해준다. 로그인한 유저가 로그인 유저는 출입불가한 페이지에 접속하려 하면 landingPage로 이동하게 해준다.

### 후기

react-router-dom 버전이 6으로 최근에 바뀌게 되면서 강의와 다른 부분이 두세개 정도 있었다. react는 공식 문서나 참조할 수 있는 블로그 글들이 많아 해결이 쉬웠던 것 같다. 나중에라도 사람들이 잘 사용하지 않은 언어로 개발을 하게 될때 잘 해결할 수 있을 정도로 실력을 키워야 겠다는 생각이 들었다.

또한 마지막에 클라이언트 auth를 구현하는데 자꾸 /api/users/auth 페이지의 404에러가 떠서 오타 찾는다고 고생했다. 한 3-4시간 정도 구글링하고 인프런 강의의 질문을 샅샅이 뒤졌는데 404에러 대부분이 라우팅 url이나 get, post 를 잘못적는 경우였다. 하지만 나의 경우, 그 부분에선 전혀 실수가 없었다. 서버쪽 routing 해주는 index.js에서 login 라우팅 부분 안에 auth 라우팅이 들어간 채로 코드를 작성해서 auth가 실행이 안된 것이었다. 😭 (나는 멍청이다) 들여쓰기를 잘 봤어야 됐는데, 그거 하나 때문에 4시간을 허비했다. (눈물 좔좔)

지금은 정말 아무런 서비스를 제공하지 못하는 웹을 만드는데에만 대충 2주가 걸렸지만, 더 열심히 공부해서 실질적인 서비스를 제공하는 웹을 기깔나게 만들어보고 싶다.

그리고 보완할 수 있는 부분(landingPage에서 로그인된 user에게만 logout버튼 보여주기, 로그인 안된 user에게 login 버튼 보여주기 등) 은 바로 수정을 할 예정이다.




---

참조

- [Hook의 개념](https://ko.reactjs.org/docs/hooks-intro.html)

- [인프런 강의](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%85%B8%EB%93%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EA%B8%B0%EB%B3%B8/dashboard)

