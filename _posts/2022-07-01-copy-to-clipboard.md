---
layout: post
title: Copy to Clipboard
subtitle: 특정 텍스트 노드 복사하기
categories: javascript-module
tags: [javascript]
---

```html
<span data-copy-clipboard="false">나는 복사할 수 없습니다!!!!</span>
<span data-copy-clipboard="true">꺄아악 나를 복사하세요!!!</span>
<span data-copy-clipboard="true">꺄아아아악악 나도 복사하세요!!</span>
<span data-copy-clipboard="false">복사할 수 없습니다!!!!!!!!!!!!</span>

<textarea name="" id="" cols="30" rows="4" placeholder="복사한 텍스트를 붙여넣기 해 보세요."></textarea>
```

```javascript
// data-copy-clipboard 속성 가진 node list 
let clipboardArray = document.querySelectorAll('[data-copy-clipboard]');

class CopyToClipboard {
  constructor(item) {
    this._range;
    this._item = item;
  }
  init () {
    if( this._item.dataset['copyClipboard'] == 'false') return;
    if( this._item.dataset['copyClipboard'] == 'true') {
      this.getTextNode();
      this.setCopyClipboard();
    }
  }
  getTextNode () {
    // 한 쌍의 경계지점을 가진 텍스트 문자열인 Range 객체 생성
    this._range = document.createRange();
    // Range 객체에 item node 정보 저장.
    // 만약 경계 지점을 별도로 지정할 시 setStart, setEnd 메서드 이용
    this._range.selectNode(this._item.childNodes[0]);
  }
  setCopyClipboard () {
    // 사용자가 마우스나 키보드를 통해 서너택한 텍스트의 범위를 가져온다.
    let targetSelection = window.getSelection();
    targetSelection.removeAllRanges(); // 기존 선택 정보 제거
    targetSelection.addRange(this._range); // 사용자가 선택한 node 정보 저장
    // 클립보드에 현재 선택 영역 복사
    document.execCommand('copy');
    // 선택 정보 삭제
    targetSelection.removeRange(this._range);
    alert('클립보드에 복사되었습니다.');
  }
}

clipboardArray.forEach( (item, index, arr) => {
  item.addEventListener('click', () => {
    let copy = new CopyToClipboard(item);
    copy.init()
  })
})

// ie는 노드 오브젝트에서 forEach 메서드를 가져와 사용해야함
Array.prototype.forEach.call(clipboardArray , function(item, index, arr){
  item.addEventListener('click', function(){
    CopyToClipboard2.init(item);
  })
})
```

### Range
한 쌍의 경계 지점(범위 시작&끝)을 가진 텍스트 문자열을 말하는 Range는 객체형태로 반환된다. 이 Range 객체는 `document.createRange()` 로 생성이 가능하며, 스크립트에 의해 추가/삭제가 가능하다.

### Selection
사용자가 마우스를 통해 드래그하거나, 키보드를 통해 선택한 텍스트의 범위를 나타낸다. Selection은 방향을 가질 수 있으며, 그 값은 브라우저에서 제공하는 Selection API를 통해 확인할 수 있다.
FireFox는 하나의 selection에 여러 range 객체를 가질 수 있으나 그 외 브라우저는 selection에 하나의 Range 객체만 허용한다.

### Selection API
 * anchor : 텍스트를 선택한 지점
 * focus : 선택이 종료된 지점
두 값을 비교하여 Selection의 방향을 확인 가능하다.
( 좌 ⇒ 우로 드래그할 시 anchor < focus / 우 ⇒ 좌로 드래그할 시 anchor > focus )

### Selection Object
사용자 또는 Caret의 위치에 따라 선택한 Range를 나타내는 `window.getSelection()` 으로 Selection 객체를 호출할 수 있다.

---

### ref.
* https://developer.mozilla.org/ko/docs/Web/API/Document/createRange
* https://ohgyun.com/547
* https://sub0709.tistory.com/144
* https://gdtbgl93.tistory.com/175
* https://jungpaeng.tistory.com/86