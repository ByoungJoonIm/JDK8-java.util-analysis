

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
    - [java.util.Map](#map) interface
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
- interface와 abstract class는 어떤 차이가 있을까?
  - interface는 주 목적이 spec을 기술한 것이라면, abstract class는 기능 구현에 더 중점이 맞춰져 있다.
  - 하지만 부분적으로 interface에서도 `default` 키워드를 이용하여 기능이 구현된 경우가 있다.
  - 함수 레벨의 수행(BiFunction)은 interface의 default method로 구현된다.(정확하지 않음)

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

    //구현하지 않으면 지원하지 않는다는 예외 발생 
    public boolean add(E e) {
        throw new UnsupportedOperationException();
    }
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

    // iterator로 순차적으로 탐색하다가 발견되면 삭제. 삭제 성공시 true 반환
    public boolean remove(Object o) {
        Iterator<E> it = iterator();
        if (o==null) {
            while (it.hasNext()) {
                if (it.next()==null) {
                    it.remove();
                    return true;
                }
            }
        } else {
            while (it.hasNext()) {
                if (o.equals(it.next())) {
                    it.remove();
                    return true;
                }
            }
        }
        return false;
    }

    // 내부적으로 contains 함수를 재사용. 현재 컬렉션에 모든 파라미터 컬렉션의 요소가 있을 때만 true 반환
    public boolean containsAll(Collection<?> c) {
        for (Object e : c)
            if (!contains(e))
                return false;
        return true;
    }

    // 파라미터 컬렉션을 모두 현재 컬렉션에 추가. 하나라도 추가된 경우(현재 컬렉션이 변경된 경우) true 반환. 합집합 연산
    public boolean addAll(Collection<? extends E> c) {
        boolean modified = false;
        for (E e : c)
            if (add(e))
                modified = true;
        return modified;
    }

    // 현재 컬렉션에서 파라미터 컬렉션의 요소 삭제. 하나라도 삭제된 경우 true 반환. 차집합 연산
    public boolean removeAll(Collection<?> c) {
        Objects.requireNonNull(c);
        boolean modified = false;
        Iterator<?> it = iterator();
        while (it.hasNext()) {
            if (c.contains(it.next())) {
                it.remove();
                modified = true;
            }
        }
        return modified;
    }

    // 파라미터 컬렉션과 현재 컬렉션이 공통으로 가진 요소만 남기고 삭제. 현재 컬렉션이 변경되었으면 true 반환. 교집합 연산
    public boolean retainAll(Collection<?> c) {
        Objects.requireNonNull(c);
        boolean modified = false;
        Iterator<E> it = iterator();
        while (it.hasNext()) {
            if (!c.contains(it.next())) {
                it.remove();
                modified = true;
            }
        }
        return modified;
    }

    // 컬렉션의 모든 요소 삭제
    public void clear() {
        Iterator<E> it = iterator();
        while (it.hasNext()) {
            it.next();
            it.remove();
        }
    }

    // 현재 컬렉션의 모든 요소를 다음과 같은 형태로 반환
    // "[factor1, factor2, factor3]"
    public String toString() {
        Iterator<E> it = iterator();
        if (! it.hasNext())
            return "[]";

        StringBuilder sb = new StringBuilder();
        sb.append('[');
        for (;;) {
            E e = it.next();
            sb.append(e == this ? "(this Collection)" : e);
            if (! it.hasNext())
                return sb.append(']').toString();
            sb.append(',').append(' ');
        }
    }
}	//the end of AbstractCollection
```

## Map
```java
public interface Map<K,V> {
    // key-value 쌍의 수 반환. Integer.MAX_VALUE보다 크면 Integer.MAX_VALUE 반환
    int size();

    // key-value 쌍이 하나도 없으면 true 반환
    boolean isEmpty();

    // 현재 맵이 특정 key에 대한 매핑을 가지면 true 반환
    boolean containsKey(Object key);

    // 특정 값이 존재하면 true 반환. 일반적으로 O(n)이 걸림
    boolean containsValue(Object value);

    // 특정 key에 매핑되는 value 반환
    V get(Object key);

    // 특정 키에 대한 특정 값을 할당.
    V put(K key, V value);

