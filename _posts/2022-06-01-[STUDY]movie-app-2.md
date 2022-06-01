---
layout: post
title: "[STUDY] movie-app-2"
subtitle: Grid Card, Movie info
categories: study
tags: [react]
typora-root-url: ../




---

## [STUDY] movie-app 만들기 2

### landingPage에서 grid card 만들기

view > commons > GridCards.js 생성해준다. common 디렉터리에 생성해준 이유는 grid 스타일을 다른 페이지에서도 사용가능하기 때문이다.

rfce 로 functional component 를 자동 생성해준다.

```javascript
return (
      <Col lg = {6} md = {8} xs ={24}>
          <div style = {{position : 'relative'}}>
              <a href = {`/movie/${props.movieId}`}>
                  <img style = {{width : '100%' , height :'320px' }} src = {props.image} alt = {props.movieName}/>
              </a>
  
          </div>
      </Col>
    )
```

하나의 컬럼은 24 사이즈이며 movie 정보들을 최대로 나타내줄때 6 사이즈 씩 4개, 중간일땐 8 사이즈 씩 3개, 가장 작을 땐 24 사이즈 하나(하나가 컬럼을 다 차지하게)로 나타내준다.

landingPage에서 prop으로 들어갈 gridCard에 들어갈 정보들을 뿌려줘야 한다.

```javascript
<div style = {{width : '85%', margin : '1rem auto'}}>
                <h2>Movies by latest</h2>
                <hr style = {{'margin-bottom': '16px'}}/>

                    {/* Movie Grid cards */}
                    <Row gutter={[16,16]}>
                    {/* grid card 사이사이 하얀 여백 줌 */}
                    {Movies && Movies.map((movie, index)=>(
                        <React.Fragment key = {index}> {/* key 값 넣어줘야 오류가 안난다 */}
                            <GridCards // prop 으로 넘겨쥴 데이터 작성 
                                landingPage
                                image =  {movie.poster_path ? 
                                    `${IMAGE_BASE_URL}w500${movie.poster_path}` : null }
                                    // poster_path 가 없는 영화가 있을 수도 있다*
                                movieId = {movie.id}
                                movieName = {movie.original_title}

                            />
                        </React.Fragment>
                    ))}
                    </Row>

            </div>
```

20개의 영화 정보들은 Movie state에 넣어뒀으므로 이를 map 함수 사용하여 하나하나씩 처리해준다.

이때 key 값을 지정해줘야 오류가 나지 않는다.

GridCards 컴포넌트에 prop 지정해준다. 이때 `image` 는 포스터 이미지이다. 이는 Movies의 poster_path 값을 이용한다. `movieId` 는 grid를 클릭할 때 고유의 영화 정보로 들어가기 위해 필요하므로 지정해줘야 한다.

`gutture` 로 grid의 위아래, 좌우 여백을 준다.

gridCard에선 받은 prop을 통해 링크와 img src를 지정해준다.

```html
<a href = {`/movie/${props.movieId}`}>
                  <img style = {{width : '100%' , height :'320px' }} src = {props.image} alt = {props.movieName}/>
                  {/* image 사이즈 100% 넘어갈때 포스터 크기 초과출력됨   */}
              </a>
```

![landingPage-grid-card](/assets/images/etc/movie-app/landingPage-grid-card.png)

### Load More Button 만들기

landingPage 에서 Load More 버튼을 누르면 20개 다음 영화들 20개를 더 보여줘야한다. 우선 button에 onClick 메서드로 `loadMoreItems` 지정해준다.

```javascript
const fetchMovies = (endpoint)=>{
        fetch(endpoint)
        .then(response => response.json())
        .then(response => {
            console.log(response.results)
            setMovies([...Movies, ...response.results]);
            // 원래 있던 movie와 현재 results 합쳐준다 => Load more 누를때마다 더해짐
            if(currentPage === 0){
                setMainMovieImage(response.results[0]);
            }
            setcurrentPage(response.page)
            // response 페이지 정보도 포함돼있음
        })
    }

    const loadMoreItems = () =>{
        const endpoint = `${API_URL}movie/popular?api_key=${API_KEY}&language=en-US&page=${currentPage+1}`;
        fetchMovies(endpoint);
    }
```

