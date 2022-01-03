---
layout: post
title: "[STUDY] 서버 만들기(Node.js)"
subtitle: node.js모듈과 객체, 모듈로 서버 만들기
categories: study
tags: [Node.js]
typora-root-url: ../





---



## [STUDY]-서버 만들기(Node.js)



### Node.js 시작하기

우선 config(설정 파일)를 설정해주어야 한다. 이는 `npm`명령어로 가능하다. 터미널에서 해당 폴더로 이동하여  `npm init` 명령어로 초기화 작업을 해준다. (npm은 node package manager의 약자이다)

이 명령어를 실행하면 package.json파일이 만들어지며 `package name`을 입력하라는 표시가 뜬다. 또한 `entry point`는 실행파일의 진입점 역할을 하는 파일을 지정해주는데, 서버를 실행시킬 파일을 지정해주면 된다. 주로 index.js, app.js, server.js등의 이름을 사용하며 기본값은 index.js이다.

`test command`옵션은 코드를 테스트할 때 입력할 명령어이며 `keyword`는 npm에서 패키지를 찾게 해주는 옵션, `license`는 원하는 라이선스를 지정해주는 옵션이다.



### 모듈 시스템

모듈은 기능 단위로 분리하고 기능을 이루는 코드를 모아서 캡슐화해 놓은 것이다. 애플리케이션을 구성하고 애플리케이션을 이루는 기본 단위가 된다.

모듈은 하나의 .js파일이라고 생각하면 된다. 하나 또는 여러개의 기능별로 .js 파일을 만들어 필요한 곳에 require해서 사용할 수 있다.

Node.js는 모듈을 사용해서 서로 다른 파일의 리소스(변수, 함수, 객체 등)을 주거니 받거니 한다.

```javascript
/* A.js */

const A = 'variable A from A.js';

module.exports = A; // 모듈을 내보낸다
```

```javascript
/* B.js */

const A = require('./A'); // require() : 모듈을 불러온다.

console.log(A + ' in B.js');
```

B.js의 실행 결과는 `variable A from A.js in B.js`가 된다.

다른 모듈에서 사용하려면 module.exports 또는 exports 객체를 통해 정의하고 외부에서 사용하고 싶다는 것을 알려주어야 한다. 그리고 다른 모듈의 기능을 불러올 때 require를 통해 불러온다.

* **require()** : 모듈을 불러온다.

  파일 뿐 아니라 디렉터리를 지정할 수도 있다
  
  ```javascript
  const myModule = require('./module');
  ```
  module/ 디렉터리 하나만 require해주면 내부에 있는 index.js, calc.js, print.js 를 모두 사용할 수 있다.

* **module.exports=프로퍼티 또는 exports.프로퍼티** : 모듈을 내보낸다.

  * module.exports=프로퍼티 : 하나의 값(원시타입, 함수, 객체)을 할당할 수 있다
  * exports.프로퍼티 : 메서드, 프로퍼티를 여러 개 내보낼 수 있다



### 자주 쓰이는 내장 모듈과 객체

Node.js에 포함되어 있는 기본 모듈 중 자주 사용되는 모듈과 객체를 정리해보자.

`require`을 통해 해당 모듈을 불러오고, 모듈을 저장한 변수 뒤에 . 를 붙인 뒤 해당 모듈의 함수를 사용하면 된다.

| 종류 | 메서드 |
| --- | ---- |
| global </br> : 전역 객체 | global.exports : 모듈 내보낼때 사용 </br> global.require : 모듈 불러들일 때 사용|
| process </br> : 프로세스 객체 | process.argv : 실행 파라미터 출력 </br> process.execPath : 실행 파일 경로 </br> process.cwd : 현재 애플리케이션 경로 </br> process.version : node 경로 </br> porcess.memoryUsage : 현재 서버의 메모리 상태 </br> process.env : 여러 환경 설정 정보|
|os</br> : 운영체제 모듈 | os.type, os.platform : 운영체제 확인 </br> os.arch : 서버 아키텍처를 구분 </br> os.networkInterface : 서버의 지역IP를 확인함 |
|fs</br> : 파일시스템 모듈 | fs.readFile : 파일을 읽는다 </br> fs.exists : 파일을 확인한다 </br> fs.writeFile : 파일을 쓴다 |
| utils </br> : 유틸 모듈 | utils.format : 파라미터로 입력한 문자열을 조합한다 |
| url </br> : URL모듈 | url.parse : url을 객체화한다 </br> url.format : url을 직렬화 한다 |



### 정리

Node.js는 모듈 시스템을 기반으로 동작한다. 따라서 우리는 지금까지 모듈이 무엇이고 어떤 종류의 모듈이 있으며 모듈을 어떻게 사용하는지에 대해 알아봤다. 그리고 Node.js는 기본적으로 내장 모듈만을 가지고도 많은 기능으 사용할 수 있으므로 자주 쓰이는 내장 모듈에 대해 살펴보았다. 

**module.exports**와 **require** 어떤 역할인지 중요하다.