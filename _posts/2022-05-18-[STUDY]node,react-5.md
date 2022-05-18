---
layout: post
title: "[STUDY] node, 리액트 - 5"
subtitle: react 시작하기
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] node, 리액트 - 5

### react란?

react는 프레임워크가 아닌 `라이브러리`이다. 컴포넌트화로 `재사용성`이 뛰어나며 `virtual DOM` 이라는 특징 또한 갖고있다. 또한 react는 babel과 webpack이라는 기능도 가지고 있는데 여기서 `babel`이란 최신 자바스크립트 문법을 지원하지 않는 브라우저들을 위해 최신 자바스크립트 문법으로 변환 시켜주는 기능이다. javascript ES5로 변환 시켜준다. `webpack`이란 많은 프레임워크 사용으로 복잡해진 모듈들을 webpack을 이용해 묶어주는(bundle) 역할이다. **src 폴더** 안에서 관리를 해주므로 이미지 같은 파일은 src에 넣어줘야 webpack의 관리를 받을 수 있다.

기존 만들어둔 코드들(config, middleware, models, index.js) 는 server라는 폴더를 만들어서 모두 넣어준다. 별개로 client 폴더를 만들어줘서 client 디렉터리에서 `npx create-react-app .` 명령어를 쳐준다. 명렁어가 실행되면 client 폴더 안에 여러 폴더와 파일들이 자동으로 생기게 된다.

### npm 과 npx

`npm`은 레지스트리 라는 저장소 역할을 한다. 라이브러리들을 담고있는 레지스트리 역할을 하는데 package.js에 정의 되어 있다.

이때 -g 플래그를 주면 global로 다운로드를 받는 것이기 때문에 프로젝트 안에서 다운로드 받아지는게 아니라 /usr/local/bin과 같은 폴더(컴퓨터)에 저장된다.

`npx`는 node 레지스트리에 있는 것을 가져다 쓸 수 있는 명령어이다. global로 해서 컴퓨터에 다운로드 받는게 아니라서 disk space를 낭비하지 않을 수 있다. npm registry에서 create-react-app을 찾아서 다운로드 없이 실행시켜 주기 때문에 항상 최신 버전을 사용할 수 있다.

### 구조 설명

- `App.js` : 렌더링 돼서 웹페이지 화면으로 나오는 실제 부분, 라우팅 관련 일 처리를 해준다
- `index.js` : App 컴포넌트가 import되어 들어가 있는 부분
- `imdex.html` : div id = "root"를 통해 index.js에서 'root' id로 잡은 다음에 app 컴포넌트와 연결해준다.
- `_actions`, `_reducer` : redux를 위한 폴더
- `components/views` : page들 들어가는 폴더
- `components/views/sections` : 해당 page에 관련된 css파일이나 component들 넣어주는 폴더
- `config.js` : 환경변수 같은 것들 정하는 곳
- `hoc` : higher order component의 약자로 컴포넌트 안에 function이다. 다음 액션을 취할 수 있게 도와주는 역할을 한다
- `Utils` : 여러 군데에서 쓰일 수 있는 것들을 이곳에 넣어둬서 어디든지 쓸 수 있게 해준다

> rfce 로 기존 functional component 를 자동으로 작성할 수 있다.

### React router dom

페이지 간 이동할 때 react router dom 을 사용해준다. 먼저 react-router-dom을 다운로드 받아야 한다. `npm install react-router-dom --save`

다운로드 받은 react-router-dom은 App.js에서 아래와 같이 사용 가능하다.

```javascript
import {
  BrowserRouter,
  Routes,
  Route,
  //Link
} from "react-router-dom";

 import LandingPage from './components/views/LandingPage/LandingPage'
 import LoginPage from "./components/views/LoginPage/LoginPage";
 import RegisterPage from "./components/views/RegisterPages/RegisterPage";

function App() {
  return (
    <BrowserRouter>
        <Routes>
          <Route exact path="/" element = {<LandingPage />}/>
          <Route exact path="/login" element = {<LoginPage />}/>
          <Route exact path="/register" element = {<RegisterPage />}/>
        </Routes> 
    </BrowserRouter>
  );
}
export default App;
```

실행 결과는 아래와 같다.

![landingPage](/assets/images/etc/landingPage1.jpg)

![loginPage](/assets/images/etc/loginPage1.jpg)

![registerPage](/assets/images/etc/registerPage1.jpg)

라우팅이 잘 되는 것을 확인 할 수 있다.

---

참조 : [인프런 강의](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%85%B8%EB%93%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EA%B8%B0%EB%B3%B8/dashboard)