---
layout: post
title: "[COTE] JadenCase 문자열 만들기"
subtitle: 프로그래머스 레벨2
categories: COTE(JS)
tags: [COTE, programmers]




---

## [COTE] JadenCase 문자열 만들기

```javascript
function solution(s) {
    return s.split(' ').map((word)=>{
        if(word==='') return word;
        if(/[0-9]/.test(word[0])){
            return word.toLowerCase()
        } else{
            return word[0].toUpperCase()+word.substr(1).toLowerCase();
        }
    }).join(' ');
}
```

---

문제 : [프로그래머스-JadenCase 문자열 만들기](https://programmers.co.kr/learn/courses/30/lessons/12951)