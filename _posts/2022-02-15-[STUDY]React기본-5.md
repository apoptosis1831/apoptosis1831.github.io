---
layout: post
title: "[STUDY] React 기본 -5 (update와 delete 구현)"
subtitle: update와 delete 구현
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] React 기본 -5 (update와 delete 구현)

### Update 기능 구현

Update기능은 read기능과 create기능이 결합된 것이다. 폼 기능이 구현되어있는 기존의 `CreateContent` 파일을 복사하여 `UpdateContent` 파일을 만들어보자.

* UpdateContent.js

```javascript

import React,{ Component } from 'react';

class UpdateContent extends Component{
  constructor(props){
    super(props);
    this.state={
      title:this.props.data.title,
      desc : this.props.data.desc
    }
    this.inputFormHandler = this.inputFormHandler.bind(this);
  }
  inputFormHandler(e){
    this.setState({[e.target.name]:e.target.value});
  }
    render(){
      console.log(this.props.data);
      console.log('UpdateContent render');
      return (
        <article>
              <h2>Update</h2>
              <form action="/create_process" method="post"
              onSubmit={function(e){
                e.preventDefault();
                this.props.onSubmit(e.target.title.value, e.target.desc.value);
              }.bind(this)}
              >
                <p>
                  <input
                  type="text"
                  name="title"
                  placeholder="title"
                  value = {this.state.title}
                  onChange={this.inputFormHandler}
                  ></input>
                  </p>
                <p><textarea
                  onChange={this.inputFormHandler}
                name="desc"
                placeholder ="description"
                value={this.state.desc}></textarea></p>
                <p><input type="submit"></input></p>
              </form>
          </article>
      );
      }
  }

export default UpdateContent;
```

* App.js

```javascript
import './App.css';
import React,{ Component } from 'react';
import TOC from "./componenets/TOC"
import ReadContent from './componenets/ReadContent';
import Subject from './componenets/Subject'
import Control from './componenets/Control'
import CreateContent from './componenets/CreateContent';
import UpdateContent from './componenets/UpdateContent';


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

  getReadContent(){
    var i = 0;
      while(i < this.state.contents.length){
        var data = this.state.contents[i];
        if(data.id === this.state.selected_content_id){
          return data;
          break;
        }
        i = i +1;
      }
  }
  getContent(){
    var _title, _desc, _article = null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc = this.state.welcome.desc;
      _article = <ReadContent title={_title} desc={_desc}></ReadContent>
    } else if(this.state.mode === 'read'){
      var _content = this.getReadContent();
      _article = <ReadContent title={_content.title} desc={_content.desc}></ReadContent>
    } else if(this.state.mode === 'create'){
      _article = <CreateContent onSubmit={function(_title, _desc){
        this.max_content_id = this.max_content_id+1;
        var newContents = Array.from(this.state.contents);
        newContents.push({id:this.max_content_id, title : _title, desc : _desc});
        this.setState({
          contents: newContents
        });
        console.log(_title,_desc);
      }.bind(this)}></CreateContent>
    } else if(this.state.mode === 'update'){
      _content = this.getReadContent();
      _article = <UpdateContent data = {_content} onSubmit={function(_title, _desc){
        this.max_content_id = this.max_content_id+1;
        var newContents = Array.from(this.state.contents);
        newContents.push({id:this.max_content_id, title : _title, desc : _desc});
        this.setState({
          contents: newContents
        });
        console.log(_title,_desc);
      }.bind(this)}></UpdateContent>
    }
    return _article;
  }

  render() {
    
    console.log('App render');
    
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
        {this.getContent()}
      </div>
    );
  }
}

export default App;
```

UpdateContent를 생성해서 update를 누르면 mode를 'update'로 바꿔준 후, 레이블도 Update를 띄운다.

getContent 함수를 만들어 _article을 리턴하게 해주었다.

UpdateContent 실행될때 입력값으로 현재 선택된 content id에 해당하는 content를 UpdateContent 컴포넌트에 주입시켜줘야 한다. 이는 getReadContent 함수를 만들어주어 data를 리턴함으로써 구현했다. getContent함수 안에서 this.getReadContent 실행되고 리턴되는 값을 _content에 넣어준다.

