---
layout: post
title: Debounce & Throttle
subtitle: 짧은 시간 내 발생하는 이벤트로 인한 브라우저 성능 저하 예방법
categories: javascript-study
tags: [javascript, optimization]
---

리사이즈, 스크롤 등과 같은 리스너들은 짧은 시간 내 많은 이벤트를 발생합니다. 리스너와 연결된 함수의 내용이 복잡한 내용을 담고 있다면 브라우저의 성능 저하를 일으킬 수 있겠죠. 이런 문제를 해결하기 위해 이벤트 발생을 제한하는 디바운스와 쓰로틀이라는 방법이 존재합니다.


## 디바운싱(Debouncing)
과다한 이벤트 실행을 방지하기 위해 함수들을 그룹화하여 특정시간 내 연이어 호출되는 함수들 중 제일 마지막에 발생하는 이벤트만 호출하는것을 말합니다.
예제를 보면 쉽게 이해할 수 있습니다.

```javascript
(function(){
  'use strict';
  let timer,
    time = 300;

  let onInputFunc = (e) => {
    if(timer) {
      clearTimeout(timer);
    }
    timer = setTimeout(() => {
        console.log(e.target.value);
      }, time);
  }
  document.getElementById('inp').addEventListener('input', onInputFunc);
})()
```

이벤트 발생 시 300ms의 호출 제한 시간을 가지며, 이동안 반복 발생되는 이벤트는 clearTimeout으로 제거됩니다. 호출 제한 시간이 지나면 마지막으로 등록된 함수의 내용, 즉 timer에 마지막으로 등록된 내용이 호출됩니다.


ajax 호출을 하거나 리사이즈와 같은 이벤트에 복잡한 함수가 존재할 경우 등에서 포함하는 코드가 복잡하다면 유용하게 사용할 수 있는 방법 중 하나입니다. 특히 유료 API를 사용하는 경우라면 사용자 이벤트가 종료된 후 호출을 하기 때문에 비용 절감 효과를 가져올 수 있습니다.


## 쓰로틀링(Throttling)
쓰로틀링은 마지막 함수가 호출된 후 일정 시간 내 다시 호출되지 않게 하는것을 말합니다. 쓰로틀링 적용의 가장 대표적인 예로는 1초에 수십번씩 반복되는 스크롤 같은 이벤트에 있습니다.

```javascript
(function(){
  'use strict';

  let timer,
      time = 1000;

  window.addEventListener('scroll', function(e){
    if(!timer) {
      console.log(1, timer)
      timer = setTimeout(() => {
        var dd = new Date();
        console.log(2, timer)
        timer = null;
        console.log('scroll 완룡', dd);
        console.log(3, timer)
      }, time);
      console.log(4, timer);
    }
    console.log(5, timer);
  })
})()
```


디바운스와 혼동을 막기 위해 중간 상태 별 콘솔 로그를 확인할 수 있도록 추가하였습니다. 브라우저 스크롤 시 로그 순서는 다음과 같이 노출됩니다.

![](/assets/images/posts/2022/07/2022-07-01-01.png)