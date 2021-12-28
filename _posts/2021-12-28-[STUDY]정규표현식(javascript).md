---
layout: post
title: STUDY-정규표현식(javascript)
subtitle: javascript에서 정규표현식
categories: study
tags: [javascript]




---



## STUDY-정규표현식(javascript)



### 정규표현식이란?

정규표현식은 문자열에 나타나는 **특정 문자 조합과 대응**시키기 위해 사용하는 패턴이다. JS에서 정규표현식은 객체이다. (정규표현시은 정규식이라고도 한다)



### 어떤 메소드와 함께 쓰이는가?

| 객체   | 메소드명           | 메소드 설명                                                  |
| :----- | ------------------ | ------------------------------------------------------------ |
| RegExp | **exec** 메소드    | 대응되는 문자열 찾는 메소드<br />정보를 가지고 있는 배열을 반환한다<br />대응되는 문자열을 못찾으면 null을 반환한다 |
| RegExp | **test** 메소드    | 대응되는 문자열 있는지 검사하는 메소드<br />true나 false 반환한다 |
| String | **match** 메소드   | 대응되는 문자열 찾는 메소드<br />정보를 가지고 있는 배열 반환한다<br />대응되는 문자열 못찾으면 null을 반환한다 |
| String | **replace** 메소드 | 대응되는 문자열 찾아 다른 문자열로 치환한다                  |
| String | **split** 메소드   | 정규식 혹은 문자열로 대상 문자열을 나누어 배열로 반환한다    |



### 정규 표현식 만들기

정규식을 만드는 방법은 두가지가 있다.

첫번째는 _정규식 리터럴_을 사용하는 방법이다. 이는 스크립트가 불러와질때 컴파일 된다. 만약 정규식이 상수라면 이 방법으로 사용하는 것이 성능을 향상시킬 수 있다.

```javascript
var re = /ab+c/;
```

두번째는 _RegExp 객체의 생성자 함수_를 호출하는 방법이다. 이는 정규식이 실행 시점에 컴파일 된다. 정규식의 패턴이 변경될 수 있는 경우, 혹은 사용자 입력과 같이 다른 출처로부터 패턴을 가져와야 하는 경우에 사용하는 것이 좋다.

```javascript
var re = new RegExp("ab+c");
```



### 정규식에서 특수 문자

