---
layout: post
title: "[STUDY] javascript 함수 (map,filter,reduce,slice,구조분해할당,화살표함수)"
subtitle: javascript 내 다양한 함수
categories: study
tags: [javascript]




---



## [STUDY] javascript 함수 (map,filter,reduce,slice,구조분해할당,화살표함수)



### map함수 (Array.prototype.map())

map() 메서드는 배열 내의 모든 요소 각각에 대하여 주어진 함수를 호출한 결과를 모아 새로운 배열을 반환한다.

#### 예제 코드

```javascript
const array1 = [1,4,9,16];
const map1 = array1.map(x=>x*2);
console.log(map1);
// Array [2,8,18,32]
console.log(array1);
// Array [1,4,9,16]
```

#### 구문

> arr.map(callback(currentValue[, index[, array]])[, thisArg])

- callback : 새로운 배열 요소를 생성하는 함수이고 세가지 인수를 가진다.
    - currentValue : 처리할 현재 요소
    - index(optional) : 처리할 현재 요소의 인덱스
    - array(optional) : map()을 호출한 배열
- thisArg(optional) : callback을 실행할 때 this로 사용되는 값

> 반환값 : 배열의 각 요소에 대해 실행한 callback의 결과를 모은 새 배열

#### 설명

map은 callback 함수를 각각의 요소에 대해 한번씩 순서대로 불러 그 함수의 반환값으로 새로운 배열을 만든다. callback 함수는 (undefined도 포함해서) 배열 값이 들어있는 인덱스를 호출한다. 즉, 값이 삭제되거나 아직 값이 할당/정의되지 않는 인덱스에 대해서는 호출되지 않는다.

callback 함수는 호출될 때 대상 요소의 값, 그 요소의 인덱스, 그리고 map을 호출한 원본 배열 3개의 인수를 전달받는다.

thisArg 매개변수가 map에 전달된 경우 callback 함수의 this 값으로 사용된다.

map이 처리할 요소의 범위는 첫 callback을 호출하기 전에 정해진다. map이 시작한 이후 배열에 추가되는 요소들은 callback을 호출하지 않는다. 배열에 존재하는 요소들의 값이 바뀐 경우 map이 방문하는 시점의 값이 callback에 전달된다. map이 시작되고, 방문하기 전에 삭제된 요소들은 방문하지 않는다.


#### 예제 코드

```javascript
var kvArray = [ {key:1, value:10},
                {key:2, value:20},
                {key:3, value:30}];
var reformattedArray = kvArray.map(function(obj){
    var rObj = {};
    rObj[obj.key] = obj.value;
    return obj;
});

console.log(reformattedArray);
// [{1:10},{2:20},{3:30}]
```

> 오브젝트의 배열을 받아 각 오브젝트를 다른 형태로 재구성해 새로운 배열을 만든다.

```javascript
var numbers = [1,4,9];
var doubles = numbers.map(function(num){
    return num*2;
})

console.log(doubles);
// [2,8,18]
```

> 인자가 1개인 함수 이용해 숫자 배열 재구성한다. 배열과 안의 요소들은 map을 통해 순회하면서 원본 배열로부터 자동으로 할당된다.

```javascript
['1','2','3'].map(parseInt);
// [1, NaN, NaN]

function returnInt(element){
    return parseInt(element, 10);
}

['1','2','3'].map(returnInt);
// [1, 2, 3]
['1','2','3'].map(str => parseInt(str));
// [1, 2, 3]
['1','2','3'].map(Number);
// [1, 2, 3]
['1.1','2.2e2','3e300'].map(Number);
// [1.1, 220, 3e+300]
```

> 위의 코드에서 실행결과 [1, Nan, Nan] 이 나온 이유가 뭘까?
>
> pareInt 함수는 보통 하나의 인자만 사용하지만, 두 개도 받을 수 있다. 첫 번째 인자는 **변환하고자 하는 표현**이고, 두 번째는 숫자로 변환할 때 사용할 **진법**이다.
>
> Array.prototype.map은 콜백에 세가지 인자를 전달한다. (배열의 값, 값의 인덱스, 배열) 세번째 인자는 parseInt가 무시하지만 두번쨰 인자는 아니다.





### filter함수 (Array.prototype.filter())

filter() 메서드는 주어진 함수의 테스트를 통과하는 모든 요소를 모아 새로운 배열로 반환한다.

#### 예제 코드

```javascript
const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];
const result = words.filter(word => word.length>6);

console.log(result);
// Array ["exuberant", "destruction", "present"]
```

#### 구문

> arr.filter(callback(element[,index [, array]])[, thisArg])

