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
* sorted()는 지정된 Comparator로 스트림을 정렬하는데, Comparator 대신 int값을 반환하는 람다식을 사용하는 것도 가능합니다.
* Comparator를 지정하지 않으면 스트림 요소의 기본 정렬 기준(Comparable)로 정렬합니다.
 * 단 스트림의 요소가 Comparable을 구현한 클래스가 아니면 예외가 발생합니다.

#### Comparator의 default 메서드
```
 reversed()
 thenComparing(Comparator<T> other)
 thenComparing(Function<T, U> keyExtractor)
 thenComparing(Function<T, U> keyExtractor, Comparator<U> keyCorp)
 thenComparingInt(ToIntFunction<T> keyExtactor)
 thenComparingLong(ToLongFunction<T> keyExtractor)
 thenComparingDouble(ToDoubleFunction<T> keyExtractor)
```

#### Comparator의 static 메서드
```
 naturalOrder()
 reverseOrder()
 comparing(Function<T, U> keyExtractor)
 comparing(Function<T, U> keyExtractor, Comparator<U> keyComparator)
 comparingInt(ToIntFunction<T> keyExtractor)
 comparingLong(ToLongFunction<T> keyExtractor)
 comparingDoulbe(ToDoubleFunction<T> keyExtractor)
 nullsFirst(Comparator<T> comparator)
 nullsLast(Comparator<T> comparator)
```

* thenComparing(): 정렬 조건을 추가할 때 사용하는 메서드입니다.
```
 thenComparing(Comparator<T> other)
 thenComparing(Function<T, U> keyExtractor)
 thenComparing(Function<T, U> keyExtractor, Comparator<U> keyComp)
```

* 학생 스트림(studentStream)을 반, 성적, 이름순으로 정렬하려면 다음과 같이 작성합니다.
```
 studentStream.sorted(Comparator.comparing(Student::getBan)
                                .thenComparing(Student::getTotalScore)
                                .thenComparing(Student::getName))
                                .forEach(System.out::println);
```

### 변환 - map()
* 스트림의 요소에 저장된 값 중 원하는 필드만 뽑거나 특정 형태로 변환할 때 사용합니다.
* map() 역시 중간 연산이므로, 연산 결과는 String을 요소로 하는 스트림입니다.
* map()도 filter()처럼 하나의 스트림에 여러 번 적용할 수 있습니다. 다음은 File의 스트림에서 파일의 확장자만 뽑은 다음 중복을 제거해서 출력합니다.
```
 fileStream.map(File::getName)                // Stream<File> -> Stream<String>
           .filter(s -> s.indexOf('.') != -1) // 확장자가 없는 것은 제외
           .map(s -> s.substring(s.indexOf('.') + 1) // Stream<String> -> Stream<String>
           .distinct()                        // 중복 제거
           .forEach(System.out:;print);
```

### 조회 - peek()
* 연산과 연산 사이에 올바르게 처리되었는지를 확인하는 메서드입니다.
* forEach()와 달리 스트림의 요소를 소모하지 않으므로 연산 사이에 여러 번 끼워 넣어도 됩니다.
```
 fileStream.map(File::getName)                // Stream<File> -> Stream<String>
           .filter(s -> s.indexOf('.') != -1) // 확장자가 없는 것은 제외
           .peek(s -> System.out.printf("filename = %s%n", s)) // 파일명을 출력
           .map(s -> s.substring(s.indexOf('.') + 1) // Stream<String> -> Stream<String>
           .peek(s -> System.out.printf("extension = %s%n", s)) // 확장자를 출력
           .distinct()                        // 중복 제거
           .forEach(System.out:;print);        
```

## 스트림의 최종 연산
* 최종 연산은 스트림의 요소를 소모해서 결과를 만들어 냅니다.
* 따라서 최종 연산 후에는 스트림이 닫히게 되고 더 이상 사용할 수 없습니다.
* 최종 연산의 결과는 스트림 요소의 합과 같은 단일 값이거나, 스트림의 요소가 담긴 배열 또는 컬렉션일 수 있습니다.

### forEach()
* 중간 연산인 peek()과는 달리 스트림의 요소를 소모하는 최종 연산입니다.
* 반환 타입이 void이므로 스트림의 요소를 출력하는 용도로 많이 사용됩니다.

### 조건 검사 - allMatch(), anyMatch(), noneMatch(), findFirst(), findAny()
* 스트림 요소에 대해 지정된 조건에 모든 요소가 일치하는지, 일부가 일치하는지, 아니면 어떤 요소도 일치하지 않는지 확인하는 요소입니다.
* 이 메서드들은 모든 매개변수로 Predicate를 요구하며 연산 결과로 boolean을 반환합니다.
* 학생들의 성적 정보 스트림 stuStream에서 총점이 낙제점(총점 100 이하)인 학생이 있는지 확인하는 방법은 다음과 같습니다.
```
 boolean noFailed = stuStream.anyMatch(s -> s.getTotalScore() <= 100)
```

## reduce()
* Stream.reduce(accumulator) : Stream의 요소들을 하나의 데이터로 만드는 작업을 수행합니다.
 * accumulator - 연산을 수행하는 부분으로, 직접 구현해서 인자로 전달해야 합니다.

### 통계 - count(), sum(), average(), max(), min()


## collect()
* 스트림의 요소를 수집하는 최종 연산입니다.
* collect()가 스트림의 요소를 수집하려면 어떻게 수집할 것인지에 대한 방법이 정의되어 있어야 하는데, 이 방법을 정의한 것이 컬렉터(collector)입니다.
* List나 set이 아닌 특정 컬렉션을 지정하려면 toCollection()에 해당 컬렉션의 생성자 참조를 매개변수로 넣어주면 됩니다.
```
 List<String> names = stuStream.map(Student::getName).collect(Collectors.toList());
 ArrayList<String> list = names.stream().collect(Collectors.toCollection(ArrayList::new));
```
* Map은 키와 값의 쌍으로 저장해야 하므로 객체의 어떤 필드를 키로, 어떤 필드를 값으로 사용할지 지정해 줘야 합니다.
```
 Map<String, Person> map = personStream.collect(Collectors.toMap(p -> p.getRegId(), p -> p));
 // 타입이 Person인 스트림에서 regId를 키로하고, 값으로 Person 객체를 그대로 저장
```
* 스트림에 저장된 요소들을 'T\[]'타입의 배열로 변환하려면 toArray()를 사용하면 됩니다.
 * 단, 해당 타입의 생성자 참조를 매개변수로 지정해야 합니다. 만약 매개변수를 지정하지 않으면 반환되는 배열의 타입은 Object\[]입니다.
```
 Student[] stuNames = studentStream.toArray(Student[]::new);
 Object[] stuNames = studentStream.toArray();
 
```
