---
layout: post
title: "[STUDY] javascript 함수 (push,pop,unshift,shift)"
subtitle: javascript Array 값 추가,삭제 함수
categories: study
tags: [javascript]




---



## [STUDY] javascript 함수 (push,pop,unshift,shift)

javascript 배열(Array)에 값을 추가하거나 삭제하는 함수에 대해 알아보자.

### 배열에 값을 추가하는 함수 (push, unshift)

- `push()` : 배열의 맨 끝에 값을 추가한다
- `unshift()` : 배열의 맨 앞에 값을 추가한다

### 배열에 값을 제거하는 함수 (pop, shift)

- `pop()` : 배열의 맨 끝에 값을 제거한다
- `shift()` : 배열의 맨 앞에 값을 제거한다

### 예제코드

#### Array.prototype.push()

push() 메서드는 배열의 끝에 하나 이상의 요소를 추가하고, 배열의 새로운 길이를 반환한다

```javascript
const animals = ['pigs', 'goats', 'sheep'];
const count = animals.push('cows');
console.log(count); // 4
console.log(animals); // ['pigs', 'goats', 'sheep', 'cows']
animals.push('chickens','cats','dogs');
console.log(animals);
// ['pigs','goats','sheep','cows','chickens','cats','dogs']
```

#### Array.prototype.unshift()

unshift() 메서드는 새로운 요소를 배열의 맨 앞쪽에 추가하고, 새로운 길이를 반환한다

```javascript
const array1 = [1,2,3];
console.log(array1.unshfit(4,5)); // 5
console.log(array1); // [4,5,1,2,3]
```

#### Array.prototype.pop()

빈 배열의 경우 undefined를 반환한다

pop() 메서드는 배열에서 마지막 요소를 제거하고 그 요소를 반환한다

```javascript
const plants = ['brocoli','cauliflower','cabbage','kale','tomato'];
console.log(plants.pop()); // tomato
console.log(plants); // ['brocoli','cauliflower','cabbage','kale']
```

#### Array.prototype.shift()

빈 배열의 경우 undefined 반환한다

shift() 메서드는 0번째 위치의 요소를 제거하고 제거된 값을 반환한다

```javascript
const array1 = [1,2,3];
const firstElement = array1.shift();
console.log(array1); // [2,3]
console.log(firstElement); // 1
```

참조 : [MDN Web Docs](https://developer.mozilla.org/)