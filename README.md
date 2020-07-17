# 목차
- [java.lang.Iterable](#iterable) interface
	- [java.util.Collection](#collection) interface
		- Set interface
			- HashSet class
			- LinkedHashSet class
			- SortedSet interface
				- TreeSet class
		- List interface
			- ArrayList class
			- LinkedList class
			- Vector class
				- Stack class
		- Queue interface
			- Deque interface
				- ArrayDeque class
- java.util.Map interface
	- HashTable class
	- sortedMap interface
		- Navigable interface
			- TreeMap class
	- AbstractMap class
		- HashMap class
		- EnumMap class

## Iterable
```java
public interface Iterable<T>{
	//각각의 엘레멘트에 주어진 액션을 수행
	//모든 엘레멘트가 수행 완료되거나 exception 발생시 종료
	default void forEach(Consumer<? super T> action){//구현부 생략}
	
	//모든 엘레멘트에 대해 spliterator를 생성하여 반환
	default Spliterator<T> spliterator(){//구현부 생략}
}
```

## Collection
```java
public interface Collection<E> extends Iterable<E>{
	//이 콜렉션의 엘레멘트 갯수 반환
	int size();

	//엘레멘트가 하나도 없으면 true 반환
	boolean isEmpty();

	//특정 엘레멘트를 포함하고 있으면 true 반환
	boolean contains(Object o);
	
	//이 콜렉션의 엘레멘트를 가진 Iterator 객체 반환. 순서 보장 없음
	Iterator<E> iterator();

	//모든 엘레멘트를 포함한 배열 반환. iterator가 특정한 순서를 가지면, 이 메소드 또한 동일한 순서를 보장해야 함
	Object[] toArray();
	<T> T[] toArray(T[] a);
	
	//컬렉션에 변화가 있으면 true 반환. 이 메소드를 지원하는 컬렉션은 어떤 엘레멘트가 추가될 수 있는지 제한해야 함
	boolean add(E e);

	//특정한 하나의 엘레멘트 인스턴스를 삭제. 삭제 되었다면 true 반환
	boolean remove(Object o);

	//이 컬렉션이 특정 컬렉션의 모든 엘레멘트를 포함하고 있으면 true 반환
	boolean containsAll(Collection<?> c);

	//특정 컬렉션의 모든 엘레멘트를 현재 컬렉션에 추가. 이 연산으로 현재 컬렉션에 변화가 있었으면 true 반환(합집합)
	boolean addAll(Collection<? extends E> c);

	//현재 컬렉션에서 특정 컬렉션이 가지고 있는 모든 엘레멘트 제거. 이 연산으로 현재 컬렉션에 변화가 있었으면 true 반환(차집합)
	boolean removeAll(Collection<?> c);

	//predicate를 만족하는 모든 엘레멘트 삭제. 하나라도 엘레멘트가 지워졌다면 true 반환
	default boolean removeIf(Predicate<? super E> filter){//구현부 생략}

	//특정 컬렉션에 포함된 것을 제외한 모든 엘레멘트 삭제(교집합)
	boolean retainAll(Collection<?> c);
	
	//이 컬렉션의 모든 엘레멘트 삭제 
	void clear();

	//특정 객체와 이 컬렉션의 동등성 비교
	boolean equals(Object o);
	
	//이 컬렉션의 해쉬 코드 값 반환
	int hashCode();

	//이 컬렉션에 대한 spliterator 생성
	@Override
	default Spliterator<E> spliterator(){//구현부 생략}

	//이 컬렉션을 소스로 사용하여 순차적인 stream 반환
	default Stream<E> stream(){//구현부 생략}
	
	//이 컬렉션을 소스로 사용하여 병렬 stream 반환
	default Stream<E> parallelStream(){//구현부 생략}
}
```
	
