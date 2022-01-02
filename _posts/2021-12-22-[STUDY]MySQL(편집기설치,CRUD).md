---
layout: post
title: "[STUDY] MySQL(편집기 설치, CRUD)"
subtitle: MySQL편집기 설치 및 CRUD로 데이터 편집
categories: study
tags: [MySQL]



---



## [STUDY] MySQL (편집기 설치, CRUD)



### 편집기 설치

MySQL의 편집기는 내가 참고하는 책에서 설치하라는 workbench를 설치해주었다.

`brew install mysqlworkbench`

터미널에서 한줄의 명령으로 설치를 완료할 수 있었다.

이후 새로운 컨넥션을 생성해주고 edit connection으로 들어가 password에서 Keychain을 root의 비밀번호로 설정해주었다. (이걸 설정해주지 않으면 connection을 열때 자동으로 꺼진다. 이유는 모르겠다.)



쿼리를 실행시키는 것은 해당 쿼리문에 커서를 옮겨놓고 키보드의 `command + enter` 로 가능하다.

또한 여러줄을 실행 시키고자 한다면 원하는 영역을 드래그 한 후 `command + shift + enter` 를 눌러줘야 한다.



### 데이터베이스(스키마) 생성

그 다음은 데이터베이스(스키마) 생성해주는 것이다.

```mysql
show databases;
CREATE SCHEMA `roadbook` default CHARACTER SET UTF8;
USE roadbook; # 해당 DB를 이제 사용한다
SHOW DATABASES;
```

위에 코드에선 CREATE SCHEMA라고 돼있는데 CREATE DATABASE와 동일한 의미이다.

우선 roadbook이라는 스키마를 생성해주고 UTF8옵션을 지정함으로써 한글을 사용할 수 있게 해준다.



### 테이블 생성

```mysql
CREATE TABLE roadbook.customers(
id INT NOT NULL AUTO_INCREMENT,
name VARCHAR(20) NOT NULL,
age INT UNSIGNED NOT NULL,
sex VARCHAR(10) NOT NULL,
joined_data DATETIME NOT NULL DEFAULT now(),
PRIMARY KEY(id)
)
DEFAULT CHARSET = utf8mb4
ENGINE = InnoDB;

CREATE TABLE roadbook.purchase(
id INT NOT NULL AUTO_INCREMENT,
customer_id INT NOT NULL,
book_name VARCHAR(20) NOT NULL,
purchase_date DATETIME NOT NULL DEFAULT now(),
PRIMARY KEY(id),
FOREIGN KEY(customer_id)
REFERENCES roadbook.customers(id)
ON DELETE CASCADE
ON UPDATE CASCADE
)
DEFAULT CHARSET = utf8mb4
ENGINE = InnoDB;

SHOW TABLES;
```

**customers**와 **purchase**라는 두 개의 테이블을 생성해줬다.

사실 roadbook.customers, roadbook.purchase에서 roadbook은 생략이 가능하다. 앞에서 `use roadbook;` 명령어를 실행시켜줬으면 말이다.

나머지 내용은 코드를 찬찬히 살펴보면 이해할 수 있을 것이다. 

두 테이블의 경우 현재 purchase 의 customer_id를 통해 관계가 설정돼있다. 이는 customers의 기본키인 id를 참조하고 있다. 이때 REFERENCES를 사용하는데 `CASCADE`는 고객이 탈퇴하여 삭제될 경우, 그것을 참조하는 외래키에 해당하는 데이터도 함께 삭제한다는 뜻이다.

```mysql
DESCRIBE customers; # DESC customers; 테이블구조 조회
DESCRIBE purchase;
```

`DESC customers;` 도 같은 의미의 명령어다.

해당 명령어로 테이블의 구조를 조회할 수 있다. (Field, type, NULL, Key, Default, Extra)



### CRUD 작업

CRUD란 데이터를 생성(Create)하고, 조회(Read)하고, 수정(Update)하고, 삭제(Delete)하는 작업이다.



#### Create(데이터 생성)

```mysql
INSERT INTO customers(name, age, sex) VALUES('홍길동', 30, '남');
INSERT INTO customers(name, age, sex) VALUES('이수진', 23, '여');
INSERT INTO customers(name, age, sex) VALUES('박민철', 21, '남');
INSERT INTO customers(name, age, sex) VALUES('이세라', 35, '여');
INSERT INTO customers(name, age, sex) VALUES('김유미', 46, '여');
```

```mysql
INSERT INTO purchase(customer_id, book_name) VALUES(1,'엔지니어를 위한 문장의 기술');
INSERT INTO purchase(customer_id, book_name) VALUES(2,'개발자를 위한 글쓰기 카이드');
INSERT INTO purchase(customer_id, book_name) VALUES(3,'엔지니어를 위한 문장의 기술');
INSERT INTO purchase(customer_id, book_name) VALUES(4,'백견불여일타 딥러닝 입문');
INSERT INTO purchase(customer_id, book_name) VALUES(5,'엔지니어를 위한 문장의 기술');
SELECT * FROM customers;
SELECT * FROM purchase; # 해당 테이블의 모든 값 조회
```

