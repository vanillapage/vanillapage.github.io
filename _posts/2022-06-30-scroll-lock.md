---
layout: post
title: Scroll Lock
subtitle: 모바일 스크롤 막기
categories: javascript-module
tags: [javascript, scroll, popup]
---

업무마다 상이하지만 크게 2가지 유형으로 scroll lock을 적용  

A. body 에 fixed 스타일 적용  
B. body, wrap 등 스크롤 가능 영역에 관련 이벤트 중단  
  
## body fixed + es6 + body,wrap 이벤트 중단

```javascript
(function(win){
    'use strict';

    let body            = document.body;
    let btnPopupTrigger = document.querySelectorAll('.btn');
    let popupOpenFlag   = false;

    // TYPE A
    let scrollLock = (_boolean) => {
        if ( _boolean == true ) {
            body.style = `overflow:hidden;position:fixed;width:100%;height:100%`;
        } else {
            body.removeAttribute('style');
        }
    }
    
    // TYPE B
    let scrollLock = (_boolean) => {
        if ( _boolean == true ) {
            body.style = `overflow:hidden;position:fixed;width:100%;height:100%`;
        } else {
            body.removeAttribute('style');
        }
    }

    for (let i=0; i < btnPopupTrigger.length; i++) {
        btnPopupTrigger[i].addEventListener('click', () => {
            scrollLock(true);
        })
    }
})(window)
```

2. PC에서는 큰 이슈 없으나 팝업 + scroll lock 적용 시 다음과 같은 문제가 발생했다.  
  1) A타입의 경우 스크롤은 중단되나 콘텐츠 스크롤이 상단으로 이동함  
  2) B타입의 경우 스크롤이 콘텐츠 상단으로 이동하진 않지만 다음과 같은 이슈 발생  
  * iOS 13 ↓ : 콘텐츠 스크롤 중단 + 팝업 스크롤 불가 (캡쳐링으로 인해 모든 콘텐츠 스크롤 중단)  
  * 최신 iOS : 콘텐츠 스크롤 중단되지 않음 + 팝업 스크롤 불가  

## es5+팝업스크롤막기+ 현재 위치 기억

```html
<button type="button" class="js-btn-popup" data-popup="popupSample">나를 누르면 팝업이 뿅</button>

<div class="popup" id="popupSample" style="display:none">
  <!-- .popup:before에 dimmed style 적용 -->
  <div class="popup_inner"> <!-- 실제 팝업 콘텐츠 영역 -->
    <button type="button" class="btn_close_popup js-btn-close-popup">닫기</button>
  </div>
</div>
```

```javascript
  return {
    init : function() {
      this.setElement();
      this.initOpts();
      this.bindEvents();
    },
    setElement : function() {
      this.btnOpenPopup  = $('.js-btn-popup');
      this.btnClosePopup = $('.js-btn-close-popup');
      this.popup         = $('.popup');
    },
    initOpts: function() {
      this.scrollLockOffset;
      this.popupOpenFlag = false;
    },
    bindEvents : function() {
      this.btnOpenPopup.on('click', $.proxy(this.onClickOpenLayerPopup, this))
      this.btnClosePopup.on('click', $.proxy(this.closeLayerPopup, this))
      $('.popup').before().on('click', $.proxy(this.onDimmedLayerClose, this))
    },
    onClickOpenLayerPopup : function(e) {
      var target                 = $(e.currentTarget),
      targetLayerPopup = target.data('popup'); // 버튼 클릭 시 해당 data-poup 값 가져옴
      this.openLayerPopup(targetLayerPopup); // data-popup값 전달
    },
    openLayerPopup : function(_targetLayerPopup) {
      var _this = this;
      this.scrollLockOffset = window.pageYOffset; // 현재 스크롤 값
      this.closeLayerPopup(); // 모든 팝업 close
      this.scrollLock(true); // 페이지 scroll Lock 적용
      $(_targetLayerPopup).fadeIn();
      setTimeout(function(){
        _this.popupOpenFlag = true; // popup flag 변수 재할당
      },300)
    },
    closeLayerPopup : function() { // close 버튼 클릭 시 실행
      this.scrollLock(false);
      this.popup.fadeOut();
    },
    scrollLock : function(_boolean) {
      if(_boolean) {
        $('html').css({
          overflow: 'hidden', // 팝업 오픈 시 콘텐츠 스크롤 되지 않도록 적용
        })
        $('body').css({
          position: 'fixed',
          height: '100%',
          top: - (this.scrollLockOffset), // 현재 스크롤값 위치로 body 이동
          touchAction: 'none' // 콘텐츠 스크롤 되지 않도록 적용
        })
      } else {
        ('html,body').removeAttr('style');
        window.scrollTo(0, this.scrollLockOffset); // 현재 스크롤 위치로 이동
        window.setTimeout(function () {
          this.scrollLockOffset = null; // 스크롤 참조값 초기화
        }, 0);
      }
    },
    onDimmedLayerClose : function(e) { // dimmed 클릭 시 팝업 닫힘
      var layerPopup = $('.popup_inner'); // 실제 콘텐츠 영역 (dimmed 제외한)
      if (this.popupOpenFlag == true) { // 만약 팝업이 오픈 상태라면
        if(layerPopup.has(e.target).length === 0) { // 클릭 타겟이 popup_inner가 아니라면
          this.closeLayerPopup(); // 화면 전체를 뒤덮은 dimmed로 간주하고 팝업 close
          this.popupOpenFlag = false; // popup flag 변수 재할당
        }
      }
    }
```

* 페이지 스크롤이 불가하도록 html 은 overflow:hidden 처리 후 body 를 fixed 형태로 띄워 현재 스크롤 값 위치로 이동하여 상단 이동 발생하지 않도록 적용
* dimmed 클릭의 경우 일부 디바이스에서 popup_inner 와 dimmed 영역이 겹쳐져 있는 부분을 클릭할 경우 onDimmedLayerClose 가 호출되는 케이스가 있어 this.popupOpenFlag flag 변수로 명시성을 구분