    // 특정 키에 대한 매핑을 삭제
    V remove(Object key);

    // 특정 map의 모든 매핑을 현재 맵에 복사
    void putAll(Map<? extends K, ? extends V> m);

/*
views
*/
    // 모든 매핑을 삭제
    void clear();

    // 이 맵에 대한 모든 키를 셋으로 반환
    Set<K> keySet();

    // 이 맵에 대한 모든 값을 컬렉션으로 반환
    Collection<V> values();

    // 이 맵에 대한 모든 매핑을 셋으로 반환
    Set<Map.Entry<K, V>> entrySet();

/*
nested Entry interface
*/    
    interface Entry<K,V> {
        // 이 엔트리의 키 반환
        K getKey();

        // 이 엔트리의 값 반환
        V getValue();

        // 현재 엔트리의 값을 특정 값으로 대체. 대체되기 전의 값을 반환함
        V setValue(V value);

        // 현재 엔트리와 특정 엔트리간의 동등성 검사 결과 반환
        boolean equals(Object o);

        // 현재 엔트리의 해쉬 코드값을 반환
        int hashCode();

        // key에 대한 natural order(알파벳 순서)인 Comparator 반환
        public static <K extends Comparable<? super K>, V> Comparator<Map.Entry<K,V>> comparingByKey() {
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> c1.getKey().compareTo(c2.getKey());
        }
        // key에 대한 Comparator를 활용하여 Entry에 대한 Comparator 반환
        public static <K, V> Comparator<Map.Entry<K, V>> comparingByKey(Comparator<? super K> cmp) {
            Objects.requireNonNull(cmp);
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> cmp.compare(c1.getKey(), c2.getKey());
        }

        // value에 대한 narual order인 Comparator 반환
        public static <K, V extends Comparable<? super V>> Comparator<Map.Entry<K,V>> comparingByValue() {
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> c1.getValue().compareTo(c2.getValue());
        }
        // value에 대한 Comparator를 활용하여 Entry에 대한 Comparator 반환
        public static <K, V> Comparator<Map.Entry<K, V>> comparingByValue(Comparator<? super V> cmp) {
            Objects.requireNonNull(cmp);
            return (Comparator<Map.Entry<K, V>> & Serializable)
                (c1, c2) -> cmp.compare(c1.getValue(), c2.getValue());
        }
    }

/*
comparison and hashing
*/

    // 특정 객체와 이 맵의 동등성 검사
    boolean equals(Object o);

    // 이 맵에 대한 hash code 값 반환
    int hashCode();

    // 이 메소드는 원자성을 보장하지 않으므로, 동기화가 필요하면 override 해야함
    // key에 대한 매핑이 존재하면 매핑에 대한 value를, 아니면 defaultValue를 반환
    default V getOrDefault(Object key, V defaultValue) {
        V v;
        return (((v = get(key)) != null) || containsKey(key))
            ? v
            : defaultValue;
    }

    // 주어진 action을 모든 엔트리에 대해 수행
    // 모두 수행되거나 exception 발생시까지 수행
    default void forEach(BiConsumer<? super K, ? super V> action) {
        Objects.requireNonNull(action);
        for (Map.Entry<K, V> entry : entrySet()) {
            K k;
            V v;
            try {
                k = entry.getKey();
                v = entry.getValue();
            } catch(IllegalStateException ise) {
                // this usually means the entry is no longer in the map.
                throw new ConcurrentModificationException(ise);
            }
            action.accept(k, v);
        }
    }

    // 주어진 function을 모든 엔트리에 대해 수행
    // 모두 수행되거나 exception 발생시까지 수행
    default void replaceAll(BiFunction<? super K, ? super V, ? extends V> function) {
        Objects.requireNonNull(function);
        for (Map.Entry<K, V> entry : entrySet()) {
            K k;
            V v;
            try {
                k = entry.getKey();
                v = entry.getValue();
            } catch(IllegalStateException ise) {
                // this usually means the entry is no longer in the map.
                throw new ConcurrentModificationException(ise);
            }

            // ise thrown from function is not a cme.
            v = function.apply(k, v);

            try {
                entry.setValue(v);
            } catch(IllegalStateException ise) {
                // this usually means the entry is no longer in the map.
                throw new ConcurrentModificationException(ise);
            }
        }
    }