### Form 만들기

props의 데이터는 read-only이므로 contructor를 작성해준다.

value = {this.state.title}로 input 태크를 작성하며 onChange로 function 만들어줬다. inputFormHandler에서 `[e.target.name]`으로 이벤트가 발생하고 있는 태그의 이름을 알수있다.

![실행결과](/assets/images/etc/22-02-15-1.jpg)

### state 변경

* UpdateContent.js

```javascript
import React,{ Component } from 'react';

class UpdateContent extends Component{
  constructor(props){
    super(props);
    this.state={
      id : this.props.data.id,
      title:this.props.data.title,
      desc : this.props.data.desc
    }
    this.inputFormHandler = this.inputFormHandler.bind(this);
  }
  inputFormHandler(e){
    this.setState({[e.target.name]:e.target.value});
  }
    render(){
      console.log(this.props.data);
      console.log('UpdateContent render');
      return (
        <article>
              <h2>Update</h2>
              <form action="/create_process" method="post"
              onSubmit={function(e){
                e.preventDefault();
                this.props.onSubmit(this.state.id, this.state.title, this.state.desc);
              }.bind(this)}
              >
                <input type="hidden" name = "id" value={this.state.id}>

                </input>
                <p>
                  <input
                  type="text"
                  name="title"
                  placeholder="title"
                  value = {this.state.title}
                  onChange={this.inputFormHandler}
                  ></input>
                  </p>
                <p><textarea
                  onChange={this.inputFormHandler}
                name="desc"
                placeholder ="description"
                value={this.state.desc}></textarea></p>
                <p><input type="submit"></input></p>
              </form>
          </article>
      );
      }
  }

export default UpdateContent;
```

* App.js

```javascript
import './App.css';
import React,{ Component } from 'react';
import TOC from "./componenets/TOC"
import ReadContent from './componenets/ReadContent';
import Subject from './componenets/Subject'
import Control from './componenets/Control'
import CreateContent from './componenets/CreateContent';
import UpdateContent from './componenets/UpdateContent';


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

  getReadContent(){
    var i = 0;
      while(i < this.state.contents.length){
        var data = this.state.contents[i];
        if(data.id === this.state.selected_content_id){
          return data;
          break;
        }
        i = i +1;
      }
  }
  getContent(){
    var _title, _desc, _article = null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc = this.state.welcome.desc;
      _article = <ReadContent title={_title} desc={_desc}></ReadContent>
    } else if(this.state.mode === 'read'){
      var _content = this.getReadContent();
      _article = <ReadContent title={_content.title} desc={_content.desc}></ReadContent>
    } else if(this.state.mode === 'create'){
      _article = <CreateContent onSubmit={function(_title, _desc){
        // add content to this.state.contents
        this.max_content_id = this.max_content_id+1;
        // this.state.contents.push(
        //   {id:this.max_content_id, title : _title, desc : _desc}
        //   );
        // var _contents = this.state.contents.concat(
        //   {id:this.max_content_id, title : _title, desc : _desc}
        // );
        var newContents = Array.from(this.state.contents);
        newContents.push({id:this.max_content_id, title : _title, desc : _desc});
        this.setState({
          contents: newContents,
          mode :'read',
          selected_content_id : this.max_content_id
        });
        console.log(_title,_desc);
      }.bind(this)}></CreateContent>
    } else if(this.state.mode === 'update'){
      _content = this.getReadContent();
      _article = <UpdateContent data = {_content} onSubmit={function(_id, _title, _desc){
        var _contents = Array.from(this.state.contents);
        var i=0;
        while(i< _contents.length){
          if(_contents[i].id === _id){
            _contents[i]= {id:_id, title:_title, desc:_desc}
            break;
          }
          i=i+1;
        }
        this.setState({
          contents: _contents
        });
        this.setState({
          cotents: _contents,
          mode: 'read'
        })
      }.bind(this)}></UpdateContent>
    }
    return _article;
  }

  render() {
    
    console.log('App render');
    
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
        {this.getContent()}
      </div>
    );
  }
}

export default App;
```

