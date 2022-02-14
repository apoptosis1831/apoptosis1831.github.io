---
layout: post
title: "[STUDY] React 기본 -4 (create기능 구현)"
subtitle: onChangeMode, onSubmit이벤트, push와 concat
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] React 기본 -4 (onChangeMode, onSubmit이벤트, push와 concat)

### 베이스 캠프

여태껏 공부해온 것을 대충 정리하자면 우선 `props`는 사용자측에서 사용하는 것이며 read-only특징을 가지고 있다. `state`는 코드 구현자측에서 사용하며 setState로 state값이 변경 가능하다.

여기서 상위 컴포넌트가 하위 컴포넌트에게 명령할때는 props를 통해서 하며, 하위가 상위로 명령할 떈 event를 통해서 코드가 동작한다.

### Create 구현

**CRUD** 중 C에 해당하는 Create를 구현해보고자 한다.

* App.js

```javascript
import './App.css';
import React,{ Component } from 'react';
import TOC from "./componenets/TOC"
import Content from './componenets/Content';
import Subject from './componenets/Subject'
import Control from './componenets/Control'


class App extends Component {
  constructor(props){
    super(props);
    this.state= {
      mode : 'read',
      selected_content_id : 2,
      subject : {title: 'WEB', sub: 'World wide web!'},
      welcome : {title: 'Welcome', desc: 'Hello, React !!'},
      contents: [
        {id:1, title:'HTML', desc:'HTML is HyperText...'},
        {id:2, title:'CSS', desc:'Css is for design'},
        {id:3, title:'Javascript', desc:'Javascript is for interactive'}
      ]
    }
  }

  render() {
    
    console.log('App render');
    var _title, _desc = null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc = this.state.welcome.desc;
    } else if(this.state.mode === 'read'){
      var i = 0;
      while(i < this.state.contents.length){
        var data = this.state.contents[i];
        if(data.id === this.state.selected_content_id){
          _title = data.title;
          _desc = data.desc;
          break;
        }
        i = i +1;
      }

      
    }
    return (
      <div className="App">
        <Subject
        title={this.state.subject.title}
        sub={this.state.subject.sub}
        onChangePage = {function(){
          this.setState({mode:'welcome'});
        }.bind(this)}
        >
        </Subject>
        
        <TOC
          onChangePage={function(id){
            
            this.setState({
              mode:'read',
              selected_content_id : Number(id)
            });
        }.bind(this)} data={this.state.contents}></TOC>
        <Control onChangeMode={function(_mode){
          this.setState({
            mode : _mode
          });
        }.bind(this)}></Control>
        <Content title={_title} desc={_desc}></Content>
      </div>
    );
  }
}

export default App;
```

* Control.js

```javascript
import React,{ Component } from 'react';

class Control extends Component{
    render(){
      console.log('Subject render');
      return (
        <ul>
          <li><a href="/create" onClick={function(e){
            e.preventDefault();
            this.props.onChangeMode('create');
          }.bind(this)}>create</a></li>
          <li><a href = "/update" onClick={function(e){
            e.preventDefault();
            this.props.onChangeMode('update');
          }.bind(this)}>update</a></li>
          <li><input onClick={function(e){
            e.preventDefault();
            this.props.onChangeMode('delete');
          }.bind(this)} type="button" value="delete"></input></li>
          
        </ul>
      );
  
    }
  }

  export default Control;
  ```

Control.js를 추가해주어 Create, Update, Delete 태그들을 추가해주었다. App.js에서 Control 태그 안에 onChangeMode함수 (이벤트 핸들러 설치)를 통해 mode를 변경할 수 있게 해주었다. 함수 실행시 매개변수로 _mode 를 입력으로 받아 state값의 mode를 변경해주었다.

![실행결과](/assets/images/etc/control_js.jpg)
![실행결과](/assets/images/etc/mode_transfer.jpg)

### mode 전환 기능

create를 누르면 mode가 바뀌게 되면서 Content컴포넌트 또한 동적으로 바뀌게 해주려고 한다.

* App.js