    // 특정 key에 대한 매핑이 이미 할당되었거나 해당 key가 null에 매핑되어있을때 key에 대한 value를 덮어씀
    // 이전 값 반환
    default V putIfAbsent(K key, V value) {
        V v = get(key);
        if (v == null) {
            v = put(key, value);
        }

        return v;
    }

    // 특정 entry 삭제
    default boolean remove(Object key, Object value) {
        Object curValue = get(key);
        // 주어진 key에 대한 value가 주어진 value와 다르거나 특정 key가 아직 할당되지 않았으면 false 반환 및 삭제를 수행하지 않음
        if (!Objects.equals(curValue, value) ||
            (curValue == null && !containsKey(key))) {
            return false;
        }
        remove(key);
        return true;
    }

    // key에 대한 기존의 value를 새로운 value로 대체
    default boolean replace(K key, V oldValue, V newValue) {
        Object curValue = get(key);
        //값이 변경되지 않거나 매핑이 아직 생성되지 않았으면 false를 반환 및 put 연산을 수행하지 않음
        if (!Objects.equals(curValue, oldValue) ||
            (curValue == null && !containsKey(key))) {
            return false;
        }
        put(key, newValue);
        return true;
    }

    // key에 대한 value 값을 변경
    // key에 대한 기존 값 반환
    // 원자성이나 동기화를 제공하려면 override 해야함
    default V replace(K key, V value) {
        V curValue;
        // key에 대한 value가 null이 아니거나 key가 이미 할당되었을 때 put 연산 수행
        if (((curValue = get(key)) != null) || containsKey(key)) {
            curValue = put(key, value);
        }
        return curValue;
    }

    // key에 대한 매핑이 없는 경우에 연산 수행
    // 새로운 값이 할당된 경우 할당된 값 반환
    default V computeIfAbsent(K key,
            Function<? super K, ? extends V> mappingFunction) {
        Objects.requireNonNull(mappingFunction);
        V v;
        if ((v = get(key)) == null) {
            V newValue;
            if ((newValue = mappingFunction.apply(key)) != null) {
                put(key, newValue);
                return newValue;
            }
        }

        return v;
    }

    // key에 대한 매핑이 있는 경우에 연산 수행
    // 연산 결과가 null이면 해당 매핑을 삭제 및 null 반환, 존재하지 않는 매핑이어도 null 반환
    // 새로운 값이 할당된 경우 할당된 값 반환
    default V computeIfPresent(K key,
            BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        V oldValue;
        if ((oldValue = get(key)) != null) {
            V newValue = remappingFunction.apply(key, oldValue);
            if (newValue != null) {
                put(key, newValue);
                return newValue;
            } else {
                remove(key);
                return null;
            }
        } else {
            return null;
        }
    }

    // 주어진 key에 대한 연산을 수행
    // 연산의 결과가 null이 아닌 경우 연산 수행 결과를 key에 대한 value로 저장
    // 연산의 결과가 null인 경우 key에 대한 매핑이 존재 했다면 해당 매핑을 삭제
    default V compute(K key,
            BiFunction<? super K, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        V oldValue = get(key);

        V newValue = remappingFunction.apply(key, oldValue);
        if (newValue == null) {
            // delete mapping
            if (oldValue != null || containsKey(key)) {
                // something to remove
                remove(key);
                return null;
            } else {
                // nothing to do. Leave things as they were.
                return null;
            }
        } else {
            // add or replace old mapping
            put(key, newValue);
            return newValue;
        }
    }

    // 특정 키에 대한 매핑이 할당되지 않았거나, null로 할당되었다면 주어진 null이 아닌 value로 매핑 할당
    // 그 외에는 할당된 값을 remapping function으로 매핑하거나 결과가 null인 경우는 삭제
    default V merge(K key, V value,
            BiFunction<? super V, ? super V, ? extends V> remappingFunction) {
        Objects.requireNonNull(remappingFunction);
        Objects.requireNonNull(value);
        V oldValue = get(key);
        V newValue = (oldValue == null) ? value :
                   remappingFunction.apply(oldValue, value);
        if(newValue == null) {
            remove(key);
        } else {
            put(key, newValue);
        }
        return newValue;
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

