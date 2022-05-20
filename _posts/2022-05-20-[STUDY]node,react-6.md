---
layout: post
title: "[STUDY] node, 리액트 - 6"
subtitle: axios, proxy설정, concurrently, redux
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] node, 리액트 - 6

### 데이터 FLOW & axios

데이터의 흐름을 잠깐 살펴보자. 어떤 유저가 로그인을 한다고 하면 ID와 패스워드를 입력하고 로그인 버튼을 누른다. 이때 request가 서버로 전달된다. 서버에선 DB에서 ID가 있는지 찾고 패스워드가 맞는지 체크한다. 이후 클라이언트에게 response를 전달한다.

request를 할때 지금까지는 client부분이 없었기에 Postman을 이용했다.

서버쪽으로 요청을 보낼때 `axios 라이브러리`를 사용하려고 한다. 이는 `jquery를 사용할때의 ajax`와 비슷한 역할이라고 보면된다.

여기서 `ajax`란? **비동기 자바 스크립트와 xml**이다. 서버와 통신하기 위해 XMLHttpRequest 객체를 사용한다. json, xml, html, 일반 텍스트 형식 등 다양한 포맷을 주고 받을 수 있다. 또한 페이지 전체를 리프레쉬 하지 않고서도 수행되는 `비동기성 특징`을 가진다. 비동기성을 통해 사용자의 이벤트가 있으면 전체 페이지가 아닌 일부분만을 업데이트 할 수 있게 해준다. 페이지의 새로고침 없이 서버에 요청하고 서버로부터 데이터를 받고 작업을 수행한다.

axios를 사용하여 `get(데이터 조회), post(데이터 등록), put(데이터 수정), delete(데이터 삭제) 등 메서드로 API 요청`이 가능하다.

우선 client 디렉터리로 이동하여 `npm install axios --save` 명령어로 axios 라이브러리를 다운로드 받아준다. 그 후 landingPage.js에서 아래와 같이 코드를 작성해본다.(오류가 날 예정)

```javascript
import React , {useEffect} from 'react'
import axios from 'axios';

function LandingPage() {

  useEffect(()=>{
    axios.get('/api/hello')
    .then(response => console.log(response.data))
  } , [])


  return (
    <div>LandingPage ????</div>
  )
}

export default LandingPage
```

server 디렉터리 쪽 index.js에서는

```javascript
app.get('/api/hello', (req,res)=>{
    res.send("안녕하세요")
})

```

로 작성하여 실행시키게 되면 server 포트(5000번)와 client 포트(3000번)이 다르기 때문에 오류가 생긴다. 요청을 보내도 서버가 받지를 못하는 상황이 된다. 이때 `axios.get('http://localhost:5000/api/hello')`로 억지로 바꿔도 에러가 해소 되지 않는다.

### CORS 이슈, proxy 설정

두개의 다른 포트를 가지고 있는 서버는 `CORS 정책`에 의해 통신 불가 오류를 가지게 된다. cross-origin resource sharing 의 약자이며 보안을 위해 존재하는 정책이다. CORS 정책은 가져오는 리소스들이 안전한지 검사해주는 역할이다.

이를 해결하기 위한 방법으론 여러가지가 있겠지만 여기선 `Proxy`를 사용함으로써 오류를 해결한다.

client 디렉터리에서 `npm install http-proxy-middleware --save` 를 실행해주고 src/setupProxy.js 파일을 만들어준다.

```javascript
const {createProxyMiddleware} = require('http-proxy-middleware');

module.exports = function(app){
    app.use(
        '/api',
        createProxyMiddleware({
            target : 'http://localhost:5000',
            changeOrigin : true,
        })
    );
}
```

서버의 포트가 5000번 인 것을 확인해서 타겟으로 해당 포트 번호를 설정해준다.

### Proxy Server

여기서 proxy server에 대해서 알아보자.

`proxy server` 는 서버와 클라이언트 사이에 들어갈 수 있다. user가 서버쪽으로 데이터를 보낼 때 proxy server에서 user의 Ip나 데이터를 바꿔서 보내줄 수 있다. 그 결과 서버쪽에선 실제 user ip를 모르게 된다. `방화벽 기능`과 `웹 필터 기능`이 가능하다.

### concurrently 이용해 프론트, 백 서버 한번에 키기

root 디렉터리에서 `npm install concurrently --save` 를 실행해준다.

package.json에서

```json
"dev" : "concurrently \"npm run backend\" \"npm run start --prefix client \" "
```

위의 명령어를 추가 해준다. 이제 `npm run dev` 명령어 하나로 프론트와 백 서버를 둘 다 한번에 킬 수 있다. 또한 이때 package.json에서 기재한 순서대로 켜주게 된다.

### Antd CSS Framework

기능에 초점을 맞추기 위해 CSS Framework 를 사용할 예정이다.

`ant desiagn`이라는 css framework를 사용해준다. 사이즈가 크지만 스타일이 깔끔하고 사용이 용이한 장점이 있다.

client 디렉터리로 이동하여 `npm install antd --save` 실행 시켜준 후, src > index.js에 `import antd/dist/antd.css`를 추가해준다.

### Redux 기초

