# 5장 빅오
### 빅오란 입력크기가 무한대로 커질 때 알고리즘의 시간복잡도와 공간복잡도를 대략적으로 계산하는데 사용된다.
시간 복잡도의 크기 차이<br>
O(1) < O(log n) < O(n) < O(n log n) < O(n<sup>2</sup>) < O(2<sup>n</sup>) < O(n!)
#### O(1) : 입력값이 아무리 커도 실행 시간이 일정하다.
```
public int fn(int n) {
    return 42;    
}
```
n이 얼마든 간에 일정한 상수를 반환하므로 O(1)이다.

#### O(log n) : 입력값의 절반정도 실행하는 경우
```
public int fn(int n) {
    while(n > 1) 
        n /= 2;
    return n;
}
```
n의 값을 계속해서 반으로 나누므로 log<sub>2</sub> n 과 동일하므로 log n으로 표현

#### O(n) : 정확히 입력값의 크기만큼 실행 시간에 영향을 받는다.
```
public int fn(int n) {
    int r = 0;
    for(int i = 0; i < n; i++)
        r++;
    return r;
}
```
정확히 입력값의 크기만큼 연산을 진행한다. 
* 실행 시간이 선형으로 증가하기 때문에 **선형 시간 알고리즘**이라고도 한다.

#### O(n log n) : 입력값만큼 순회하며 log n의 연산이 곱해진다.
```
public int fn(int n) {
    int r = n;
    for(int i = 0; i < n; i++) {
        while(n > 1)
            n /= 2;
        n = r;    
    }
    return r;
}
```
병합 정렬을 비롯한 효율적인 정렬 알고리즘은 이에 해당한다.
* 모든 수를 한 번 이상을 비교해야 하는 비교 기반 정렬 알고리즘은 아무리 좋은 알고리즘도 O(n log n)보다 빠를 수 없다.

#### O(n<sup>2</sup>) : 입력값을 제곱만큼 연산한다. (입력값을 중첩으로 반복하면서 연산)
```
public int fn(int n) {
    int r = n;
    for(int i = 0; i < n; i++) {
        for(int j = 0; j < n; j++) 
            r += j;
    }
    return r;
}
```
버블 정렬같은 비효율적인 정렬 알고리즘이 이에 해당한다.
* 코딩 테스트에서는 알고리즘을 최적화한다고 하면 O(n<sup>2</sup>)을 O(n log n)으로 줄이는 일이 거의 대부분이다.

#### O(2<sup>n</sup>): 입력값의 크기만큼 2배씩 연산한다.
```
public int fn(int n) {
    if(n <= 1)
        return n;
    else
        return fn(n-1) + fn(n-2); 
}
```
파보나치 수를 재귀로 구현하는 알고리즘 (정확히는 1.6<sup>n</sup> 정도 되지만 단순히 2<sup>n</sup>으로 언급)

#### O(n!): 입력값을 1씩 줄여가며 곱셈 연산을 한다.(재귀 호출방식)
```
public int fn(int n) {
    for(int i = 0; i < n; i++)
        fn(n-1);
    return n;
}
```
O(n!)는 가장 느린 알고리즘으로 코딩 테스트나 실무에서는 적용하기 어려운 알고리즘이다.
* 팩토리얼을 재귀로 구현할 경우 O(n!)이라고 생각할 수 있지만 시간 복잡도는 O(n)이다

### 자바 컬렉션 프레임워크
#### 리스트 시간 복잡도

|     연산      |  ArrayList   |    LinkedList    |
|:-----------:|:------------:|:----------------:|
|  인덱스 끝에 삽입  | O(1) 가끔 O(n) |       O(1)       |
| 인덱스 중간에 삽입  |     O(n)     | 탐색 O(n), 삽입 O(1) |
| 인덱스 끝에서 삭제  |     O(1)     |       O(1)       |
| 인덱스 중간에서 삭제 |     O(n)     | 탐색 O(n), 삭제 O(1) |
|     조회      |     O(1)     |       O(n)       |
* ArrayList의 삽입에는 O(1)이지만 공간이 가득찰 경우 더블링이 일어나고 O(n)이 소요된다.
* ArrayList는 인덱스의 끝에서의 삽입과 조회가 빠르고, LinkedList는 인덱스 중간에서의 삽입과 삭제가 빠르다.

##### LinkedList의 내부 구현
```
public boolean add(E e) {
    ...
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if(l == null) first = newNode;
    else l.next = newNode;
    size++;
    modCount++;
}
```
LinkedList의 add의 경우 별도의 Node를 생성하는 매우 비싼 작업이 수반되기 때문에 속도가 느림

#### 맵 시간 복잡도

| 연산  |   HashMap   | LinkedHashMap |
|:---:|:-----------:|:-------------:|
| 추가  |    O(1)     |     O(1)      |
| 삭제  |    O(1)     |     O(1)      |
| 조회  |    O(1)     |     O(1)      |
LinkedHashMap은 HashMap과 사실상 동일 자료형으로 봐도 무방(코틀린은 기본적으로 LinkedHashMap을 사용)
* LinkedHashMap은 입력 순서를 보장한다.

#### 데크 시간 복잡도

| 연산  | ArrayDeque | LinkedList |
|:---:|:----------:|:----------:|
| 삽입  |    O(1)    |    O(1)    |
| 추출  |    O(1)    |    O(1)    |

데크의 경우 ArrayDeque를 주로 사용 
* ArrayDeque는 자바에서 스택과 큐를 대체하는 역할 





