```javascript
import './App.css';
import React,{ Component } from 'react';
import TOC from "./componenets/TOC"
import ReadContent from './componenets/ReadContent';
import Subject from './componenets/Subject'
import Control from './componenets/Control'
import CreateContent from './componenets/CreateContent';


class App extends Component {
  constructor(props){
    super(props);
    this.state= {
      mode : 'read',
      selected_content_id : 2,
      subject : {title: 'WEB', sub: 'World wide web!'},
      welcome : {title: 'Welcome', desc: 'Hello, React !!'},
      contents: [
        {id:1, title:'HTML', desc:'HTML is HyperText...'},
        {id:2, title:'CSS', desc:'Css is for design'},
        {id:3, title:'Javascript', desc:'Javascript is for interactive'}
      ]
    }
  }

  render() {
    
    console.log('App render');
    var _title, _desc, _article = null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc = this.state.welcome.desc;
      _article = <ReadContent title={_title} desc={_desc}></ReadContent>
    } else if(this.state.mode === 'read'){
      var i = 0;
      while(i < this.state.contents.length){
        var data = this.state.contents[i];
        if(data.id === this.state.selected_content_id){
          _title = data.title;
          _desc = data.desc;
          break;
        }
        i = i +1;
      }
      _article = <ReadContent title={_title} desc={_desc}></ReadContent>
    } else if(this.state.mode === 'create'){
      _article = <CreateContent></CreateContent>
    }
    return (
      <div className="App">
        <Subject
        title={this.state.subject.title}
        sub={this.state.subject.sub}
        onChangePage = {function(){
          this.setState({mode:'welcome'});
        }.bind(this)}
        >
        </Subject>
        
        <TOC
          onChangePage={function(id){
            
            this.setState({
              mode:'read',
              selected_content_id : Number(id)
            });
        }.bind(this)} data={this.state.contents}></TOC>
        <Control onChangeMode={function(_mode){
          this.setState({
            mode : _mode
          });
        }.bind(this)}></Control>
        {_article}
      </div>
    );
  }
}

export default App;
```

기존 Content.js의 이름은 ReadContent.js로 바꿔주고, 새로운 CreateContent.js를 만들어줘야 한다. 또한 가변적으로 컴포넌트를 바꿔주기 위하여 _article 이라는 변수를 사용해준다.

mode가 read 및 welcome일때는 `ReacContent 컴포넌트`가 _article로 들어가게 되고, create일 때는 `CreateContent 컴포넌트`가 들어가게 된다.

* CreateContent.js

```javascript
import React,{ Component } from 'react';

class CreateContent extends Component{
    render(){
      console.log('Content render');
      return (
        <article>
              <h2>Create</h2>
              <form>
                
              </form>
          </article>
      );
      }
  }

export default CreateContent;
```

![실행결과](/assets/images/etc/content_transfer.jpg)

create 버튼을 누르면 렌더링 되는 컴포넌트인 CreateContent로 태그명이 바뀐것 확인할 수 있다.

### form 완성하기

글을 추가해주는 `form`을 완성해보자.

* CreateContent.js

```javascript
import React,{ Component } from 'react';

class CreateContent extends Component{
    render(){
      console.log('Content render');
      return (
        <article>
              <h2>Create</h2>
              <form action="/create_process" method="post"
              onSubmit={function(e){
                e.preventDefault();
                this.props.onSubmit(e.target.title.value, e.target.desc.value);
                alert('submit!');

              }.bind(this)}
              >
                <p><input type="text" name="title" placeholder="title"></input></p>
                <p><textarea name="desc" placeholder ="description"></textarea></p>
                <p><input type="submit"></input></p>
              </form>
          </article>
      );
      }
  }

export default CreateContent;
```

`placeholder`는 값이 입력 안됐을때 출력되는 값이다.

`textarea`는 우리가 입력할 텍스트가 여러줄일 때 사용한다.

form태그에서 어디로 전송해줄지 action값을 주며 method=post를 해줘야 url이 노출되지 않는다.

**submit 버튼**을 포함하고 있는 form태그에 `onSubmit` 이벤트를 설치해놓으면 그 이벤트가 실행되게 약속되어 있다. 이는 html의 고유 기능이며 react의 기능이 아니다.

`e.preventDefault`로 페이지 전환이 안되게 해줘야 한다.

* App.js

```javascript
import './App.css';
import React,{ Component } from 'react';
import TOC from "./componenets/TOC"
import ReadContent from './componenets/ReadContent';
import Subject from './componenets/Subject'
import Control from './componenets/Control'
import CreateContent from './componenets/CreateContent';


class App extends Component {
  constructor(props){
    super(props);
    this.state= {
      mode : 'create',
      selected_content_id : 2,
      subject : {title: 'WEB', sub: 'World wide web!'},
      welcome : {title: 'Welcome', desc: 'Hello, React !!'},
      contents: [
        {id:1, title:'HTML', desc:'HTML is HyperText...'},
        {id:2, title:'CSS', desc:'Css is for design'},
        {id:3, title:'Javascript', desc:'Javascript is for interactive'}
      ]
    }
  }

  render() {
    
    console.log('App render');
    var _title, _desc, _article = null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc = this.state.welcome.desc;
      _article = <ReadContent title={_title} desc={_desc}></ReadContent>
    } else if(this.state.mode === 'read'){
      var i = 0;
      while(i < this.state.contents.length){
        var data = this.state.contents[i];
        if(data.id === this.state.selected_content_id){
          _title = data.title;
          _desc = data.desc;
          break;
        }
        i = i +1;
      }
      _article = <ReadContent title={_title} desc={_desc}></ReadContent>
    } else if(this.state.mode === 'create'){
      _article = <CreateContent onSubmit={function(_title, _desc){
        // add content to this.state.contents
        console.log(_title,_desc);
      }.bind(this)}></CreateContent>
    }
    return (
      <div className="App">
        <Subject
        title={this.state.subject.title}
        sub={this.state.subject.sub}
        onChangePage = {function(){
          this.setState({mode:'welcome'});
        }.bind(this)}
        >
        </Subject>
        
        <TOC
          onChangePage={function(id){
            
            this.setState({
              mode:'read',
              selected_content_id : Number(id)
            });
        }.bind(this)} data={this.state.contents}></TOC>
        <Control onChangeMode={function(_mode){
          this.setState({
            mode : _mode
          });
        }.bind(this)}></Control>
        {_article}
      </div>
    );
  }
}

export default App;
```

