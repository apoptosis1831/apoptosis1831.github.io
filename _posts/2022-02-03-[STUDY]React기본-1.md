---
layout: post
title: "[STUDY] React 기본 -1 (react정의,component,create-react-app)"
subtitle: react의 정의, component, create-react-app
categories: study
tags: [react]




---

## [STUDY] React 기본 -1 (react정의,component,create-react-app)

### React란 ? 

react란 사용자 인터페이스를 만들기 위한 라이브러리이다. 프레임워크가 아니며 다른 라이브러리들과 함께 특정한 환경을 렌더링하는 데 사용된다.

react는 javascript의 특징을 이용한다. JS와 다른 부분은 JSX구문을 사용한다는 것이다. (JSX는 JS의 구문을 확정해 HTML과 유사한 코드를 사용할 수 있도록 한다)

```javascript
const heading = <h1>Mozilla Developer Network</h1>;
const header = (
    <header>
    <h1>Mozilla</h1>
    </header>
);
```

위의 코드에서 heading은 JSX표현식으로 알려져있다.

reactjs.org의 공식문서를 확인하면 쉽게 이해가 가능하다.

### Component는 무엇인가

react에서 component는 재사용성, 가독성, 유지보수 부분에 있어서 편의성을 제공해준다. (Bug나 개선사항이 있을때 사용된 component를 변경해줌으로써 동시변경이 가능하다)

props를 입력으로 하고 UI가 어떻게 보여야 하는지 정의하는 React Element를 출력으로 하는 일종의 함수이다.

### react app 간단히 만들어보기

npm을 이용해 create react app을 설치하여 간단하게 react app을 만들어 볼 수 있다.

> `npm install create-react-app` : create-react-app 설치하기
>
> **/vscode/react-app 디렉터리** 만들어준 다음
>
> `cd /vscode/react-app` : 해당 디렉타리로 이동
>
> `create-react-app .` : create-react-app 실행
>
> `npm run start` : react 실행

#### 만들어진 디렉터리 구조

- public > **index.html** : `<body>안에 <div id="root"></div>`

- src > **index.js** : 진입파일 (src폴더 안에 파일들에서 컴포넌트 만들어준다)

`document.getElmentById("root");` 로 index.html과 연결(root라는 id를 통해 연결된다)

`ReactDom.reader(<App/>, document.getElementByID("root"));` : App.js 파일 안에 `<div className="App">` / `import App from '.App'`으로 App 태그 사용할 수 있게 해준다

- **public 폴더** : create-react-app에서 npm run start 했을때 파일을 찾는 document root 디렉터리이다

- **semantic tag** : 웹페이지는 대부분 header, navigator, foot 등으로 나누어져 있고 이걸 나누는 태그를 시맨틱 태그라고 부른다



### Deploy

크롬 개발자모드를 통해 네트워크 상태를 확인할 수 있는데, 이때 개발자모드를 킨 상태에서 '캐시 비우기 및 강력 새로고침'을 통해 캐시를 모두 없애버리고 새로고침 할 수 있다.

create-react-app으로 만든 간단한 app의 크기 확인 결과 1.7MB 정도이다. 이는 react가 자체적으로 기능들을 추가해놓은 상황이어서 크기가 크다고 보면된다.

build할땐 `npm run build` 명령어를 통해 할 수 있는데, 이 명령어를 실행시키면 build 라는 디렉터리가 생긴다. 여기서 index.html을 확인해보면 기존 파일과 비교했을때 공백이나 불필요한 정보가 모두 없어진 것을 확인할 수 있다.

`npx serve -s build` : 웹서버를 실행시킬때 build를 document root로 하겠다(-s) 라는 의미이다.

해당 명령어를 실행한 후 다시 앱의 크기를 확인하면 125KB정도로 크기가 확 줄었다는 것을 확인할 수 있다.







---

참조
- [developer.mozilla.org](https://developer.mozilla.org/ko/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_getting_started)
- [구글링 했던 블로그](https://medium.com/little-big-programming/react%EC%9D%98-%EA%B8%B0%EB%B3%B8-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EB%A5%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-92c923011818)
- [인프런 생활코딩 강의](https://www.inflearn.com/course/react-%EC%83%9D%ED%99%9C%EC%BD%94%EB%94%A9/dashboard)