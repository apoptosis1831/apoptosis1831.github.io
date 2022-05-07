---
layout: post
title: "[STUDY] MySQL 종합공부"
subtitle: select 사용하여 프로그래머스 전 문제 풀기
categories: study
tags: [mysql]
typora-root-url: ../




---

## [STUDY] MySQL 종합공부

### 테이블 및 데이터 정보

* 1번 테이블 (ANIMAL_INS)
  - 테이블 구조
  ![테이블1 구조](/assets/images/etc/ANIMAL_INS_structure.jpg)
* 2번 테이블 (ANIMAL_OUTS)
  - 테이블 구조
  ![테이블2 구조](/assets/images/etc/animal_outs.jpg)

### SELECT로 원하는 항목만 출력, 데이터 정렬

```mysql
SELECT NAME, DATETIME FROM ANIMAL_INS
ORDER BY ANIMAL_ID DESC;
```

`SELECT [컬럼명1], [컬럼명2] FROM [테이블명]` : 테이블에서 해당 컬럼만 출력

`ORDER BY [컬럼명1] [ASC | DESC], [컬럼명2] [ASC | DESC];` : 데이터 정렬. ASC(오름차순), DESC(내림차순) 을 의미하며 ASC는 생략가능하다. 첫번째 조건에 해당하는 값이 여러개일때 두번째 조건으로 검색할 수 있다. (순서대로 적으면 된다)

### 조건문 달아서 검색

```mysql
SELECT ANIMAL_ID, NAME FROM ANIMAL_INS
WHERE INTAKE_CONDITION="Sick"
ORDER BY ANIMAL_ID;
```

`WHERE [조건이 필요한 컬럼] = [해당 값]` : 컬럼에 해당 값이 들어가있는 데이터만 출력한다. 해당 값이 아닐 경우를 출력할때에는 `!=` 사용한다.

### LIMIT 으로 출력 데이터 개수 제한하기

```mysql
SELECT NAME FROM ANIMAL_INS
ORDER BY DATETIME LIMIT 1;
```

`LIMIT [개수(숫자)]` : 지정한 개수만큼만 데이터를 가져올 수 있다.

`LIMIT [OFFSET] [개수]` : OFFSET 인덱스 부터 개수 만큼 가져온다.

### COUNT로 데이터 개수 출력하기

```mysql
SELECT COUNT(*) as count FROM ANIMAL_INS;
```

`SELECT COUNT(*) FROM [TABLE명]` : 모든 행 개수 출력한다.

`SELECT COUNT(DISTINCT [컬럼명]) FROM [테이블명]` : DISTINCT(중복값 제외한 행의 개수), 여기서 NULL값은 집계가 안된다.

### GROUP BY 로 그룹화하기

```mysql
SELECT ANIMAL_TYPE, COUNT(*) as count FROM ANIMAL_INS
GROUP BY ANIMAL_TYPE
ORDER BY ANIMAL_TYPE;
```

`SELECT COUNT(*) FROM [테이블명] GROUP BY [칼럼명]` : 컬럼으로 그룹화된 데이터들의 개수 출력한다.

### HAVING 으로 그룹 조건 처리하기

```mysql
SELECT NAME, COUNT(NAME) as COUNT FROM ANIMAL_INS
GROUP BY NAME
HAVING count(NAME)>1
ORDER BY NAME;
```

`HAVING [조건]` : GROUP BY 한 결과에 조건을 붙여 데이터를 필터링한다.

```mysql
SELECT HOUR(DATETIME) AS HOUR, COUNT(DATETIME) FROM ANIMAL_OUTS
GROUP BY HOUR(DATETIME) HAVING HOUR>=9 && HOUR<=19
ORDER BY HOUR(DATETIME);
```

`DATETIME` 의 TYPE에 해당하는 데이터들은 `HOUR(DATETIME)`의 형식으로 시각만 가져올 수 있다.

### 입양 시각 구하기(2) 문제

0시부터 23시까지 각 시간대별로 입양이 몇건이나 발생했는지 조회하는 SQL문(결과는 시간대 순으로 정렬)은 아래와 같다.

```mysql
WITCH RECURSIVE Y as(
SELECT ROW_NUMBER() OVER(ORDER BY NAME) -1 as hour
FROM ANIMAL_OUTS
LIMIT 24)
SELECT HOUR, COUNT(ANIMAL_ID) as count
FROM Y
LEFT JOIN ANIMAL_OUTS
ON DATE_FORMAT(DATETIME, "%H)=Y.HOUR
GROUP BY HOUR
ORDER BY HOUR;
```

`WITH RECURSIVE` : 메모리 상에 가상 테이블을 저장해준다. 재귀 쿼리 이용해 가상 테이블을 만든다.

`ROW_NUMBER() OVER (정렬기준)` : 정렬기준에 따른 행번호를 반환한다.

`DATE_FORMAT(DATETIME, "%H")` : `HOUR(DATETIME)`로 대체 가능하다. (둘다 같은 의미) DATETIME의 시각만 반환한다.

### IS NULL

