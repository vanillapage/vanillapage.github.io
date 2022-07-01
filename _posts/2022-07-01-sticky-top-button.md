---
layout: post
title: Sticky top button
subtitle: 특정 레이아웃을 만나면 해당 레이아웃 높이만큼 위치가 변하는 상단 이동 버튼
categories: javascript-module
tags: [javascript, scroll]
---

```css
*{margin:0;padding:0}
.btn_top{position:fixed;bottom:100px;right:100px;padding:20px;background:orangered;font-size:12px;color:#fff;text-align:center}
```

```html
<div class="wrap">
   <main class="container" style="display:block;height:2000px;background:beige">
     나는 컨텐츠
   </main>
   <footer class="footer" style="height:300px;background:darkcyan">
     나는 푸터
   </footer>
   <a href="#" class="btn_top">TOP</a>
 </div>
```

```javascript
class PageTop {
      // _buttonEl : 탑버튼 element
      // _breakPointEl : 탑버튼 offset 전환 기준이 될 블럭
      constructor (_buttonEl,_breakPointEl) {
        this.timer;
        this.time          = 20;
        this._buttonEl     = _buttonEl;
        this._breakPointEl = _breakPointEl;
        this._this = this;
      }
      scrollFunc () {
        // 디바운싱 적용
        if(!this.timer) {
          this.timer = setTimeout(() => {
            this.checkCurrentScroll();
            this.timer = null;
          }, this.time);
        }
      }
      checkCurrentScroll () {
        // scrollY = scroll top 값
        // innerHeight = 브라우저 창 안쪽 height 값
        // offsetTop = document 내에서 breakPointEl의 위치값(top)
        if( window.scrollY + window.innerHeight >= this._breakPointEl.offsetTop ) {
          this.setButtonOffset(true);
        } else {
          // 푸터가 노출되지 않는 스크롤 높이일 경우 기존 css에 정의된 값이 적용되도록 style 속성 제거
          this.setButtonOffset(false);
        }
      }
      setButtonOffset (_bl) {
        if(_bl) {
          // 스크롤 다운 시 화면 하단 기준 푸터 노출 시 TOP버튼이 유동적인 값을 가질 수 있도록 화면 상단값 기준인 scrollY에서 브라우저 높이만큼 빼줌.
          let calc = window.scrollY + window.innerHeight - this._breakPointEl.offsetTop;
          // 푸터가 화면에 노출되는만큼 TOP버튼 bottom값을 더해준다.
          this._buttonEl.style.bottom = calc + 100 + 'px'
        } else {
          this._buttonEl.removeAttribute('style');
        }
      }
      onClickFunc (event) {
        event.preventDefault();
        window.scrollTo({top: 0, behavior: 'smooth'})
      }
    }
    let btnTop          = document.querySelector('.btn_top');
    let breakPointBlock = document.querySelector('.footer');
    let pageTop         = new PageTop(btnTop, breakPointBlock);

    window.addEventListener('scroll', () => {
      pageTop.scrollFunc();
    })
    window.addEventListener('resize', () => {
      pageTop.checkOffset();
    })
    btnTop.addEventListener('click', () => {
      pageTop.onClickFunc(event);
    })
```