- callback : 각 요소를 시험할 함수. true를 반환하면 요소를 유지하고, false를 반환하면 버린다. 밑에 세가지 매개변수를 받는다.
    - element : 처리할 현재 요소
    - index(optional) : 처리할 현재 요소 인덱스
    - array(optional) : filter를 호출한 배열
- thisArg(optional) : callback을 실행할 때 this로 사용하는 값

> 반환값 : 테스트를 통과한 요소로 이루어진 새로운 배열. 어떤 요소도 테스트를 통과하지 못했으면 빈 배열을 반환한다.

#### 설명

filter()는 배열 내 각 요소에 대해 callback 함수를 호출해 callgack이 true로 반환하는 모든 값을 새로운 배열로 생성한다. callback은 할당된 값이 있는 배열의 인덱스에 대해서만 호출한다. 삭제되거나 값이 할당된 적 없는 인덱스에 대해선 호출하지 않는다. callback 테스트를 통과하지 못한 배열 요소는 그냥 건너뛰며 새로운 배열에 포함되지 않는다.

filter()는 호출되는 배열을 변화시키지 않는다.

filter()에 의해 처리되는 요소의 범위는 callback의 첫 호출 전에 설정된다. 호출 시작 이후로 배열에 추가된 요소는 callback에 의해 방문되지 않는다. 배열의 기존 요소가 변경, 삭제된 경우, callback에 전달된 그 값은 filter()가 그 요소를 방문한 시점에 값이 된다. 삭제된 요소는 반영되지 않는다.

#### 예제 코드

```javascript
function isBigEnough(value){
    return value >= 10;
}
var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
// [12, 130, 44]
```

```javascript
var fruits = ['apple', 'banana', 'grapes', 'mango', 'orange'];

function filterItems(query){
    return fruits.filter(function(el){
        return el.toLowerCase().indexOf(query.toLowerCase()) > -1 ;
    })
}

console.log(filterItems('ap'));
// ['apple', 'grapes']
console.log(filterItems('an'));
// ['banana', 'mango', 'oragne']
```

위의 코드를 아래와 같이 표현할 수도 있다.

```javascript
var fruits = ['apple', 'banana', 'grapes', 'mango', 'orange'];

const filterItems = (query) => {
    return fruits.filter((el) => 
        el.toLowerCase().indexOf(query.toLowerCase()) > -1
    );
}

console.log(filterItems('ap'));
// ['apple', 'grapes']
console.log(filterItems('an'));
// ['banana', 'mango', 'oragne']
```




### reduce함수 (Array.prototype.reduce())

recude() 메서드는 배열의 각 요소에 대해 주어진 **리듀서(reducer)**함수를 실행하고, 하나의 결과값을 반환한다.

#### 예제 코드

```javascript
const array1 = [1,2,3,4];
const reducer = (previosValue, currentValue) => previousValue + currentValue;

console.log(array1.reduce(redeucer));
// 10
console.log(array1.reduce(reducer, 5));
//15
```

#### reducer함수

reducer함수는 4개의 인자를 가진다.

1. 누산기(acc)
2. 현재 값(cur)
3. 현재 인덱스(idx)
4. 원본 배열(src)

리듀서 함수의 반환 값은 누산기에 할당되고, 누산기는 순회 중 유지되므로 결국 최종 결과는 하나의 값이 된다.

#### 구문

> arr.reduce(callback[, initialValue])

- callback : 배열의 각 요소에 대해 실행할 함수. 아래의 4가지 인수를 받는다.
    - accumulator : 누산기는 콜백의 반환값을 누적한다. 콜백의 이전 반환값 또는, 콜백의 첫번째 호출이면서 initialValue를 제공한 경우엔 initialValue의 값이다.
    - currentValue : 처리할 현재 요소
    - currentIndex(optional) : 처리할 현재 요소의 인덱스. initialValue를 제공한 경우 0 아니면 1부터 시작한다.
    - array(optional) : reduce()를 호출한 배열
- initialValue(optional) : callback의 최초 호출에서 첫번째 인수에 제공하는 값. 초기값을 제공하지 않으면 배열의 첫번째 요소를 사용한다. 빈 배열에서 초기값 없이 reduce()를 호출하면 오류 발생한다.

> 반환값 : 누적계산의 결과 값

#### 설명

reduce()는 빈 요소를 제외하고 배열 내에 존재하는 각 요소에대해 callback 함수를 한번씩 실행하는데, 콜백 함수는 다음의 네 인수를 받는다. (`accumulator`, `currentValue`, `currentIndex`, `array`)

콜백 최초 호출 때 accumulator와 currentValue는 다음 두가지 값 중 하나를 가질 수 있다. 만약 reduce() 함수 호출에서 initialValue를 제공한 경우, accumulator는 initialValue와 같고, currentValue는 배열의 첫번째 값과 간다.