```mysql
SELECT ANIMAL_ID FROM ANIMAL_INS
WHERE NAME IS NULL
ORDER BY ANIMAL_ID;
```

NAME 의 값이 NULL일때 `IS NULL` 사용한다. NULL이 아닌 조건은 `IS NOT NULL` 사용한다.

### 값이 NULL일 경우 IFNULL로 default값 지정

```mysql
SELECT ANIMAL_TYPE, IFNULL(NAME,"No name"), SEX_UPON_INTAKE
FROM ANIMAL_INS
ORDER BY ANIMAL_ID;
```

`SELECT IFNULL ([컬럼명], [NULL값일 때 대체할 값])` : 컬럼명에 해당하는 값이 NULL일때 두번째 매개변수로 출력한다.

### JOIN

입양을 간 기록은 있는데, 보호소에 들어온 기록이 없는 동물의 ID와 이름을 ID순으로 조회하는 SQL문은 아래와 같다.

```mysql
SELECT ANIMAL_OUTS.ANIMAL_ID, ANIMAL_OUTS.NAME
FROM ANIMAL_OUTS
LEFT JOIN ANIMAL_INS
ON ANIMAL_OUTS.ANIMAL_ID = ANIMAL_INS.ANIMAL_ID
WHERE ANIMAL_INS.ANIMAL_ID IS NULL;
```

`LEFT JOIN` : A와 B 테이블 중에 A값 전체와 A의 KEY값과 B의 KEY값이 같은 결과를 리턴한다.

![SQL JOIN 정리](/assets/images/etc/SQL_JOINS.jpg)

### JOIN, WHERE 조합 쿼리

보호소에 들어올 당시에는 중성화 되지 않았지만, 보호소를 나갈 당시에는 중성화된 동물의 아이디와 생물 종, 이름을 조회하는 아이디 순으로 조회하는 SQL문은 아래와 같다.

```mysql
SELECT ANIMAL_OUTS.ANIMAL_ID, ANIMAL_OUTS.ANIMAL_TYPE, ANIMAL_OUTS.NAME
FROM ANIMAL_OUTS
INNER JOIN ANIMAL_INS
ON ANIMAL_OUTS.ANIMAL_ID = ANIMAL_INS.ANIMAL_ID
WHERE ANIMAL_INS.SEX_UPON_INTAKE like 'Intact%'
and (ANIMAL_OUTS.SEX_UPON_OUTCOME like 'Spayed%' or
     ANIMAL_OUTS.SEX_UPON_OUTCOME like 'Neutered%')
ORDER By ANIMAL_INS.ANIMAL_ID;
```

### WHERE ~~ IN (A, B, C)

```myssql
SELECT ANIMAL_ID, NAME, SEX_UPON_INTAKE
FROM ANIMAL_INS
WHERE NAME IN ('Lucy','Ella','Pickle','Rogan','Sabrina','Mitty')
ORDER BY ANIMAL_ID;
```

`WHERE [컬럼명] IN ('값1', '값2' ...)` : 괄호안에 들어간 값이 포함하는 데이터만 추출한다.

### CASE WHEN 문으로 조건에 따라 출력 다르게 하기

중성화된 동물은 `SEX_UPON_INTAKE` 컬럼에 'Neutered' 떠는 'Spayed'라는 단어가 들어가 있다. 동물의 아이디와 이름, 중성화 여부를 아이디 순으로 조회하려고 하며 이때 중성화가 되어 있다면 'O', 아니면 'X'라고 표시한다.

```mysql
SELECT ANIMAL_ID, NAME,
CASE WHEN SEX_UPON_INTAKE LIKE "%intact%" THEN 'X'
ELSE 'O'
END AS '중성화'
FROM ANIMAL_INS
ORDER BY ANIMAL_ID;
```

`CASE WHEN [조건1] THEN [반환 값1] WHEN [조건2] THEN [반환 값2] ... ELSE [WHEN조건에 해당 안되는 경우의 반환 값]`으로 CASE WHEN 사용 가능하다.

### 위의 문제를 IF로 풀어보자

```mysql
SELECT ANIMAL_ID, NAME,
IF(SEX_UPON_INTAKE LIKE "%intact%", "X", "O") as 중성화
FROM ANIMAL_INS
ORDER BY ANIMAL_ID;
```

`IF([조건문], [참일 때 값], [거짓일 때 값]) as [속성 제목]`

### DATE_FORMAT 으로 DATETIME 여러 형식으로 나타내기

```mysql
SELECT ANIMAL_ID, NAME, DATE_FORMAT(DATETIME, "%Y-%m-%d") as '날짜'
FROM ANIMAL_INS
ORDER BY ANIMAL_ID;
```


`DATE_FORMAT(날짜, 형식)` : 날짜를 지정한 형식으로 출력한다.

![DATE_FORMAT 구분기호](/assets/images/etc/date_format.jpg)



---

참조 :

[구글링 블로그1](https://yoo-hyeok.tistory.com/98)

[구글링 블로그2](https://devjhs.tistory.com/89)