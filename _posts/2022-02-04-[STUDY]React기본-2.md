---
layout: post
title: "[STUDY] React 기본 -2 (component,props,state)"
subtitle: component, pops, state
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] React 기본 -2 (component,props,state)

### component 만들기

src디렉터리 > App.js 수정하여 `Subject`라는 컴포넌트를 만들어보자

```javascript
class Subject extends Component{
    render(){
        return(
            <header>
                <h1>WEB</h1>
                world wide web!!
            </header>
        );
    }
}
```

return() 안에 html 코드를 작성할 수 있으며 컴포넌트는 반드시 하나의 최상위 태그로 시작해야한다. (위의 코드에선 `<header>`가 최상위 태그 역할을 한다)

```javascript
class App extends Component {
    render(){
        return(
            <div className="App">
            <Subject></Subject>
            </div>
        );
    }
}
```

만들어준 Subject 컴포넌트는 위의 코드처럼 사용할 수 있다. 여기서 주의해야할 점은

```javascript
import React,{Component} from 'react';
```

이를 App.js 상단에 작성해줘야 component를 만들어줄 수 있다는 것이다.

![first-main](/assets/images/etc/first-main.jpg)

실행결과는 위와 같다.

react가 코드 처리 이후 최종적으로 웹브라우저에 띄우는 것은 html코드이며 `<Subject>`부분은 웹브라우저에서는 보이지 않는다.

![크롬 개발자 모드 캡쳐](/assets/images/etc/web-developer-mode.jpg)


### props

props는 react에서 데이터를 사용할때 다루는 개념이다. props는 부모 컴포넌트가 자식 컴포넌트에 값을 전달할 때 사용하는 것이며 읽기 전용이다.

`{this.props.name}` 의 형태로 컴포넌트의 속성을 표현할 수 있다.

```javascript
class Subject extends Component{
  render(){
    return(
      <header>
        <h1>{this.props.title}</h1>
        {this.props.sub}
      </header>
    );
  }
}

class TOC extends Component{
  render(){
    return(
      <nav>
        <ul>
          <li><a href="1.html">HTML</a></li>
          <li><a href="2.html">CSS</a></li>
          <li><a href="3.html">JavaScript</a></li>
        </ul>
      </nav>
    )
  }
}

class Content extends Component{
  render(){
    return(
      <article>
        <h2>{this.props.title}</h2>
        {this.props.desc}
      </article>
    )
  }
}

class App extends Component{
  render(){
    return(
      <div className="App">
        <Subject title="WEB" sub="World Wide Web"></Subject>
        <Subject title="React" sub="FOR UI"></Subject>
        <TOC></TOC>
        <Content title="HTML" desc="HTMI is HyperTextMarkupLanguage"></Content>
      </div>
    );
  }
}
```

Subject, TOC, Content 라는 세개의 컴포넌트들을 만들어준 후, 이 컴포넌트에서는 props를 통해 데이터를 받도록 해줬다. 입력한 컴포넌트의 속성값이 컴포넌트의 입력값이 되어 그에 따라 출력이 달라지는데 이를 `리팩토링`이라고 한다.

![3개컴포넌트 출력결과](/assets/images/etc/web-first-1.jpg)

### component를 여러 파일로 분리하기

src > components 디렉터리를 만들어주어 그 안에 컴포넌트 별로 따로 파일을 만들어준다.

* src/components/Content.js

```javascript
import React,{Component} from 'react';

class Content extends Component{
    render(){
      return(
        <article>
          <h2>{this.props.title}</h2>
          {this.props.desc}
        </article>
      )
    }
  }

  export default Content;
  ```

* src/components/Subject.js

```javascript
import React,{Component} from 'react';
  class  Subject extends Component{
    render(){
      return(
        <header>
          <h1>{this.props.title}</h1>
          {this.props.sub}
        </header>
      );
    }
  }

  export default Subject;
  ```
  
  * src/components/TOC.js

  ```javascript
  import React,{Component} from 'react';
  class TOC extends Component{
    render(){
      return(
        <nav>
          <ul>
            <li><a href="1.html">HTML</a></li>
            <li><a href="2.html">CSS</a></li>
            <li><a href="3.html">JavaScript</a></li>
          </ul>
        </nav>
      )
    }
  }

  export default TOC;
  ```

분리해준 컴포넌트 파일에서 주의할 점은 `export default TOC;` 이다. 이 부분을 통해 TOC컴포넌트를 외부에서 사용할 수 있는 것이다.

* App.js

```javascript
import './App.css';
import React,{Component} from 'react';
import Content from './components/Content';
import Subject from './components/Subject';
import TOC from './components/TOC';


class App extends Component{
  render(){
    return(
      <div className="App">
        <Subject title="WEB" sub="World Wide Web"></Subject>
        <Subject title="React" sub="FOR UI"></Subject>
        <TOC></TOC>
        <Content title="HTML" desc="HTMI is HyperTextMarkupLanguage"></Content>
      </div>
    );
  }
}

export default App;
```