initialValue를 제공하지 않았다면 accumulator는 배열의 첫번째 값과 같고 currentValue는 두번째와 같다.

> 참고 : initialValue를 제공하지 않으면 reduce()는 인덱스 1부터 시작해 콜백함수를 실행하고 첫번째 인덱스는 건너뛴다.
>
> initialValue를 제공하면 인덱스 0에서 시작한다.

배열이 비었는데 initialValue도 제공하지 않으면 TypeError가 발생한다. 배열의 요소가 하나 뿐이면서 initalValue를 제공되지 않은 경우, 또는 initialValue는 주어졌으나 배열이 빈 경우엔 그 단독값을 callback 호출 없이 반환한다.

```javascript
var maxCallback = (acc, cur) => Math.max(acc.x , cur.x);
var maxCallback2 = (max, cur) => Math.max(max,cur);

// initialValue없이 reduce()
[ {x:22}, {x:42} ].reduce(maxCallback);
//42
[ {x:22} ].reduce(maxCallback);
// {x:22} // callback 호출없이 반환

// 비었거나 더 큰 배열에서도 동작함
[ {x:22}, {x:42} ].map(el => el.x)
                  .reduce(maxCallback2, -Infinity);
```

### 예제 코드

```javascript
var sum = [0,1,2,3].reduce(function(accumulator,currentValue) {
    return accumulator+currentValue;
}, 0);
// sum is 6
```

화살표 함수로도 작성할 수 있다.

```javascript
var total = [0,1,2,3].reduce(
    (accumulator, currentValue) => accumulator + currentValue, 0
);
```

```javascript
var initialValue = 0;
var sum = [{x:1},{x:2},{x:3}].reduce(function(accumulator, currentValue){
    return accumulator+currentValue.x;
}, initialValue)

console.log(sum) // 6
```

> 객체로 이루어진 배열에 들어 있는 값을 합산하기 위해서는 반드시 초기값을 주어 각 항목이 함수를 거치도록 해야한다.

아래와 같이 화살표 함수로도 작성할 수 있다.

```javascript
var initialValue = 0;
var sum = [{x:1}, {x:2}, {x:3}].reduce(
    (accumulator, currentValue) => accumulator + currentValue.x, initialValue
);

console.log(sum) //6
```

* 중첩 배열 펼치기

```javascript
var flattened = [[0,1],[2,3],[4,5]].reduce(
    function(accumulator, currentValue){
        return accumulator.concat(currentValue);
    },
    []
);
// [0,1,2,3,4,5]
```

화살표 함수로 변환하면 아래와 같다

```javascript
var flattened = [0,1],[2,3],[4,5]].reduce(
    (accumulator, currentValue) => accumulator.concat(currentValue), []
);
```

* 객체 내의 값 인스턴스 개수 세기

```javascript
var names = ['Alice', 'Bob', 'Tiff', 'Bruce', 'Alice']
var countedNames = names.reduce(function(allNames, name){
    if(name in allNames){
        allNames[name]++;
    }
    else{
        allNames[name]=1;
    }
    return allNames;
}, {});

// countedName :
// {'Alice':2 , 'Bob':1, 'Tiff':1, 'Bruce':1}
```

* 속성으로 객체 분류하기

```javascript
var people = [
    { name : 'Alice', age : 21 },
    { name : 'Max', age : 20 },
    { name : 'Jane', age : 20}
];

function groupBy(objectArray, property){
    reeturn objectArray.reduce(function(acc,obj){
        var key = obj[property];
        if(!acc[key]){
            acc[key] = [];
        }
        acc[key].push(obj);
        return acc;
    }, {});
}

var groupedPeople = groupBy(people, 'age');
// groupedPeople
// { 20 : [ {name:'Max', age:20}, {name:'Jane', age:20}] ,
//   21 : [ {name:'Alice', age:21}]
```

* 확장 연산자와 초기값을 이용하여 객체로 이루어진 배열에 담긴 배열 연결하기

```javascript
var friends = [{
    name: 'Anna',
    books: ['Bible','Harry Potter'],
    age : 21
}, {
    name: 'Bob',
    books: ['war and peace', 'Romeo and Juliet'],
    age: 26
}, {
    name : 'Alice',
    books: ['The Lord of the rings', 'The shining'],
    age: 18
}];

var allbooks = friends.reduce(function(accumulator, currentValue){
    return [...accumulator, ...currentValue.books];
}, ['Alphabet']);

/* allbooks =[
    'Alphabet', 'Bible', 'HarryPotter', 'War and peace', 'Romeo and Juliet', 'The Lord of the Rings', 'The Shining'
]*/
```

* 배열의 중복 항목 제거

> 참고 : Set과 Array.from() 을 사용할 수 있는 환경이라면,
>
> `let orderedArray = Array.from(new Set(myArray));`을 사용해 중복 요소를 제거할 수도 있다.

