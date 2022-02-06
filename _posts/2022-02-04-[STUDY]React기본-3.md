---
layout: post
title: "[STUDY] React 기본 -3 () [작성중]"
subtitle: 
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] React 기본 -3 ()

### 역동성 만들기

우선 Subject 컴포넌트에 링크를 달아주자.

* Subject.js

```javascript
import React,{Component} from 'react';

class Subject extends Component{
    render(){
      return(
        <header>
          <h1><a href="/">{this.props.title}</a></h1>
          {this.props.sub}
        </header>
      );
    }
  }

  export default Subject;
  ```

  이 링크를 클릭하면 그에 따라서 app의 컴포넌트 state가 바뀌고, 바뀐 state가 Content컴포넌트의 props의 값으로 전달되면서 동적으로 app이 바뀌는 것이 목표이다.

  state값이 바뀌면 그 state를 갖고있는 컴포넌트의 render함수가 다시 호출된다. render함수 하위에 있는 컴포넌트도 다 같이 호출되어 화면이 다시 그려지게 된다. (render가 어떤 html을 그릴지 결정하는 함수이기 때문이다)

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
      mode : 'welcome',
      subject: {title: 'WEB', sub: "World Wide Web"},
      welcome: {title: 'Welcome', desc : 'Hello React!!'},
      contents: [
        {id:1, title: 'HTML', desc:'HTMI is HyperTextMarkupLanguage'},
        {id:2, title: 'CSS', desc:'CSS is for design'},
        {id:3, title: 'Javascript', desc:'Javascript is for interactive'}
      ]
    }
  }
  render(){
    var _title, _desc=null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc=this.state.welcome.desc;
    } else if(this.state.mode === 'read'){
      _title = this.state.contents[0].title;
      _desc = this.state.contents[0].desc;
    }
    return(
      <div className="App">
        <Subject
        title={this.state.subject.title}
        sub={this.state.subject.sub}
        ></Subject>
        <TOC data={this.state.contents}></TOC>
        <Content
        title={_title}
        desc={_desc}
        ></Content>
      </div>
    );
  }
}


export default App;
```

mode에 따라서 만들어질 렌더링 결과가 달라지게 하기위해 render함수안에 if문을 넣어줬다. state의 mode값이 'welcome'인 경우 welcome state의 title과 desc를 가져온다. mode가 'read'인 경우 contents state의 첫번째 title과 desc를 가져온다.(HTML/HTML is HyperTextMark...)

> 이때 각 render함수에 console을 찍어주면 차례대로 app.js하위 컴포넌트 render함수가 실행되는것을 확인할 수 있다.
>
> 결과 :
>
> App render
>
> Subject render
>
> TOC render
>
> Content Render


### 이벤트 설치하기

Subject.js안의 Subject 컴포넌트를 app.js로 잠시 copy해서 실습해보자.

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
      mode : 'welcome',
      subject: {title: 'WEB', sub: "World Wide Web"},
      welcome: {title: 'Welcome', desc : 'Hello React!!'},
      contents: [
        {id:1, title: 'HTML', desc:'HTMI is HyperTextMarkupLanguage'},
        {id:2, title: 'CSS', desc:'CSS is for design'},
        {id:3, title: 'Javascript', desc:'Javascript is for interactive'}
      ]
    }
  }
  render(){
    var _title, _desc=null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc=this.state.welcome.desc;
    } else if(this.state.mode === 'read'){
      _title = this.state.contents[0].title;
      _desc = this.state.contents[0].desc;
    }
    return(
      <div className="App">
        {/* <Subject
        title={this.state.subject.title}
        sub={this.state.subject.sub}
        ></Subject> */}
        <header>
          <h1><a href="/"
            onClick = {function(e){
              console.log(e);
              e.preventDefault();
            }}
          >
          {this.state.subject.title}</a></h1>
          {this.state.subject.sub}
        </header>
        <TOC data={this.state.contents}></TOC>
        <Content
        title={_title}
        desc={_desc}
        ></Content>
      </div>
    );
  }
}


export default App;
```