`import TOC from "./components/TOC"`를 통해 위의 코드에서 태그들을 바로 사용 가능할 수 있게된다.

실행결과는 컴포넌트를 파일로 나누기 전과 동일하다.

### state

사용자의 입장에서 제품을 조작할 때 사용하는 것이 `props`이다. (사용자가 컴포넌트를 사용하는 입장이다) 그리고 제품을 만드는 사람 입장에서 내부적인 구현을 위해 다양한 **내부적 조장장치들**이 `state`이다. (pops의 값에 따라 내부 구현에 필요한 값들이다)

사용자는 props를 조작할 수 있다 => 우리가 만들었던 컴포넌트(내부에 state)를 조작한다는 뜻이다.

state는 컴포넌트 자기 자신이 가지고 있는 값이다.

`<Subject title="WEB" sub="World Wide Web"></Subject>` 에서 "WEB"과 "World Wide Web" 부분을 state로 만들고, 이 값을 Subject의 pops로 전달하려고 한다.

```javascript
import './App.css';
import React,{Component} from 'react';
import Content from './components/Content';
import Subject from './components/Subject';
import TOC from './components/TOC';


class App extends Component{
  constructor(props){
    super(props);
    this.state = {
      subject: {title: 'WEB', sub: "World Wide Web"}
    }
  }
  render(){
    return(
      <div className="App">
        <Subject title={this.state.subject.title} sub={this.state.subject.sub}></Subject>
        <Subject title="React" sub="FOR UI"></Subject>
        <TOC></TOC>
        <Content title="HTML" desc="HTMI is HyperTextMarkupLanguage"></Content>
      </div>
    );
  }
}


export default App;
```

state값을 초기화하고 초기값으로 WEB과 World Wide Web으로 초기화 해준다. 이는 render보다 먼저 실행되고, 컴포넌트 값을 초기화해줄때는 constructor 안에 코드를 변경해주면 된다. 

> constructor가 초기화를 담당하며, 컴포넌트가 만들어질때 제일 먼서 실행된다.

state는 {this.state.subject.title}과 같은 방식으로 사용하면 된다.

index.js가 App.js의 App을 사용중인데, subject가 있는지 없는지 알수없게 된다. (외부에서 알 필요가 없는 정보는 은닉하는 것이 좋은 가용성을 가진 것이다.)

TOC태그 안에 있는 데이터를 App의 내부 state로 TOC에 주입하는 것을 통해서 자동으로 변경되게 할려고 한다. TOC의 부모가 가지고있는 state를 이용해서 TOC 내부 데이터가 바뀌게 된다.

* App.js

```javascript
import './App.css';
import React,{Component} from 'react';
import Content from './components/Content';
import Subject from './components/Subject';
import TOC from './components/TOC';


class App extends Component{
  constructor(props){
    super(props);
    this.state = {
      subject: {title: 'WEB', sub: "World Wide Web"},
      contents: [
        {id:1, title: 'HTML', desc:'HTMI is HyperTextMarkupLanguage'},
        {id:2, title: 'CSS', desc:'CSS is for design'},
        {id:3, title: 'Javascript', desc:'Javascript is for interactive'}
      ]
    }
  }
  render(){
    return(
      <div className="App">
        <Subject title={this.state.subject.title} sub={this.state.subject.sub}></Subject>
        <Subject title="React" sub="FOR UI"></Subject>
        <TOC data={this.state.contents}></TOC>
        <Content title="HTML" desc="HTMI is HyperTextMarkupLanguage"></Content>
      </div>
    );
  }
}


export default App;
```

state에 contents라는 객체배열을 추가해주었으며, data라는 props로 {this.state.contents}를 주입시켜주었다.

* TOC.js

```javascript
import React,{Component} from 'react';

class TOC extends Component{
    render(){
      var lists=[];
      var data = this.props.data;
      var i=0;
      while(i<data.length){
        lists.push(<li key={data[i].id}>
          <a href={"/content"+data[i].id}>
            {data[i].title}
          </a>
        </li>)
        i=i+1;
      }
      return(
        <nav>
          <ul>
            {lists}
          </ul>
        </nav>
      );
    }
  }

  export default TOC;
  ```

TOC.js는 내부적으로 this.props.data를 가지고있다.

lists는 배열이며 데이터를 담는다. 반복문이 실행될때 마다 이 배열에 data의 id값인 **li태그**와 id, title인 **a태그**가 들어가게 된다. 이때 레이블 표시는 **{data[i].title}** 형식으로 해준다. return으로 {lists}를 추가해주면 넣어준 데이터값들이 반환된다.

여기서 `<li key={data[i].id}>` 를 추가해준 이유는 react가 내부적으로 이 값이 필요해서 우리에게 요청하는 정보이다. 이를 작성 안해주면 console창에 에러가 발생한다. (each child should have key prop이라는 에러)



---

참조

- [구글링 블로그](https://studyingych.tistory.com/52)
- [인프런 생활코딩 강의](https://www.inflearn.com/course/react-%EC%83%9D%ED%99%9C%EC%BD%94%EB%94%A9/dashboard)