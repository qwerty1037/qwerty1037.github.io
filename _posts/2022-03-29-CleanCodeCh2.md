---
layout: single
title: "Clean Code Chapter 2. 의미 있는 이름"
categories: clean_code
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
*1차 개선* :각 이름이 무엇을 명시하는지 알 수 있다.

```java
public List<Cell> getFlaggedCells(){
    List<Cel> flaggedCells = new ArrayList<Cell>();
    for(Cell cell: gameBoard)
        if(cell.isFlagged())
            flaggedCells.add(cell);
    return flaggedCells;
} 
```
*2차 개선* :int배열을 Cell 클래스로 만들고, if비교문을 하나의 함수를 사용해 명시성을 높였다.



위 코드는 단순성이 아니라 *함축성* 에서 문제가 있다. 코드의 맥락이 명시적으로 드러나지 않는다.  

위 코드는 독자가 4가지 정보를 알고 있음을 전제한다.  

1. theList에 무엇이 들었는가?
2. theList에서 0번째 값이 왜 중요한가?
3. 값 4는 무슨 의미인가?
4. 반환값 list1을 어떻게 사용하는가?  


---  
 

### 2. 그릇된 정보 피하기  

---  

hp와 같은 약어, List가 아닌데 acoountList라 명명하는 등 잘못된 정보를 주는 것을 피해라.  

육안으로 보기에 유사한 이름을 사용하지 마라. o와 0 혼용, l과 1 혼용이 그 예시이다.  

---  

### 3. 구분을 명확히 하기  

---

불용어(분석에 의미가 없는 단어, ex) info, data등)를 사용하면 안된다.

a1, a2와 같은 연속적인 이름은 사용하면 안된다.

---  

### 4. 발음하기 쉬운 이름을 사용하기  

---  

```java
class DtaRcrd102 {
    private Date genymdhms; //GENerationYearMonthDayHourMinuteSecond
    private Date modymdhms; //MODificationYearMonthDayHourMinuteSecond
    private final String pszqint = "102";
    /* ... */
};
```
*나쁜 코드* :변수의 발음이 어렵다.  

```java
class Customer {
    private Date generationTimestamp;
    private Date modificationTimestamp;
    private final String recordId = "102";
    /* ... */
};
```
*개선* :변수의 발음이 명료하고, 의미가 명료하다.

---  

### 5. 검색하기 쉬운 이름 사용  

---  

상수 or 변수의 이름을 지을 때, 7 이나 e 같은 문자는 ide의 자동완성기능에 입력하면 연관된 모든 상수or변수가 출력된다.  

이 경우 원하는 변수를 검색하기 매우 어려워지므로, 구분이 쉬운 변수명을 사용하는 것이 좋다.

간단한 메서드의 로컬 변수만 한 문자를 사용한다.(예: for문의 i=0)

```java
for(int j=0; j<34; j++) {
    s += (t[j]*4)/5;
}
```
*나쁜 코드* : 각 변수가 구분되지 않고, 의미를 알 수 없다.

```java
int realDaysPerIdealDay = 4;
const int WORK_DAYS_PER_WEEK = 5;
int sum = 0;
for(int j=0; j< NUMBER_OF_TASKS; j++){
    int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
    int realTaskWeeks = (realTaskDays / WORK_DAYS_PER_WEEK);
    sum += realTaskWeeks;
}
```
*개선된 코드* : 함수가 길어졌으나, 의미가 명료하고 WORK_DAYS_PER_WEEK를 검색하는 것이 쉽다.  

---  

### 6. 클래스 이름은 명사, 메서드 이름은 동사

---

클래스와 객체의 이름은 명사나 명사구가 적합하다.

메서드의 이름은 동사나 동사구가 적합하다. 
    
    메서드(Method): OOP에서 클래스 내부에 정의된 함수 

접근자(Accessor), 변경자(Mutator), 조건자(Predicate)는 get, set, is를 값 앞에 붙인다.  

    Accessor: private 멤버변수에 대해 클래스 내부에 메서드로 만들어놓은 함수, 인스턴스 변수의 값을 리턴

    Mutator: 인스턴스 변수의 값을 설정

    Predicator: 조건식을 표현하는데 사용되는 함수, 매개변수는 1개이고 boolean을 return

생성자를 overload할 떄는 정적 팩토리 메서드(Static Factory Method)를 사용한다.

```java
Complex fulcrumPoint = Complex.FromRealNumber(23.0); //좋은 예시
Complex fulcrumPoint = new Complex(23.0); //나쁜 예시
```

---

### 7. 한 개념에 한 단어 사용

---

하나의 추상적 개념에는 하나의 단어를 선택한다.(fetch, retrieve, get)

하나의 단어를 두가지 목적으로 사용하지 않는다.

    add 메서드를 기존값 2개를 더해 새로운 값을 만드는 의미로 사용하고 있다.
    이 때, List에 값 하나를 추가하는 메서드의 이름을 일관적으로 add라고 지으면 안된다.    

---

### 8. 의미 있는 맥락

---

클래스, 함수, 이름에 맥락을 부여하고, 최후의 수단으로 접두어를 붙인다. ex) addrFirstName


```java
private void printGuessStatistics(char candidate, int count){
    String number;
    String verb;
    String pluralModifier;
    if(count == 0) {
        number = "no";
        verb = "are";
        pluralModifier = "s";
    } else if(count == 1) {
        number = "1";
        verb = "is";
        pluralModifier = "";
    } else {
        number = Integer.toString(count);
        verb = "are";
        pluralModifier = "s";
    }
    String guessMessage = String.format(
        "There %s %s %s%s", verb, number, candidate, pluralModifier
    );
    print(guessMessage);
}
```
*나쁜 코드* : 함수가 길고, 세 변수를 함수 전반에서 사용

```java
public class GuessStaticsMessage {
    private String number;
    private String verb;
    private String pluralModifier;

    public String make(char candidate, int count) {
        createPluralDependentMessageParts(count);
        return String.format(
            "There %s %s %s%s", verb, number, candidate, pluralModifier
        );
    };

    private void createPluralDependentMessagePArts(int count){
        if(count == 0){
            thereAreNoLetters();
        } else if(count == 1){
            thereIsOneLetter();
        } else{
            thereAreManyLetters(count);
        }
    }

    private void thereAreManyLetters(int count){
        number = Integer.toString(count);
        verb = "are",
        pluralModifier = "s";
    }

    private void thereIsOneLetter(){
        number = "1";
        verb = "is",
        pluralModifier = "";
    }

    private void thereAreNoLetters(){
        number = "no";
        verb = "are",
        pluralModifier = "s";
    }
}
```
*개선된 코드* : 함수를 쪼개고자 GuessStatisticsMessage클래스를 만든 후 세 변수를 클래스에 넣었다. 그러면 세 변수는 **명확히** GuessStatisticsMessage에 속한다.

위 예시는 클래스로 알고리즘을 포장함으로써 number, verb, pluralModifier라는 변수 3개가 통계 추측(guess statistics)메세지에 사용된다는 맥락을 명확하게 바꿔주고, 함수를 쪼갬으로써 알고리즘의 이해를 더 쉽게 바꾸었다.

또한, 불필요한 맥락을 없에야 한다. 예를 들어, GSD회계 모듈에 변수에 GSD접두사를 추가하는 것은 아무 의미가 없다. 