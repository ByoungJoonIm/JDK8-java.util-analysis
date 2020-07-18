
# 목차
- [의미 분석](#의미-분석)
- [코드 분석](#코드-분석)
    - [java.lang.Iterable](#iterable) interface
        - [java.util.Collection](#collection) interface
            - [AbstractCollection](#abstract-class) abstract class
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
### interface
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
### abstract class
```java
//Collection interface의 몇몇 메소드가 미리 구현되어 있다.
public abstract class AbstractCollection<E> implements Collection<E> {
/*
미구현부
*/

    //default 생성자
    protected AbstractCollection() {}

    //iterator 생성 부분
    public abstract Iterator<E> iterator();

    //사이즈 반환
    public abstract int size();

/*
구현부
*/

    //해당 컬렉션이 비어있으면 true 반환
    public boolean isEmpty() {
        return size() == 0;
    }

    //iterator를 활용하여 순차적으로 접근하며 해당 요소가 포함된 요소인지 검사
    public boolean contains(Object o) {
        Iterator<E> it = iterator();
        if (o==null) {
            while (it.hasNext())
                if (it.next()==null)
                    return true;
        } else {
            while (it.hasNext())
                if (o.equals(it.next()))
                    return true;
        }
        return false;
    }

    // 이 배열 사이즈를 넘어가면 OutOfMemoryError 발생
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

    //이터레이션 중간에 기대했던 사이즈보다 엘레멘트가 늘어난 경우 처리하는 루틴
    @SuppressWarnings("unchecked")
    private static <T> T[] finishToArray(T[] r, Iterator<?> it) {
        int i = r.length;        //배열의 원래 사이즈
        while (it.hasNext()) {    
            int cap = r.length;                        //캡은 늘어난 배열 사이즈를 의미
            if (i == cap) {                            //배열이 꽉 찬 경우
                int newCap = cap + (cap >> 1) + 1;    //새로운 배열은 약 1.5배 늘어난 사이즈가 됨
                // overflow-conscious code
                /*
                1. newCap > Integer.MAX_VALUE(overflow) ===> underflow가 일어나 조건이 참이됨
                2. MAX_ARRAY_SIZE < newCap <= Integer.MAX_VALUE ===> 연산 결과가 양수로 참이됨
                */
                if (newCap - MAX_ARRAY_SIZE > 0)    
                    newCap = hugeCapacity(cap + 1);
                    /*
                    newCap이 hugeCapacity에서 Integer.MAX_VALUE를 할당받은 경우 VM이
                    Arrays.copyOf에서 java.lang.OutOfMemoryError를 발생시킨다.
                    따라서 newCap의 최대 크기는 MAX_ARRAY_SIZE로 고정된다.
                    cap은 항상 MAX_ARRAY_SIZE 이하이므로 MAX_ARRAY_SIZE < Integer.MAX_VALUE 라면
                    hugeCapacity에서는 overflow가 일어나지 않지만,
                    MAX_ARRYA_SIZE가 추후 변경될 수도 있는 점이 hugeCapacity에 반영된듯 하다.
                    (minCapacity < 0 부분) 
                    */
                    
                r = Arrays.copyOf(r, newCap);        //배열의 크기를 확장 및 복사. 빈 공간은 null로 패딩
            }
            r[i++] = (T)it.next();        
        }
        // trim if overallocated
        return (i == r.length) ? r : Arrays.copyOf(r, i);    //과할당된(null로 패딩된) 부분을 제거하고 반환
    }

    //VM에 따른 에러 발생 처리 루틴
    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError
                ("Required array size too large");
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }


    //병렬처리로 컬렉션이 iteration 중간에 사이즈가 변경되더라도 안전한 결과를 반환한다.
    public Object[] toArray() {
        // Estimate size of array; be prepared to see more or fewer elements
        Object[] r = new Object[size()];
        Iterator<E> it = iterator();    //이터레이션 중간에 사이즈가 변경될 수 있음
        for (int i = 0; i < r.length; i++) {
            if (! it.hasNext())         // 사이즈가 줄어든 경우 지금까지 채운 배열만 반환
                return Arrays.copyOf(r, i);
            r[i] = it.next();
        }
        return it.hasNext() ? finishToArray(r, it) : r;    //사이즈가 늘어난 경우는 새로운 루틴을 적용
    }

    //T[] a의 타입으로 toArray를 수행하여 반환. 다음과 같이 사용할 수 있다.
    //String[] y = x.toArray(new String[0]); 
    @SuppressWarnings("unchecked")
    public <T> T[] toArray(T[] a) {
        // Estimate size of array; be prepared to see more or fewer elements
        int size = size();
        T[] r = a.length >= size ? a :
                  (T[])java.lang.reflect.Array
                  .newInstance(a.getClass().getComponentType(), size);
        Iterator<E> it = iterator();

        for (int i = 0; i < r.length; i++) {
            if (! it.hasNext()) { // fewer elements than expected
                /*
                1) a의 크기보다 컬렉션의 크기가 더 작아서 a를 그대로 활용한 경우 
                    -> 마지막 유효하지 않은 엘레멘트만 null로 변경 후 리턴
                2) 컬렉션 크기가 더 커서 r에 새로 메모리를 할당받고 복사하다가 a의 길이보다 더 길어진 경우
                    -> iterator 길이에 맞춰서 새로 배열을 만들어서 반환
                3) 컬렉션 크기가 더 커서 r에 새로 메모리를 할당받았는데 a의 길이보다 짧은곳에서 끝난 경우
                    -> r의 내용을 유요한 곳까지 a에 복사한 다음 유효하지 않은 데이터가 시작되는 부분을 null로 마크
                */
                if (a == r) {
                    r[i] = null; // null-terminate
                } else if (a.length < i) {
                    return Arrays.copyOf(r, i);
                } else {
                    System.arraycopy(r, 0, a, 0, i);
                    if (a.length > i) {
                        a[i] = null;
                    }
                }
                return a;
            }
            r[i] = (T)it.next();
        }
        // more elements than expected
        return it.hasNext() ? finishToArray(r, it) : r;
    }


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

