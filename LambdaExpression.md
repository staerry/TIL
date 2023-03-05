# 람다식(Lambda Expression)
* 람다식이란 메서드를 하나의 식(expression)으로 표현한 것입니다.
* 함수를 간략하면서도 명확한 식으로 표현할 수 있게 합니다.
* 메서드를 람다식으로 표현하면 메서드의 이름과 반환값이 없어지므로 '익명함수'라고도 부릅니다.
  * cf. 메서드 - 모든 메서드는 클래스에 포함되어야 하므로 클래스 및 객체를 새로 생성해야만 호출할 수 있습니다.
  * 반면 람다식은 클래스, 객체 생성 과정 없이 람다식 자체만으로도 메서드의 역할을 대신할 수 있습니다.

## 람다식 작성하기
* 메서드에서 이름과 반환타입을 제거하고 매개변수 선언부와 몸통 {} 사이에 -> 를 추가합니다.
```
  int max(int a, int b) { 
    return a > b ? a : b;
  }
  
  // 람다식으로 변환
  (int a, int b) -> {
    return a > b ? a : b;
  }
```
* 반환값이 있는 메서드의 경우, return문 대신 식(expression)으로 대신할 수 있으며, 식의 연산 결과가 자동으로 반환값이 됩니다.
  * 이때는 문장이 아닌 식이므로 끝에 ; 를 붙이지 않습니다.
```
  (int a, int b) -> a > b ? a : b;
```
* 람다식에 선언된 매개변수의 타입은 추론이 가능한 경우 생략할 수 있으며, 대부분 생략 가능합니다. (반환 타입이 없는 것도 추론이 가능하기 때문)
```
  (a, b) -> a > b ? a : b;
```
* 람다식 변환 연습
```
  // 1)
  int max(int a, int b) {
    return a > b ? a : b;
  }
   
  // 변환
  (a, b) -> a > b ? a : b
  
  // 2)
  void printVar(String name, int i) {
    System.out.println(name+"="+i);
  }
  
  // 변환
  (String name, int i) ->
    System.out.println(name+"="+i)  // 괄호 안의 문장이 하나일 때는 괄호를 생략할 수 있으며, 이때는 문장 끝에 ;를 붙이지 않아야 함
  
  (name, i) ->
    System.out.println(name+"="+i)  // 매개변수의 타입이 추론 가능한 경우 생략할 수 있음
    
  // 3)
  int sqaure(int x) {
    return x * x;
  }
  
  // 변환
  x -> x * x
  
  // 4)
  int roll() {
    return (int) (Math.random() * 6);
  }
  
  // 변환
  roll() ->
    {return (int)(Math.random() * 6);}  // 괄호 안의 문장이 return문일 경우 괄호를 생략할 수 없음
  
  // 5)
  int sumArr(int[] arr) {
    int sum = 0;
    for(int i : arr) {
      sum += i;
    return sum;
  }
  
  // 변환
  (int[] arr) -> {
    int sum = 0;
    for(int i : arr) {
     sum += i;
    return sum;
  }
```
