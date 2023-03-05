# 스트림(stream)
## 스트림의 연산
* 스트림이 제공하는 연산은 중간 연산과 최종 연산으로 분류됩니다.
  * 중간 연산 : 연산 결과를 스트림으로 반환하므로 중간 연산을 연속해서 연결할 수 있습니다.
  * 최종 연산 : 연산 결과가 스트림이 아닌 연산으로 스트림의 요소를 소모하면서 연산을 수행하므로 단 한번만 연산할 수 있습니다.
* 최종 연산이 수행되기 전까지는 중간 연산이 수행되지 않습니다. 즉, 최종 연산이 수행되어야만 스트림의 요소들이 중간 연산을 거쳐 최종 연산에서 소모됩니다.

## 스트림 만들기
* 스트림의 소스가 될 수 있는 대상 : 배열, 컬렉션, 임의의 수 등

### 컬렉션
* 컬렉션의 최고 조상인 Collection에 stream()이 정의되어 있으므로 자손인 List와 Set을 구현한 컬렉션 클래스는 모두 stream() 메서드로 스트림을 생성할 수 있습니다.
* stream() : 해당 컬렉션을 소스로 하는 스트림을 반환합니다.
```
  List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);  // 가변인자
  Stream<Integer> intStream = list.stream();
```
* forEach() : 괄호 안에 지정된 작업을 스트림의 모든 요소에 대해 수행합니다.
```
  intStream.forEach(System.out.println::println); // 스트림의 모든 요소를 출력
  intStream.forEach(System.out.println::println); // 에러 : 스트림의 요소를 소모하면서 작업을 수행하므로 같은 스트림에 forEach()를 두 번 호출할 수 없음
```

### 배열
* 배열을 소스로 하는 스트림을 생성하는 메서드는 Stream과 Arrays에 static 메서드로 정의되어 있습니다.
```
  Stream<T> Stream.of(T... values)
  Stream<T> Stream.of(T[])
  Stream<T> Arrays.stream(T[])
  Stream<T> Arrays.stream(T[] array, int startInclusive, int endExclusive)
```  
* int, long, double과 같은 기본형 배열을 소스로 하는 스트림을 생성하는 메소드도 있습니다.
```
  IntStream IntStream.of(int... values)
  IntStream Arrays.stream(int[])
```

### 특정 범위의 정수
* IntStream과 LongStream은 지정된 범위의 연속된 정수를 스트림으로 생성해 반환하는 range(), rangeClose()를 가지고 있습니다.
```
  IntStream.range(int begin, int end)   // 경계의 끝인 end가 포함되지 않음
  IntStream.rangeClosed(int begin, int end) // 경계의 끝인 end가 포함됨
```

### 임의의 수
* 난수를 생성하는 데 사용되는 Random 클래스에는 아래와 같은 인스턴스 메서드들이 포함되어 있습니다.
```
  IntStream   ints()
  LongStream  longs()
  DoubleStream  doubles()
```
* 이 메서드들이 반환하는 스트림은 크기가 정해지지 않은 무한 스트림이므로 limit()을 함께 사용해 스트림의 크기를 제한해야 합니다. (무한 -> 유한 스트림)
```
  intStream = new Random().ints();  // 무한 스트림
  intStream.limit(5).forEach(System.out::println);  // 5개의 요소로 제한해 출력
```

### 람다식 - iterate(), generate()
* 람다식을 매개변수로 받아서 이 람다식에 의해 계산되는 값들을 요소로 하는 무한 스트림을 생성하는 메서드입니다.
```
  Stream<Integer> evenStream = Stream.iterate(0, n->n+2); // 0, 2, 4, 6, ...
```

### 파일
* list()는 지정된 디렉토리(dir)에 있는 파일의 목록을 소스로 하는 스트림을 생성해서 반환합니다.
```
  Stream<Path>  Files.list(Path dir)
```

### 빈 스트림
* 요소가 하나도 없는 비어있는 스트림을 생성합니다.
* 스트림에 연산을 수행한 결과가 하나도 없을 때, null 대신 빈 스트림을 반환할 때 사용합니다.
```
  Stream emptyStream = Stream.empty();  // empty()는 빈 스트림을 생성해서 반환
  long count = emptyStream.count(); // 0 - count()는 스트림 요소의 개수를 반환
```

### 두 스트림의 연결
* concat(): 두 스트림을 하나로 연결하는 메서드로, 두 스트림의 요소는 같은 타입이어야 합니다.


## 스트림의 중간 연산
### 스트림 자르기 - skip(), limit()
* skip(3): 처음 3개의 요소를 건너뜁니다.
* limit(5): 스트림의 요소를 5개로 제한합니다.
```
  IntStream intStream = IntStream.rangeClose(1, 10);  // 1~10 요소를 가진 스트림(rangeClosed는 end 요소를 포함)
  intStream.skip(3).limit(5); // 45678
```

### 스트림 요소 걸러내기 - filter(), distinct()
* filter(): 주어진 조건(Predicate)에 맞지 않는 요소를 걸러냅니다.
```
  IntStream intStream = IntStream.rangeClosed(1, 10); // 1~10
  intStream.filter(i -> i%2 == 0).forEach(System.out::print); // 246810
```
* distinct(): 스트림에서 중복된 요소를 제거합니다.

### 정렬 - sorted()
```
  Stream<T> sorted()
  Stream<T> sorted(Comparator<? super T> comparator)
```
* sorted()는 