`Redux`란 `상태 관리 라이브러리`이다. 이때 상태(state)는 component 안에서 관리되는 것이다. 여기서 잠깐 component 간의 정보 공유에 대해 알아보자.

`● component 간의 정보 공유
 - 자식 component 들 간의 다이렉트 데이터 전달은 불가능하다
 - 자식 component 들 간 데이터를 주고 받을땐 상태를 관리하는 부모 component 통해서 가능하다
 - 자식이 많아지면 상태 관리가 매우 복잡해진다
 - 상태 관리하는 상위 컴포넌트에서 계속 내려받아야 한다 => `props drilling 이슈`

 여기서 `상태관리 툴`이 전역상태 저장소를 제공해주며 props drilling 이슈를 해결해줄 수 있다.

 ● props와 state의 비교
 
 - `props` 는 properties를 의미한다. 부모 component와 자식 component가 있을 때 component 간 데이터를 주고 받을 때 porps를 이용한다. 부모로부터 자식으로만 데이터가 이동하며 자식이 받은 props 데이터는 바뀔 수 없다. 바꿔줄려면 부모로부터 데이터를 새로 받아야 한다.
    ```
    <cahtMessages
    Message = {messages}
    currentMember = {member}/>
    ```

- `state` 는 component 안에서 데이터를 교환, 전달할 때 사용한다. mutable 특징을 갖기에 내부에서 데이터 변환이 가능하다. 데이터가 바뀔 때 마다 rerendering 된다.
    ```
    state = {
        message : '',
        attachFile : undefined,
        openMenu : false
    }
    ```

Redux는 `state를 관리하는 것으`로 `redux store`로 편하게 관리가 가능하다. 이때 store는 상태가 관리되는 오직 하나의 공간이다. component에서 상태 정보가 필요할 때 store에 접근한다.

### Redux 데이터 flow

redux는 component에서 시작하고 한방향으로만 흐른다.

![출처 : 구글 이미지 검색](/assets/images/etc/reduxFlow.gif)

`action`은 app에서 store에 운반할 데이터를 의미한다. javascript 의 `객체 형식`으로 되어 있다.

`reducer`는 action을 함으로 인해 원래 데이터가 다른 데이터로 바뀌는 값을 return 해준다. `action을 reducer에 전달`해야 하며 reducer가 주문을 보고 store의 상태를 업데이트 한다. action을 reducer에 전달하기 위해선 `dispatch() 메서드`를 사용해야 한다. reducer는 새로운 store를 생성한다.

### redux 설치, 설정

4개 dependency를 다운로드 받아줘야 한다. client 디렉터리에서 ```npm install redux react-redux redux-promise redux-thunk --save``` 실행해준다.

여기서 `redux-promise`와 `redux-thunk` 2개의 미들웨어 역할은 무엇일까? redux를 잘 쓸 수 있게 도와주는 역할이다. redux Store안에 모든 State를 관리하게 되는데 store의 state를 변경해줄때 `dispatch(action)`을 사용한다. 이때 action은 `객체 형태`여야 한다. store에서 항상 객체 형태로 받는 것이 아니라 function이나 promise형태로 action을 받을 수도 있다. 이때 thunk는 dispatch에게 어떻게 function 형태를 받아서 처리하는지를 알려주고, promise는 promise 형태를 처리하게 도와준다.

srr > index.js 에서 app에 redux를 연결시켜 줘야 한다. provider를 이용해서 가능하다. ```import {Provider} from 'react-redux';```

```javascript
const createStoreWithMiidleware = applyMiddleware(promiseMiddleware, ReduxThunk) (createStore)

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <Provider
  store = {createStoreWithMiidleware(Reducer,
    window.__REDUX_DEVTOOLS_EXTENSION__ &&
    window.__REDUX_DEVTOOLS_EXTENSION__()
    )}
  >
    <App />
  </Provider>
);
```

원래는 store를 redux에서 생성하는 건데 store는 객체밖에 못받기 때문에 function, promise 도 받을 수 있도록 미들웨어와 함께 만들어준다.

extension도 넣어준다. 구글에서 redux extension 툴을 다운로드 받아준다.

_reducers > index.js 파일을 생성한다.

```javascript
import { combineReducers } from "redux";
//import user from './user_reducer'

const rootReducer = combineReducers({
    //user,
    //comment,
    //...
})

export default rootReducer;
```

store 안에 여러가지 reducer들이 존재 가능하다. 이를 `CombineReducers`를 이용해서 **rootReducer**에서 하나로 합쳐준다. 인증과 관련된 user reducer를 만들어주고 comment reducer도 만들어주고 하면 combineReducers 함수 안에서 이들을 합쳐준다.


---

참조

- [인프런 강의](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%85%B8%EB%93%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EA%B8%B0%EB%B3%B8/dashboard)

- [react redux 개념 참조 블로그](https://hanamon.kr/redux%EB%9E%80-%EB%A6%AC%EB%8D%95%EC%8A%A4-%EC%83%81%ED%83%9C-%EA%B4%80%EB%A6%AC-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC/)

- [axios 개념 참조 블로그](https://velog.io/@mgk8609/React%EC%97%90%EC%84%9C-Axios-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)

- [ajax 개념 참조-mozilla.org](https://developer.mozilla.org/ko/docs/Web/Guide/AJAX/Getting_Started)