겹치는 코드를 fetchMovies 함수로 만들어줌으로써 코드의 재사용성과 가독성을 높일 수 있다.

처음 landingPage에서 영화 정보 20개를 가져올땐 아래와 같이 써준다.

```javascript
useEffect(()=>{
        const endpoint = `${API_URL}movie/popular?api_key=${API_KEY}&language=en-US&page=1`;
        fetchMovies(endpoint);

    }, [])
```

`currentPage` state를 하나 더 만들어주는데 이는 현재 페이지 정보를 넣어준다. fetchMovies에서 currentPage 를 설정해주고, loadMoreItems에서 이 currentPage state 사용해서 영화 정보들을 가져온다.

fetchMovies 할 때 마다 setMovies로 기존 Movies에 response.results 를 합쳐줘야 load more 버튼을 누를때마다 영화 grid들이 아래로 추가된다. (이거 없으면 기존 20개 정보들이 새로운 영화들로 update 만 된다)

### Movie Detail 페이지 만들기

views > MovieDetail > MovieDetail.js 로 movie detail 페이지 만들어준다.

`Movies/[해당 movie id]` 의 url을 통해 서버로 해당 movie 정보를 보내달라고 request 보내야 한다. 참고로 `useEffect` 에는 DOM이 로드가 되면 처음에 할 동작을 넣어주면 된다.

```javascript
let movieId = props.match.params.movieId

let endpointInfo = `${API_URL}movie/${movieId}?api_key=${API_KEY}`
```

여기서 movieId를 가져오는 방법은 무엇일까

App.js 에 route 정보를 추가해준다. 이때 `:movieId` 로 url 특정 부분을 변수처럼 사용할 수 있게 해준다.

```html
<Route exact path="/movie/:movieId" component={Auth(MovieDetail, null)} />
```

Auth 의 option 값으로 Null을 지정해줬으므로 아무나 들어올 수 있는 권한을 갖는 페이지다.

```javascript
 fetch(endpointInfo)
        .then(response=>response.json())
        .then(response => {
            console.log(response);
            // 영화 정보 response로 가져온다
            setMovie(response);
        })
```

![movie-info(console)](/assets/images/etc/movie-app/movie-info(console).png)

클릭한 영화 정보는 위의 console 정보이다. 이렇게 가져온 영화 정보는 Movie state 에 넣어준다.

landingPage 에서 사용했던 mainImage component 이용해서 movie detail 의 해당 영화 포스터 이미지를 설정해준다.

```javascript
{Movie &&
        <MainImage
            image = {`${IMAGE_BASE_URL}w1280${Movie.backdrop_path}`}
            title = {Movie.original_title}
            text = {Movie.overview}
        />}
```

이미지 아래에 들어갈 영화의 정보들을 보여주기 위해 movieDetail > Sections > MovieInfo.js 를 만들어준다.

```javascript
import { Descriptions } from 'antd'
import React from 'react'

function MovieInfo(props) {
    let {movie} = props;
  return (
    <Descriptions title = "Movie Info" bordered>
        <Descriptions.Item label="Title">{movie.original_title}</Descriptions.Item>
        <Descriptions.Item label="release_date">{movie.release_date}</Descriptions.Item>
        <Descriptions.Item label="revenue">{movie.revenue}</Descriptions.Item>
        <Descriptions.Item label="runtime">{movie.runtime}</Descriptions.Item>
        <Descriptions.Item label="vote_average" span = {2}>
            {movie.vote_average}
        </Descriptions.Item>
        <Descriptions.Item label="vote_count">{movie.vote_count}</Descriptions.Item>
        <Descriptions.Item label="status">{movie.status}</Descriptions.Item>
        <Descriptions.Item label="popularity">{movie.popularity}</Descriptions.Item>
    </Descriptions>
  )
}

export default MovieInfo
```