데이터 생성은 `INSERT INTO [테이블명] (속성값) VALUES (속성 해당값)` 쿼리문으로 가능하다.



#### Read(데이터 조회)

```mysql
SELECT * FROM purchase;
SELECT name, age FROM customers;
SELECT name FROM customers WHERE age > 99;
```

데이터 조회는 SELECT 쿼리문으로 가능하며 원하는 컬럼만 뽑아서 조회 가능하다. WHERE문 뒤에 조건을 걸어 조회할 수도 있다.



#### Update(데이터 수정)

```mysql
UPDATE customers set age = 100 where id = 1;
```

위에 코드는 customers테이블에서 id값이 1인 데이터의 age를 100으로 설정해주는 것이다.



#### Delete(데이터 삭제)

```mysql
DELETE FROM customers where id = 1;
```

id가 1인 데이터를 customers테이블에서 찾아 삭제해준다. 참고로 이 명령을 수행하면 purchase테이블에 customer_id가 1인 값도 함께 삭제된다. 

이유는 purchase 테이블을 생성해줄때

```mysql
REFERENCES roadbook.customers(id)
ON DELETE CASCADE
ON UPDATE CASCADE
```

이 문구를 넣어줘서 이다.



### 테이블 및 데이터 전체 삭제

참고로 내용을 덧붙이자면 테이블 전체를 삭제할땐 `DROP TABLE customers;` 이고

테이블에 들어가있는 모든 데이터를 삭제할땐 `DELETE FROM customers;`이다.



### 정리

#### 쿼리 정리

| 쿼리                                                         | 설명             |
| ------------------------------------------------------------ | ---------------- |
| CREATE SCHEMA [DB name] CHARACTER SET [character set];       | 스키마(DB) 생성  |
| USE [DB name];                                               | 스키마(DB) 사용  |
| DROP DATABASE [DB name];                                     | 스키마(DB) 삭제  |
| CREATE TABLE [table name] ([column name] [data type], ...);  | 테이블 생성      |
| DROP TABLE [table name];                                     | 테이블 삭제      |
| ALTER TABLE [table name] ADD [column name] [data type];      | 열 추가          |
| INSERT INTO [table name] VALUES (value1, value2, ...);       | 행 추가          |
| SELECT * FROM [table name];                                  | 모든 레코드 조회 |
| SELECT (column1, column2) FROM [table name];                 | 일부 레코드 조회 |
| UPDATE [table name] SET [column] = [value] WHERE [condition]; | 행 값 수정       |
| DELETE FROM [table name] WHERE [condition];                  | 행 값 삭제       |



#### 데이터 타입 정리

| 데이터 타입    | 설명                                    |
| -------------- | --------------------------------------- |
| CHAR(n)        | 고정 길이 문자열 표현(최대 255바이트)   |
| VARCHAR(n)     | 가변 길이 문자열 표현(최대 65536바이트) |
| TEXT(n)        | 긴 문자열 표현 (최대 65535바이트)       |
| INT(n)         | 정수형 표현 (8바이트)                   |
| FLOAT(n, 소수) | 부동 소수형 표현 (4바이트)              |
| DOUBLE(n,소수) | 부동 소수형 표현 (8바이트)              |
| DATE(n)        | 날짜(년도, 월, 일) 표현 (3바이트)       |
| DATETIME(n)    | 날짜와 시간 함께 표현 (8바이트)         |
| TIME(n)        | 시간(시,분,초) 표현 (3바이트)           |
| TIMESTAMP(n)   | 시간과 날짜 표현 (8바이트)              |
| YEAR(n)        | 년도 표현 (1바이트)                     |



#### 제약조건 정리

| 제약조건                                         | 설명                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| NOT NULL                                         | 필드에 NULL 값 가질 수 없다                                  |
| UNIQUE                                           | 필드 값이 고유해야 한다                                      |
| PRIMARY KEY                                      | 기본 키를 지정한다                                           |
| FOREIGN KEY                                      | 외래 키를 지정한다                                           |
| DEFAULT                                          | 값이 들어오지 않았을때 기본값 지정                           |
| REFERENCES 참조할 테이블 명<br />(참조할 컬럼명) | 관계를 맺을 테이블과 컬럼을 지정<br />ON DELETE, ON UPDATE옵션 :<br />- RESTRICT : 참조하는 테이블의 컬럼이 삭제되도 유지<br />- CASCADE : 참조하는 테이블의 컬럼이 삭제되면 함께 삭제<br />- SET NULL : 참조하는 테이블의 컬럼이 삭제되면 null로 변경<br />- NO ACTION : 참조하는 테이블의 컬럼이 삭제되도 무시<br />- SET DEFAULT : 참조하는 테이블의 컬럼이 삭제되면 지정 값으로 대체 |



참조 : Node.js로 서버 만들기 (저자 박민경)