---
layout: single
title: "Clean Code Chapter 2. 의미 있는 이름"
---

### 1. 의도가 분명하게 이름을 짓기  

---  

이름을 지을때, 독자가 무언가를 알고 있을 것이라는 전제를 하지 말아야 한다.  

이름은 변수의 존재 이유, 수행 기능, 사용 방법을 명확하게 밝혀야 한다.  

```java
public List<int[]> getThem() {
    List<int[]> list1 = new ArrayList<int[]>();
    for(int[] x : theList)
        if (x[0] == 4)
            list1.add(x);
    return list1;
}
```
*나쁜 코드*  

```java
public List<int[]> getFlaggedCells(){
    List<int[]> flaggedCells = new ArrayList<int[]>();
    for(int[] cell: gameBoard)
        if(cell[STATUS_VALUE] == FLAGGED)
            flaggedCells.add(cell);
    return flaggedCells;
}
```
*개선된 코드* 각 이름이 무엇을 명시하는지 알 수 있다.

```java
public List
```



위 코드는 단순성이 아니라 "함축성"에서 문제가 있다. 코드의 맥락이 명시적으로 드러나지 않는다.  

위 코드는 독자가 4가지 정보를 알고 있음을 전제한다.  

1. theList에 무엇이 들었는가?
2. theList에서 0번째 값이 왜 중요한가?
3. 값 4는 무슨 의미인가?
4. 반환값 list1을 어떻게 사용하는가?