movieDetail 에서 movieInfo를 import 한 다음에

```javascript
{   Movie &&
    <MovieInfo movie = {Movie}/>
}
```

movie 라는 prop 을 넣어주고 Movie state 를 이것에 넣어준다.


![movie-info(web)](/assets/images/etc/movie-app/movie-info(web).png)

### 영화 출연진들 정보 가져오기

MovieDetail 페이지에서 toggle actor view 라는 버튼을 눌러주면 해당 영화의 캐스팅 정보가 나오는 기능을 설정하려고 한다.

이때 landingPage 에서 이미 만들어둔 grid와 동일한 모양이므로 이 컴포넌트를 이용해서 actor 데이터만 대신 넣어주면 된다.

```javascript
let endpointCrew = `${API_URL}movie/${movieId}/credits?api_key=${API_KEY}`;

fetch(endpointCrew)
        .then(response=>response.json())
        .then(response => {
            console.log('responseForCrew',response);
            // 영화 출연진들 정보 response 로 가져온다
            setCasts(response.cast)
        })
```

가져온 actor 정보들은 아래와 같다.

![caset(console)](/assets/images/etc/movie-app/cast(console).png)

cast 가 actor 정보이므로 response.cast 만 Casts state에 넣어주면 된다.

```javascript
{Casts && Casts.map((Casts, index)=>(
                    <React.Fragment key = {index}>
                        <GridCards
                            image =  {Casts.profile_path ? 
                                `${IMAGE_BASE_URL}w500${Casts.profile_path}` : null }
                            characterName = {Casts.name}

                        />
                    </React.Fragment>
                ))}
```

image 정보와 charaterName 정보를 넣어준다.

gridCards 에선 landingPage에서 사용될 때와 Casts 정보를 보여줄 때 두가지 경우로 나누어준다.

```javascript
if(props.landingPage){
    return (
      <Col lg = {6} md = {8} xs ={24}>
          <div style = {{position : 'relative'}}>
              <a href = {`/movie/${props.movieId}`}>
                  <img style = {{width : '100%' , height :'320px' }} src = {props.image} alt = {props.movieName}/>
                  {/* image 사이즈 100% 넘어갈때 포스터 크기 초과출력됨   */}
              </a>
  
          </div>
      </Col>
    )
  } else{
    return (
      <Col lg = {6} md = {8} xs ={24}>
          <div style = {{position : 'relative'}}>
              <img style = {{width : '100%' , height :'320px' }} src = {props.image} alt = {props.charaterName}/>
          </div>
      </Col>
    )
  }
```

이때 토글이 가능하도록 `actorToggle` state 를 하나 더 만들어준다. initial value 는 false 이며 이는 actor 정보가 보여지지 않는 것을 의미한다. true 일때 정보 보여주는 것으로 지정한다.

```javascript
const toggleActorView = ()=>{
        setActorToggle(!ActorToggle);
    }


<div style={{display : 'flex', justifyContent : 'center' , margin :'2rem'}}>
                <button onClick ={toggleActorView}> Toggle Actor View </button>
            </div>

{ActorToggle &&
                <Row gutter={[16,16]}>

                {Casts && Casts.map((Casts, index)=>(
                    <React.Fragment key = {index}>
                        <GridCards
                            image =  {Casts.profile_path ? 
                                `${IMAGE_BASE_URL}w500${Casts.profile_path}` : null }
                            characterName = {Casts.name}

                        />
                    </React.Fragment>
                ))}

                </Row>
            }
```

button 에 onClick 메서드로 `toggleActorView` 를 지정해주고 actorToggle 이 true 일때만 해당 정보를 보여주기 위해 `{ActorToggle && <Row ~~>}` 로 코드를 작성해준다.

![cast(web)](/assets/images/etc/movie-app/cast(web).png)

---


참조

- [인프런 강의 - 영화 사이트 만들기](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%85%B8%EB%93%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EC%98%81%ED%99%94%EC%82%AC%EC%9D%B4%ED%8A%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0/dashboard)
