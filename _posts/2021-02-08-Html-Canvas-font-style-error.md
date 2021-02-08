---
layout: post
title: "HTML Canvas font style 적용 안 될때"
categories: programming
---

## 언제?

@font-face를 이용해서 외부 폰트를 가져다 쓸 때

## 해결법

canvas 태그 위에

```html
<p style="font-family: '폰트이름'">가나다</p>
<canvas>........</canvas>
```

위 처럼 폰트가 적용된 태그를 만들고

```javascript
$("p").hide();
```

이런식으로 숨겨준다

중요한 것은 canvas에 폰트를 사용하기 전에 미리 폰트를 불러오는 것

