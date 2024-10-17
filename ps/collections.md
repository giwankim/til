# 자주 사용하는 컬렉션 프레임워크 인터페이스

## 스택

### 레거시

자바의 컬렉션 프레임워크에는 `Stack`이라는 클래스가 존재하고 생성자 외에 다음과 같은 메서드를 갖고 있다.

| Modifier and Type | Method | Description |
| ----------------- | ------ | ----------- |
| boolean | empty() | Tests if this stack is empty. |
| E | peek() | Looks at the object at the top of this stack without removing it from the stack. |
| E | pop() | Removes the object at the top of this stack and returns that object as the value of this function. |
| E | push(E item) | Pushes an item onto the top of this stack. |
| int | search(Object o) | Returns the 1-based position where an object is on this stack. |

하지만, `Stack`클래스 선언을 보면

```java
public class Stack<E> extends Vector<E>
```

thread-safe한 `Vector`클래스를 확장해서 구현되어 있음을 확인할 수 있다.

`Stack`클래스는 컬렉션 프레임워크가 등장한 자바 1.2 (1998년) 이전에 아직 CPU 코어가 하나밖에 없던 시절 나온 자료형으로, 모든 작업에 lock이 수행되는 `Vector`클래스를 기반으로 한다. 따라서 요즘처럼 멀티코어가 당연한 시대에는 성능에 문제가 있다.

### `Deque`

그래서 SDK Javadoc에서는

> A more complete and consistent set of LIFO stack operations is provided by the Deque interface and its implementations, which should be used in preference to this class. For example:

```java
Deque<Integer> stack = new ArrayDeque<>();
```

즉, `Deque`인터페이스를 구현하는 `ArrayDeque`나 `LinkedList`를 사용할 것을 추천하고 있다.

실제로 `Deque`인터페이스에는 `push(e)`, `pop()`, `peek()`의 메소드가 정의되어 있으며 다음과 같이 `Deque`의 메소드와 상응한다.

| Stack Method | Equivalent `Deque` Method |
| ------------ | ------------------------- |
| push(e) | addFirst(e) |
| pop() | removeFirst() |
| peek() | getFirst() |

스택이 비어있는 경우, `peek()`은 `null`을 리턴하지만 `pop()`은 `NoSuckElementException`을 던진다.

### Thread-safe

그헐다면 thread-safe한 자료형이 필요할 경우 `Stack`을 사용해야할까?

그렇지 않다. 새로운 자료형으로 성능을 개선한 `ConcurrentLinkedDeque`나 `LinkedBlockDeque`를 사용하면 된다.

## 큐

## 데크

## 우선순위 큐