우선 Subject.js에서 this.pops.title로 작성한 것은 App.js에서 this.state.subject.title로 변수명을 변경해줘야 한다. 여기서 onClicke을 함수로 지정해주었고 이것이 실행된다. 기본적으로 a라고 하는 태그를 클릭했을 때 href속성이 가리키는 페이지로 이동하는데, 이 기본적인 동작 방법을 못하게 하기위해 function 첫번째 매개변수 값으로 event라는 객체를 주입해준다.

`debugger` : 크롬 개발자 도구에서 브라우저가 실행을 멈추고 sources라는 창을 띄워준다.

`e.preventDefault()` : a태그의 기본적인 동적방식을 금지시킨다.

> 위의 코드에서 web을 클릭하면 console.log(e)가 출력되는데 페이지는 전환되지 않는다. (e.preventDefault() 때문에 가능하다)

![실행결과](/assets/images/etc/event-log.jpg)

```javascript
<header>
          <h1><a href="/"
            onClick = {function(e){
              console.log(e);
              e.preventDefault();
              this.setState({
                mode: 'welcome'
              })
            }.bind(this)}
          >
          {this.state.subject.title}</a></h1>
          {this.state.subject.sub}
</header>
```

a태그를 클릭했을때 App컴포넌트의 mode값을 welcome으로 바꾸는 코드이다.

function() 안에선 컴포넌트 this를 인식 못하기 때문에 아무값도 세팅이 안돼있는 undefined상태이다. 그렇기 때문에 function()바로 뒤에 `.bind(this)`를 붙여주어야 한다.

this.state.mode = 'welcome'형식으로 코드를 짜면 react는 state상태가 변한지 모른다. `this.setState({mode : 'welcome'})` 형식으로 작성해주면 state상태를 바뀌줄 수 있다.

### 이벤트 bind() 함수

App 컴포넌트 안에 render 함수 안에 this는 render가 속해있는 App 컴포넌트 자신을 가리킨다. 하지만 태그 안에서 작성한 함수 안에서 this는 undefined이다.

```javascript
var obj= { name : 'egoing' };
function bindTest(){
    console.log(this.name);
} // 아무결과 없다
var bindTest2 = bindTest.bind(obj);
// bindTest2 함수 실행결과 = 'egoing'출력된다
```

### 이벤트 setState() 함수

`this.state.mode = 'welcome'` 으로 함수 안에서 state값을 변경해주려고 하면 react는 인식 못한다. (constructor 안에서 위의 코드는 실행 가능하다)

그렇기 때문에 `this.setState({})`형식으로 state값을 변경할 수 있다.

### 컴포넌트 이벤트 만들기

* App.js

```javascript
return(
      <div className="App">
        <Subject
        title={this.state.subject.title}
        sub={this.state.subject.sub}
        onChangePage = {function(){
          this.setState({mode:'welcome'});
        }.bind(this)}
        ></Subject>
        <TOC data={this.state.contents}></TOC>
        <Content
        title={_title}
        desc={_desc}
        ></Content>
      </div>
    );
```

* Subject.js

```javascript
class Subject extends Component{
    render(){
      return(
        <header>
          <h1><a href="/"
          onClick = {function(e){
              e.preventDefault();
              this.props.onChangePage();
          }.bind(this)}
          >{this.props.title}</a></h1>
          {this.props.sub}
        </header>
      );
    }
  }
```

Subject 컴포넌트를 사용하여 링크 클릭했을 때 이벤트 안에 작성된 함수 호출할 수 있게 한다.

App.js의 Subject태그 안에 OnChangePage함수를 작성하여 이 함수는 props형태로 Subject.js에 전달된다. 그리고 Subject.js에서 props의 onChangePage()함수를 호출해준다.




---

참조 : 