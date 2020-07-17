
# 목차
- [의미 분석](#의미-분석)
- [코드 분석](#코드-분석)
	- [java.lang.Iterable](#iterable) interface
		- [java.util.Collection](#collection) interface
			- AbstractCollection abstract class
			- [Set](#set) interface
				- Abstract set abstract class
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

# 의미 분석
- interface와 abstract class를 실제로 어떻게 활용할까?
  - Collection이라는 spec을 구현해야 한다고 생각해보자. 구현이 강제되는 메소드는 총 15개, 자동으로 구현되는 메소드는 4개(default)이다. 이를 매번 실제로 구현하다보면, 구현부가 항상 비슷한 메소드가 존재한다. 예를 들어 `boolean isEmpty()` 메소드는 size가 0일때만 true를 반환하는데, 이를 매번 구현할 필요는 없다. AbstractCollection 에서는 이렇게 자주 같은 기능으로 사용되는 메소드를 미리 구현해 놓았다.
	  - `boolean contains(Object o)`, `Object[] toArray()`, `boolean remove(Object o)` 등은 iterator를 활용해서 순차적으로 하나씩 엘레멘트를 살피면서 결과를 도출한다. 따라서 iterator를 어떻게 얻을 것인지는 아직 모르지만, iterator를 통해서 해야할 고정된 작업들은 미리 정의를 해 놓을 수 있다.
  - 마찬가지로 AbstractSet은 AbstactCollection을 상속한다. AbstractCollection에서는 Collection이기 위한 작업중 미리 정의할 수 있는 작업이 정의되어 있다면, AbstractSet에서는 추가적으로 'Set'이기 위한 미리 정의해 놓을 수 있는 작업을 정의해 놓을 수 있다. 즉, AbstractClass들을 사용하면서 interface의 spec을 구현함에 있어서 최소한의 노력으로 목적에 맞는 코드를 작성할 수 있게 되는 것이다. 
  - interface는 '너가 Collection이라는 성질을 띠려면 이 15개의 메소드는 있어야해!'라는 느낌
  - abstract class는 '너가 Collection이라는 성질을 띠려면 원래 15개의 메소드가 필요한데, 그중에 대부분은 내가 미리 만들어 놓을게! 맘에 안들면 고치고, 넌 그냥 한 5개만 구현하면 충분해!'라는 느낌

# 코드 분석
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

## Set
```java
//중복 엘레멘트를 포함하지 않는 컬렉션. 최대 1개의 null 엘레멘트를 가질 수 있다.
public interface Set<E> extends Collection<E> {
	/*
	Collection interface와 동일하며 다음과 같은 차이가 있다.
	1. removeIf, stream, parallelStream은 Collection의 default를 그대로 사용
	2. spliterator() 재정의
	*/
}
```