```javascript
let arr = [1,2,1,2,3,5,4,5,3,4,4,4,4];
let result = arr.sort().reduce((accumulator, current)=> {
    const length = accumulator.length
    if(length ===0 || accumulator[length-1] !== current){
        accumulator.push(current);
    }
    return accumulator;
}, []);
console.log(result) // [1,2,3,4,5]
```



### slice함수 (Array.prototype.slice())

slice() 메서드는 어떤 배열의 begin부터 end까지 (end 미포함) 얕은 복사본을 새로운 배열 객체로 반환한다. 원본 배열은 바뀌지 않는다.

#### 예제 코드

```javascript
const animals = ['ant', 'bison', 'camel', 'duck', 'elephant'];

console.log(animals.slice(2));
// ["camel", "duck", "elephant"]

console.log(animals.slice(2,4));
// ["camel", "duck"]

console.log(animals.slice(1,5));
// ["bison", "camel", "duck", "elepahnt"]

console.log(animals.slice(-2));
// ["duck", "elepahnt"]
// 배열에서 마지막 두 개의 element를 추출

console.log(animals.slice(2, -1));
// ["camel", "duck"]
// 세번째부터 끝에서 두번째 요소까지 추출
```

#### 구문

> arr.slice([begin[, end]])

- begin(optional) : 0을 시작으로 하는 추출 시작점에 대한 인덱스. 음수 인덱스는 배열의 끝에서부터의 길이를 나타낸다. undefined인 경우 0번 인덱스부터 slice한다. begin이 배열의 길이보다 큰 경우 빈 배열을 반환한다.
- end(optional) : 추출을 종료할 0 기준 인덱스이다. slice는 end 인덱스를 제외하고 추출한다. 음수 인덱스는 배열의 끝에서부터 길이를 나타낸다. end가 생략되면 slice()는 배열의 끝까지(arr.length) 추출한다. 만약 end 값이 배열 길이보다 크면 배열의 끝(arr.length)까지 추출한다.

> 반환값 : 추출한 요소를 포함한 새로운 배열

#### 설명

slice()는 원본 배열에서 요소의 얕은 복사본을 반환한다.

객체 참조(및 실제 객체가 아님)의 경우, 객체 참조를 새 배열로 복사한다. 원본 배열과 새 배열은 모두 동일한 객체를 참조한다. 참조된 객체가 변경되면 변경 내용은 새 배열과 원래 배열 모두에서 변경된다.





### 구조 분해 할당

구조 분해 할당 구문은 배열이나 객체의 속성을 해체하여 그 값을 개별 변수에 담을 수 있게 하는 javascript 표현식이다.

#### 예제 코드

```javascript
let a, b, rest;
[a, b] = [10, 20];
console.log(a); // 10
console.log(b); // 20

[a,b, ...rest] = [10,20, 30, 40, 50];

console.log(rest);
// [30,40,50]
```

```javascript
var a, b, rest;
({a,b, ...rest} = {a:10, b:20, c:30, d:40});
console.log(a); //10
console.log(b); //20
console.log(rest); // {c:30, d:40}
```

#### 설명

객체 및 배열 리터럴 표현식을 사용하면 즉석에서 쉽게 데이터 뭉치를 만들 수 있다.

```javascript
var x = [1,2,3,4,5];
var [y,z] = x;
console.log(y); //1
console.log(z); //2
```

#### 배열 구조 분해

```javascript
var foo = ["one", "two", "three"];
var [red, yellow, green] = foo;
console.log(red); // "one"
console.log(yellow); // "two"
console.log(green); // "three"
```

```javascript
var a=1;
var b=3;

[a,b] = [b,a];
console.log(a) //3
console.log(b) //1
```

#### 객체 구조 분홰

```javascript
let {a,b, ...rest} = {a:10 , b:20 , c:30 , d:40}
a; //10
b; //20
rest; // {c:30, d:40}
```



### 화살표 함수

화살표 함수 표현은 전통적인 함수표현의 간편안 대안이다. 몇가지 제한점이 있다.

- this나 super에 대한 바인딩이 없고, methods로 사용될 수 없다.
- new.target 키워드가 없다.
- 일반적으로 스코프를 지정할 때 사용하는 call, apply, bind methods를 이용할 수 없다.
- 생성자로 사용할 수 없다.
- yield를 화살표 함수 내부에서 사용할 수 없다.

```javascript
const materials = [
    'Hydrogen',
    'Helium',
    'Lithium',
    'Beryllium'
];

console.log(materials.map(material => material.length));
elements.map((element) => {
    return element.length;
});
// [8,6,7,9]
```




참조 : [MDN Web Docs](https://developer.mozilla.org/)