
# JDK8-java.util-analysis
java.util에서 제공하는 인터페이스&amp;자료구조 분석

## Iterable
```java
public interface Iterable<T>
```
- specs
	```java
	default void forEach(Consumer<? super T> action)
	```
	- 각각의 엘레멘트에 주어진 액션을 수행
	- 모든 엘레멘트가 수행 완료되거나 exception 발생시 종료
	```java
	default Spliterator<T> spliterator()
	```
	- 모든 엘레멘트에 대해 spliterator를 생성하여 반환

## Collection
```java
public interface Collection<E> extends Iterable<E>
```
- specs
	```java
	int size();
	```
	- 이 콜렉션의 엘레멘트 갯수 반환
	```java
	boolean isEmpty();
	```
	- 엘레멘트가 하나도 없으면 true 반환
	```java
	boolean contains(Object o);
	```
	- 특정 엘레멘트를 포함하고 있으면 true 반환
	```java
	Iterator<E> iterator();
	```
	- 이 콜렉션의 엘레멘트를 가진 Iterator 객체 반환. 순서 보장 없음
	```java
	Object[] toArray();
	```
	- 모든 엘레멘트를 포함한 배열 반환.
- 작성중...
	
	
	