![실행결과](/assets/images/etc/form.jpg)

### onSubmit 이벤트

onSubmit 이 실행되면 App컴포넌트의 state의 content에 값이 추가되어야 한다. App.js에서 this.state.mode가 create모드일 때 createContent태그 안의 onSubmit함수에서 입력값을 전달받게 해줘야 한다.

`debugger;` 를 통해 이벤트 e 매개변수를 살펴볼 수 있다.

> 코드는 위의 코드 하나로 통일시켰다.

### contents 변경

* App.js

```javascript
import './App.css';
import React,{ Component } from 'react';
import TOC from "./componenets/TOC"
import ReadContent from './componenets/ReadContent';
import Subject from './componenets/Subject'
import Control from './componenets/Control'
import CreateContent from './componenets/CreateContent';


class App extends Component {
  constructor(props){
    super(props);
    this.max_content_id = 3;
    this.state= {
      mode : 'create',
      selected_content_id : 2,
      subject : {title: 'WEB', sub: 'World wide web!'},
      welcome : {title: 'Welcome', desc: 'Hello, React !!'},
      contents: [
        {id:1, title:'HTML', desc:'HTML is HyperText...'},
        {id:2, title:'CSS', desc:'Css is for design'},
        {id:3, title:'Javascript', desc:'Javascript is for interactive'}
      ]
    }
  }

  render() {
    
    console.log('App render');
    var _title, _desc, _article = null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc = this.state.welcome.desc;
      _article = <ReadContent title={_title} desc={_desc}></ReadContent>
    } else if(this.state.mode === 'read'){
      var i = 0;
      while(i < this.state.contents.length){
        var data = this.state.contents[i];
        if(data.id === this.state.selected_content_id){
          _title = data.title;
          _desc = data.desc;
          break;
        }
        i = i +1;
      }
      _article = <ReadContent title={_title} desc={_desc}></ReadContent>
    } else if(this.state.mode === 'create'){
      _article = <CreateContent onSubmit={function(_title, _desc){
        // add content to this.state.contents
        this.max_content_id = this.max_content_id+1;
        // this.state.contents.push(
        //   {id:this.max_content_id, title : _title, desc : _desc}
        //   );
        var _contents = this.state.contents.concat(
          {id:this.max_content_id, title : _title, desc : _desc}
        );
        this.setState({
          contents: _contents
        });
        console.log(_title,_desc);
      }.bind(this)}></CreateContent>
    }
    return (
      <div className="App">
        <Subject
        title={this.state.subject.title}
        sub={this.state.subject.sub}
        onChangePage = {function(){
          this.setState({mode:'welcome'});
        }.bind(this)}
        >
        </Subject>
        
        <TOC
          onChangePage={function(id){
            
            this.setState({
              mode:'read',
              selected_content_id : Number(id)
            });
        }.bind(this)} data={this.state.contents}></TOC>
        <Control onChangeMode={function(_mode){
          this.setState({
            mode : _mode
          });
        }.bind(this)}></Control>
        {_article}
      </div>
    );
  }
}

export default App;
```

`max_content_id`를 생성해서 마지막 id값으로 지정해주었다. 이것을 state로 넣지 않은 이유는 어떤 데이터를 추가할때 id값을 뭐로 할지만 결정하는 데이터이고, UI에는 전혀 영향을 주지 않는 값이기 때문이다.

onSubmit이 실행될때 마다 max_content_id 값은 1씩 증가되며, `{id:this.max_content_id, title:_title, desc:_desc} 객체`를 생성해서 this.state.contents 뒤에 삽입하면 contents에 해당 객체가 추가된다.

위의 주석 처리된 push방법도 가능은 하나 `push`처럼 원본 데이터를 변경하는 함수보다는 `concat`과 같이 원본 데이터를 건드리지 않고 값이 추가된 새로운 배열을 return해주는 함수를 사용하는 것이 더 낫다. (push는 성능개선할때 까다로울 수 있다)

![실행결과](/assets/images/etc/content_insert.jpg)

위의 결과를 보면 create로 넣어준 title: hi, desc: hi 값이 id 4번으로 잘 들어간 것을 확인할 수 있다.


---

### 참조 

- [인프런 생활코딩 강의](https://www.inflearn.com/course/react-%EC%83%9D%ED%99%9C%EC%BD%94%EB%94%A9/dashboard)