| 특수문자 | 의미                                                         |
| -------- | ------------------------------------------------------------ |
| \        | 특수문자가 아닌 문자 앞에서 사용된 백슬래시는 **해당 문자는 특별하고, 문자 그대로 해석되면 안된다**는 사실을 가리킨다.<br />('b'는 보통 소문자 b가 나오는 패턴을 의미한다. 그러나 '\b'는 어떤 문자와도 대응되지 않는다.)<br />특수 문자 앞에 위치한 백슬래시는 **해당 문자는 특별하지 않고 문자 그대로 해석되어야 한다**는 사실을 가리킨다. (/a*/에서의 특수문자 * 은 0개이상의 a문자가 등장함을 의미한다. 하지만 /a\\*/는 *이 특별하지 않다는 것을 나타내며 a*와 같은 문자열과 대응된다.) |
| ^        | 입력의 시작 부분에 대응된다.                                 |
| $        | 입력의 끝 부분에 대응된다.                                   |
| *        | 앞의 표현식이 0회 이상 연속으로 반복되는 부분과 대응된다.<br />{0,}과 같은 의미 |
| +        | 앞의 표현식이 1회 이상 연속으로 반복되는 분분과 대응된다<br />{1,}와 같은 의미 |
| ?        | 앞의 표현식이 0회 또는 1회 등장하는 부분과 대응된다<br />{0,1}와 같은 의미 |
| .        | 개행 문자를 제외한 모든 단일문자와 대응된다                  |
| (x)      | x문자에 대응되고 그것을 기억한다. 괄호는 **포획 괄호**라 불린다. |
| x\|y     | x 또는 y 에 대응된다                                         |
| {n}      | 앞 표현식이 n번 나타나는 부분에 대응된다. (n은 반드시 양의 정수여야 한다) |
| {n,m}    | n과 m은 양의 정수이고, n <= m 이어야 한다.<br />앞 문자가 최소 n개, 최대 m개 나타나는 부분에 대응된다. (m이 생략되면 무한대로 취급된다) |
| [xyz]    | 문자셋. 괄호 안의 어떤 문자와도 대응된다.<br />[a-d]는 [abcd]와 똑같이 동작한다. |
| [^xyz]   | 부정 문자셋. 괄호 내부에 등장하지 않는 어떤 문자와도 대응된다.<br />[ ^abc ]는 [ ^a-c ] 와 동일하다. |
| \b       | 단어 경계에 대응된다. 단어의 경계는 대응 결과에 포함되지 않는다. |
| \B       | 단어 경계가 아닌 부분에 대응된다.                            |
| \d       | 숫자에 대응된다. [ 0-9 ]와 동일하다.                         |
| \D       | 숫자가 아닌 문자에 대응된다. [ ^0-9 ]와 동일하다.            |
| \f       | 폼피드 문자에 대응된다.                                      |
| \n       | 줄 바꿈 문자에 대응된다.                                     |
| \r       | 캐리지 리턴 문자에 대응된다.                                 |
| \s       | 스페이스, 탭, 폼피드, 줄바꿈 문자 등을 포함한 하나의 공백 문자에 대응된다. |
| \S       | 공백 문자가 아닌 하나의 문자에 대응된다.                     |
| \t       | 탭 문자에 대응된다.                                          |
| \v       | 수직 탭 문자에 대응된다.                                     |
| \w       | 밑줄 문자를 포함한 영숫자에 대응된다. [A-Za-z0-9_]와 동일하다.<br />여기에 대응되는 문자를 단어문자라고 한다. |
| \W       | 단어문자가 아닌 문자에 대응된다. [ ^A-Za-z0-9_ ]와 동일하다. |
| \0       | 널 문자에 대응한다.                                          |
| \xhh     | 코드가 hh((두 16진숫자)인 문자에 일치한다.                   |
| \uhhhh   | 코드가 hhhh(네개의 16진숫자)인 문자에 일치한다.              |



### 플래그

정규식은 여섯개의 플래그를 설정해줄 수 있다. 이를 통해 전역검색 또는 대소문자 구분 없는 검색을 수행할 수 있다. 이 플래그들은 각기 사용될수 있고 함께 사용될수도 있고 순서에 구분이 없다.

| 플래그 | 설명                                                         |
| ------ | ------------------------------------------------------------ |
| g      | 전역 검색                                                    |
| i      | 대소문자 구분 없는 검색                                      |
| m      | 다중행 검색                                                  |
| s      | . 에 개행 문자도 매칭                                        |
| u      | 유니코드. 패턴을 유니코드 포인트의 나열로 취급한다.          |
| y      | "sticky"검색을 수행. 문자열의 현재 위치부터 검색을 수행한다. |

```javascript
var re = /pattern/flags;

var re = new RegExp("pattern", "flags");
```



### 예시 코드

```javascript
var reg = /^A/; // 맨앞에 위치해있는지
console.log('An E'.replace(reg, '?')); // ?n E 
console.log('an A'.replace(reg, '?')); // an A 

reg = /t$/; // 맨뒤에 위치해있는지
console.log('eater'.replace(reg,'?')); // eater
console.log('eat'.replace(reg,'?')); //ea?

reg = /bo*/g; // o문자가 0회이상 연속으로 반복되는지
console.log('A ghost boooed'.replace(reg, '?')); // A ghoast ?ed
console.log('A bird warbled'.replace(reg, '?')); // A ?ird war?led
console.log('A goat grunted'.replace(reg, '?')); // A goat grunted

reg = /a+/; // a문자가 1회 이상 연속 등장하는지
console.log('candy'.replace(reg,'?')); // c?ndy
console.log('caaaaandy'.replace(reg,'?')); //c?ndy
reg = /bo*/;
console.log('cboboc'.replace(reg,'?')); //c?boc
reg = /bo*/g;
console.log('cboeboc'.replace(reg,'?')); // c?e?c

reg = /e?le?/; // 0 또는 1회 등장하는지
console.log('angel'.replace(reg,'?')); // ang?
console.log('angle'.replace(reg,'?'));  //ang?
console.log('oslo'.replace(reg, '?')) //os?o

reg = /.n/g; // 개행문자 제외한 모든 단일문자
console.log('nay, an apple is ons the tree'.replace(reg, '?'));
// nay, ? apple is ? the tree

reg = /green|red/; // green 또는 red에 대응되는지
console.log('green apple'.replace(reg,'?')); // ? apple
console.log('red apple'.replace(reg,'?')); // ? apple

reg = /a{2}/; // a가 2번 나타나는지
console.log('caaandy'.replace(reg,'?')); //c?andy

reg = /a{1,3}/; // a가 최소 1번, 최대 3번 나타나는지
console.log('caandy'.replace(reg,'?')); // c?ndy
console.log('caaaaaaaandy'.replace(reg,'?')); // c?aaaaandy

reg = /[a-d]/g ; // == [abcd] 와 똑같이 동작
console.log('citd'.replace(reg,'?')); //?it?

reg=/[a-z.]+/; // == /[\w.]+/
console.log('test.i.ng'.replace(reg, '?')); //?

reg=/[^a-c]/g; // == /[^a-c]/
console.log('brisket'.replace(reg, '?')); //b??????

reg=/\bm/; // m이 제일 앞에 있고 단어 경계에 있는지
console.log('moon'.replace(reg,'?')); //?oon
reg=/oo\b/; // oo가 제일 뒤에 있고 단어 경계에 있는지
console.log('moon'.replace(reg,'?')); //moon
reg=/oon\b/; // oon이 제일 뒤에 있고 단어 경계에 있는지
console.log('moon'.replace(reg, '?')); //m?
reg=/\boon/;
console.log('moon'.replace(reg, '?')); //moon
reg = /\w\b\w/; // 어떤것도 일치하지 않는다. 단어 문자는 절대로 비단어문자와 단어문자 두개가 뒤따라올 수 없기때문

reg=/\B../;
console.log('noonday'.replace(reg,'?')); //n?nday

reg=/\d/; // == /[0-9]/
console.log('B2 is the'.replace(reg,'?')); // B? is the

reg=/\D/; // == /[^0-9]/
console.log('B23 is the'.replace(reg,'?')); //?23 is the

reg=/\w/; // 밑줄문자 포함한 영숫자문자 ==/[A-Za-z0-9_]/
console.log('apple'.replace(reg,'?')); //?pple
console.log('$5.28,'.replace(reg,'?')); //$?.28,

reg=/\W/; // == /[^A-Za-z0-9_]/
console.log('50%'.replace(reg,'?')); //50?

var myArray = /d(b+)d/g.exec('cdbbdbsbz');
console.log(myArray); // ['dbbd', 'bb', index:1, input : 'cdbbdbsbz', groups : undefined]
```



어렵다.🥲