update를 하려면 어디를 업데이트 할지에 대한 식별자가 필요하다.(여기서 식별자란 id를 의미) hidden 타입의 input태그를 추가해주고 onSubmit에 id 값까지 추가해준다. App.js의 mode가 update일때 onSubmit 부분을 변경한다. i로 id값과 같은 content를 찾는다.

`Array.from` 으로 기존 배열을 복제해서 새로운 배열을 만든다.

### delete 기능

* App.js

```javascript
import './App.css';
import React,{ Component } from 'react';
import TOC from "./componenets/TOC"
import ReadContent from './componenets/ReadContent';
import Subject from './componenets/Subject'
import Control from './componenets/Control'
import CreateContent from './componenets/CreateContent';
import UpdateContent from './componenets/UpdateContent';


class App extends Component {
  constructor(props){
    super(props);
    this.max_content_id = 3;
    this.state= {
      mode : 'welcome',
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

  getReadContent(){
    var i = 0;
      while(i < this.state.contents.length){
        var data = this.state.contents[i];
        if(data.id === this.state.selected_content_id){
          return data;
          break;
        }
        i = i +1;
      }
  }
  getContent(){
    var _title, _desc, _article = null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc = this.state.welcome.desc;
      _article = <ReadContent title={_title} desc={_desc}></ReadContent>
    } else if(this.state.mode === 'read'){
      var _content = this.getReadContent();
      _article = <ReadContent title={_content.title} desc={_content.desc}></ReadContent>
    } else if(this.state.mode === 'create'){
      _article = <CreateContent onSubmit={function(_title, _desc){
        this.max_content_id = this.max_content_id+1;=
        var newContents = Array.from(this.state.contents);
        newContents.push({id:this.max_content_id, title : _title, desc : _desc});
        this.setState({
          contents: newContents,
          mode :'read',
          selected_content_id : this.max_content_id
        });
        console.log(_title,_desc);
      }.bind(this)}></CreateContent>
    } else if(this.state.mode === 'update'){
      _content = this.getReadContent();
      _article = <UpdateContent data = {_content} onSubmit={function(_id, _title, _desc){
        var _contents = Array.from(this.state.contents);
        var i=0;
        while(i< _contents.length){
          if(_contents[i].id === _id){
            _contents[i]= {id:_id, title:_title, desc:_desc}
            break;
          }
          i=i+1;
        }
        this.setState({
          contents: _contents,
          mode: 'read'
        })
      }.bind(this)}></UpdateContent>
    }
    return _article;
  }

  render() {
    
    console.log('App render');
    
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
          if(_mode === 'delete'){
            if(window.confirm('really?')){
              var _contents = Array.from(this.state.contents);
              var i=0;
              while(i< _contents.length){
                if(_contents[i].id===this.state.selected_content_id){
                  _contents.splice(i,1);
                  break;
                }
                i=i+1;
              }
              this.setState({
                mode : 'welcome',
                contents: _contents
              });
              alert('deleted!');
            }
          }else {
          this.setState({
            mode : _mode
          });
        }
        }.bind(this)}></Control>
        {this.getContent()}
      </div>
    );
  }
}

export default App;
```

눌러진 content 내용에서 delete 버튼을 누르면 삭제를 진행하게 된다.

_mode의 값이 delete이면 삭제가 진행되고, 아니면 페이지 전환만 진행된다.

`confirm`에서 확인을 누르게 되면 true가 반환되고 취소를 누르면 false가 반환된다. true가 반환됐으면 누구를 삭제할것인가 를 알아야 한다. 인덱스를 찾아서 `splice 함수`를 통해 삭제 진행 이후 setState로 변경된 contents값을 넣어준다.

---

### 참조 

- [인프런 생활코딩 강의](https://www.inflearn.com/course/react-%EC%83%9D%ED%99%9C%EC%BD%94%EB%94%A9/dashboard)