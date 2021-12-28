---
layout: post
title: STUDY-MySQL(서버실행, DB편집)
subtitle: MySQL서버 실행과 기초적인 DB편집
categories: study
tags: [MySQL]


---



## STUDY-MySQL (서버실행, DB편집)

### MySQL서버 실행

(MySQL설치가 다 됐다는 가정하에 실행가능하다)

> mysql.server start



### MySQL초기 설정 방법

> mysql_secure_installation



### MySQL서버 중단

> mysql.server stop



### MySQL접속

> mysql -u root -p

초기설정 과정중에서 설정했던 패스워드로 로그인 해야한다.



### Database생성

> CREATE DATABASE [DB명];

CREATE DATABASE PROJECT_DB;

형식으로 자신이 원하는 데이터베이스 이름으로 DB를 생성해줄 수 있다.

그리고 현재 어떤 DB들이 있는지 확인하기 위해선 아래 명령어를 입력해준다.

> show databases;

해당 명령어로 DB편집할 DB명을 알아냈다면 아래 명령어로 DB를 선택할 수 있다.

> use [DB명];



### Table생성

> create table [테이블명] (
>
> [컬럼명] [type],
>
> [컬럼명] [type]
>
> );

위 명령어로 Table을 생성해줄 수 있고 아래 명령어로 해당 DB의 테이블들을 리스트로 볼 수 있다.

> show tables;

테이블의 전체적인 구조(field, type, null여부, key, default값, extra) 정보를 얻기 위해서 아래 명령어를 실행해야 한다.

> desc [테이블명];



### Data 입력

> insrt into [테이블명] ( [컬럼명], [컬럼명] ) values ( [데이터], [데이터] );

데이터 입력은 insrt into 명령으로 가능하다.

> select * from [테이블명];

해당 테이블의 모든 Data를 확인하는 명령어는 select * from 이다.

결과는 표 형식으로 컬럼과 속성들이 함께 나타난다.



### Data 삭제

> delete from [테이블명] where [컬럼명] = [데이터];

삭제는 delete 명령으로 가능하며 where뒤에는 삭제하고자 하는 데이터의 조건을 설정해주면 된다.



### Table 삭제

> drop table [테이블명];



### Database 삭제

> drop database [DB명];

테이블과 DB는 모두 drop명령을 통해 가능하다. 한번에 해당 테이블이나 DB의 데이터가 모두 삭제되는 것이므로 유의하여 사용하여야 할것 같다.



---------------

#### 해당 포스팅을 마치며

학창시절 DB시간에 배웠던 내용들이 새록새록 떠올랐다. 분명 이렇게 모아놓으니 엄청 쉬운것 같은데 그 시절 교수님께서는 어찌나 복잡하게 문제를 만들어 내셨는지, 존경스럽다 정말..

DB같은 경우는 한번 까딱하면 대량의 데이터들이 변경되거나 삭제될 수 있으므로 정신차리고 작업해야 된다는 걸 예전부터 느꼈다.

물론 커밋이나 롤백 기능으로 그런 실수들을 사전에 예방할 순 있겠지만 커밋 이후에 실수를 발견했다간 되돌릴 수 없는 지경에 다다를지도 모른다.



[참조 블로그](https://secure-key.tistory.com/56)