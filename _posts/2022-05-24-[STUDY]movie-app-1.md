---
layout: post
title: "[STUDY] movie-app-1"
subtitle: 프로젝트 시작, landingPage
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] movie-app 만들기 1

### boiler-plate 와 mongoDB 연결

우선 boiler-plate를 사용하는 이유가 뭘까? 영화의 정보를 가져오고 그 정보를 이용해서 웹 개발하는 것에 집중하기 위해 기본적인 회원가입이나 로그인 기능이 구현된 boiler-plate 을 가져와서 프로젝트를 생성한다.  

인프런 강의해주시는 john ahn 님의 깃허브에 올려진 boiler-plate 를 다운로드 받는다. (https://github.com/jaewonhimnae/boilerplate-mern-stack)

서버와 클라이언트에서 package.json에 정의된 라이브러리, dependency를 다운로드 받는다. 이때 node는 다운 받아져 있는 상태여야 하는데 이는 `node -v` 명령어로 확인 가능하다. node 다운로드 된 게 확인 됐으면 `npm install` 로 라이브러리들을 설치해준다. (서버, 클라이언트 모두 install)

server > Config > dev.js 생성해서 로컬에서 작업하는 development환경일 때 사용할 수 있는 mongoDBURL를 작성해준다. 이는 전에 포스팅 완료한 boiler-plate 프로젝트의 dev.js와 동일하게 해주면 된다.


### TheMovieDB API

https://www.themoviedb.org/ 접속 후 API 사용하기 위한 `API_KEY` 를 받아야 한다. 이를 위해 로그인 과정이 필요하다. 로그인 후 설정 > API 메뉴에서 새로운 API KEY를 생성해준다.

이때 API URL은 아래와 같은 형식으로 사용 가능하다.

![api url](/assets/images/etc/movie-app/themoviedb_url_info.jpg)

여기서 URL의 공통 부분이 있다. 바로 https://api.themoviedb.org/3/

image URL 은 아래와 같은 형식으로 맞춰 사용 가능하다.

![api image url](/assets/images/etc/movie-app/themoviedb_url_info.jpg)

여기서 공통 URL 부분은 http://image.tmdb.org/t/p/ 이다.

그리고 이러한 공통 url 들은 config.js에 따로 정의해둬서 재사용성의 편의를 높여준다.

```javascript
export const API_URL = 'https://api.themoviedb.org/3/';

export const IMAGE_BASE_URL = 'http://image.tmdb.org/t/p/';

export const API_KEY = '367d662f7b8f6a64daca5b4fe53e5877'
```

### LandingPage 만들기 - 1

우선 LandingPage 의 전체적인 템플릿 틀을 만들어준다.

```javascript
return (
        <div style = {{width : '100%', margin : '0'}}>

            {/* Main image */}
           
            <div style = {{width : '85%', margin : '1rem auto'}}>
                <h2>Movies by latest</h2>
                <hr/>
                    {/* Movie Grid cards */}

            </div>

            <div style = {{display : 'flex', justifyContent: 'center'}}>
                <button>Laod More</button>
            </div>

        </div>
    )
```

![landingPage-1](/assets/images/etc/movie-app/landingPage-1.jpg)

실행결과는 이렇게 나온다.

```javascript
    const [Movies, setMovies] = useState([])
    const [MainMovieImage, setMainMovieImage] = useState(null)

    useEffect(()=>{
        const endpoint = `${API_URL}movie/popular?api_key=${API_KEY}&language=en-US&page=1`;
 
        fetch(endpoint)
        .then(response => response.json())
        .then(response => console.log(response))

    }, [])
```

`endpoint` 는 movie의 정보를 가져올 수 있는 url 을 정의해둔 것이다. 이때 page=1 로 첫번째 페이지만 가져오게 설정해둔다.

`fetch 함수`를 이용해서 endpoint를 매개변수로 넣어주면 현재 인기 있는 영화 정보를 가져오게 된다. 이 정보를 console.log로 찍어서 살펴보자.

![response-1](/assets/images/etc/movie-app/response_console-1.jpg)

![response-2](/assets/images/etc/movie-app/response_console-2.jpg)

우선 results는 20개가 있다. 이는 가장 인기 있는 영화 20위까지 정보를 가져온다는 것을 의미한다. 이러한 정보들은 useState를 이용하여 `state`에 넣어준다.

```javascript

const [Movies, setMovies] = useState([]) // 배열 형태의 state
    const [MainMovieImage, setMainMovieImage] = useState(null)

    useEffect(()=>{
        const endpoint = `${API_URL}movie/popular?api_key=${API_KEY}&language=en-US&page=1`;
 
        fetch(endpoint)
        .then(response => response.json())
        .then(response => {
            setMovies([response.results]);
        })

    }, [])

```

url에서 가져온 해당 정보들을 state에 넣어준 뒤 landingPage > Sections > `MainImage.js` 파일을 생성하여 landingPage의 상위 부분에 들어갈 `대표 영화`를 만들어준다.

```javascript
import React from 'react';
function MainImage(props){
    return(
        <div style = {{
            background : `linear-gradient(to bottom, rgba(0,0,0,0)
            39%,rgba(0,0,0,0) 
            41%,rgba(0,0,0,0.65) 
            100%),
            url('${props.image}'), #1c1c1c`,
            height: '500px',
            backgroundSize: '100%, cover',
            backgroundPosition: 'center, center',
            width: '100%',
            position: 'relative'
        }}>
            <div>
                <div style = {{ position : 'absolute', maxWidth : '500px', bottom: '2rem', marginLeft: '2rem'}}>

                    <h2 style={{color:'white'}}> {props.title} </h2>
                    <p style = {{color : 'white', fontSize : '1rem'}}> {props.text} </p>
                </div>
            </div>
        </div>
    )
}

