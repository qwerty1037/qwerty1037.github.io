---
layout: single
title: "[refactoring] 검색창 리팩토링"
categories: flutter
---

2월 26일 필자가 올렸던 검색창 코드를 리팩토링한다.

전체 코드는 아래 깃허브 링크를 참조하라.

<https://github.com/qwerty1037/flutter/tree/main/searchbar_refactored>  

또한, 리팩토링 이전 검색창에 관한 포스팅은 아래 링크를 참조하라.

<https://qwerty1037.github.io/searchbar/>

---

클린코드를 공부하면서, 필자는 현재 학교 동아리에서 진행하고 있는 프로젝트에서 작성한 코드가 상당히 더럽다는 것을 알 게 되었다.(flutter를 갓 배운 상태에서 맨 땅에 헤딩)

그 중 일부인 검색창 관련 부분을 클린코드에서 배운 방식대로 리팩토링해보기로 하였다.

---

### 1. 변수 명 바꾸기

---