export default MainImage;
```

이미지 부분, 그리고 그 안에 영화 제목인 title, 영화 설명인 text 를 만들어준다. 여기서 landingPage.js 에서는 `MainMovieImage라는 state`를 만들어준다.

```javascript
const [MainMovieImage, setMainMovieImage] = useState(null)


fetch(endpoint)
        .then(response => response.json())
        .then(response => {
            setMovies([response.results]);
            setMainMovieImage(response.results[0]);
        })
```

MainMovieImage에는 response.results[0] 즉 제일 인기 많은 movie의 데이터를 넣어주게 된다.

```javascript
<MainImage image = {`${IMAGE_BASE_URL}w1280${MainMovieImage.backdrop_path}`}
            title = {MainMovieImage.original_title}
            text = {MainMovieImage.overview}
            />
```

MainImage.js에서 가져온 MainImage에 대표 영화 정보를 넣어주기 위해 `image url`을 작성하는 과정이 필요하다. 이는 state의 수많은 데이터 중 `backdrop_path` 속성을 통해 완성해줄 수 있다.

또한 MainImage로 영화 제목과 영화 설명을 넘겨 주기 위해 `original_title`, `overview` 속성도 함께 넣어준다.

```javascript
<div style = {{
            background : `linear-gradient(to bottom, rgba(0,0,0,0)
            39%,rgba(0,0,0,0) 
            41%,rgba(0,0,0,0.65) 
            100%),
            url('${props.image}'), #1c1c1c`,
            height: '500px',
            backgroundSize: '100%, cover',
            backgroundPosition: 'center, center',
            width: '100%',
            position: 'relative'
        }}>
```

MainImage.js 에서는 props.image 로 만들어둔 image url 을 넣어주기만 하면된다.

```javascript
{MainMovieImage &&<MainImage image = {`${IMAGE_BASE_URL}w1280${MainMovieImage.backdrop_path}`}
            title = {MainMovieImage.original_title}
            text = {MainMovieImage.overview}
            />
}
```

이때 landingPage 쪽 rendering 부분에선 위의 코드처럼 작성해 줘야 하는ㄴ데 그 이유는 backdrop_path 에 접근하기 위해선 우선적으로 endpoint로 url 을 가져온 MainMovieImage state가 있어야 하는데 이를 가져오기 전에 페이지를 먼저 rendering 하기 때문에 오류가 나게된다. 이를 해결하기 위해 위의 코드처럼 `{MainMovieImage && ~~}` 로 코드를 작성해주는데, 이는 MainMpvieImage가 있을때에만 뒤의 코드를 실행한다는 뜻이다.

![landingPage-1-compelete](/assets/images/etc/movie-app/landingPage-1-compelete.jpg)


실행결과는 위와 같다.

---

참조

- [인프런 강의 - 영화 사이트 만들기](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%85%B8%EB%93%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EC%98%81%ED%99%94%EC%82%AC%EC%9D%B4%ED%8A%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0/dashboard)