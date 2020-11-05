### Collection

---

A collection represents a group of objects known as its elements.

- Colletions Framework

  A collections framework is a unified architecture for representing and manipulating collections. All collections frameworks contain the following:

  - Interfaces: These are abstract data types that represent collections. Interfaces allow collections to be manipulated independently of the details of their representation. In object-oriented languages, interfaces generally form a hierarchy.
  - Implementations: These are the concrete implementations of the collection interfaces. In essence, they are reusable data structures.
  - Algorithms: These are the methods that perform useful computations, such as searching and sorting, on objects that implement collection interfaces. The algorithms are said to be polymorphic: that is, the same method can be used on many different implementations of the appropriate.

- Interface

  ![截屏2020-11-05 下午2.16.31](https://tva1.sinaimg.cn/large/0081Kckwgy1gke9t6wcxbj30ho074abs.jpg)

  - The core collection interfaces encapsulate different types of collections

  - These interfaces allow collections to be manipulated independently of

    the details of their representation.

  - Core collection interfaces are the foundation of the Java Collections Framework.

  注意：

  Note that all the core collection interfaces are generic. such as:

  `public interface Collection<E>`The <E> syntax tells you that the interface is generic.

  **When you declare a Collection instance you can and should specify the type of object contained in the collection.**

- Collection---the root of the collection hierarchy

  - A collection can contain references to two equal objects (a.equals (b))

    as well as two references to the same object (a == b).

  - An object can belong to several collections.

  - An object can change while in a collection (unless it is immutable).

- Set---a collection that cannot contain duplicate elements.

- SortedSet — a Set that maintains its elements in ascending order.

- List — an ordered collection (sometimes called a sequence).

- Queue

  - a collection used to hold multiple elements prior to processing. Besides basic Collection operations, a Queue provides additional insertion, extraction, and inspection operations.
  - Queues typically, but do not necessarily, order elements in a FIFO (first-in, first-out) manner. Among the exceptions are priority queues, which order elements according to a supplied comparator or the elements' natural ordering.
  - **Every Queue implementation must specify its ordering properties.**

- Deque

  - In a deque all new elements can be inserted, retrieved and removed at both ends.

- Map

- SortedMap

  -  a Map that maintains its mappings in ascending key order.
  - Sorted maps are used for naturally ordered collections of key/value pairs, such as dictionaries and telephone directories.

- Collection Interface and Implementation

  ![截屏2020-11-05 下午2.27.22](https://tva1.sinaimg.cn/large/0081Kckwgy1gkea4lb4ztj315w0u0dkt.jpg)

- Colleciton, Iterator

  ![截屏2020-11-05 下午2.29.30](https://tva1.sinaimg.cn/large/0081Kckwgy1gkea6oc1aqj31cy0mcjum.jpg)

- Collection<E> Methods

  ![截屏2020-11-05 下午2.30.08](https://tva1.sinaimg.cn/large/0081Kckwgy1gkea7a69ioj31ew0tsagu.jpg)

- Iterator<E> Methods

  ![截屏2020-11-05 下午2.33.47](https://tva1.sinaimg.cn/large/0081Kckwgy1gkeab5ok59j31bw0pujv0.jpg)

- Iterator <=> "For Each" Loop

  ```java
  Collection<String> words = new ArrayList<>();
  for(String word : words) {}
  // A “for each” loop is a syntactic shortcut that replaces an iterator
  
  iterator<String> iter = words.iterator();
  while(iter.hasNext()) {
    String word = iter.next();
  }
  ```

- Iterators

  - An Iterator is an object that enables you to traverse through a collection and to remove elements from the collection selectively.

  - The remove method may be called **only once** per call to next and throws an exception if this rule is violated.

  - **The remove method removes the last element that was returned by next from the underlying Collection.**（删除的元素）

  - **Note that Iterator.remove is the only safe way to modify a collection during iteration.**

    (唯一安全)

  - 下列方法是不确定的

    ![截屏2020-11-05 下午2.39.29](https://tva1.sinaimg.cn/large/0081Kckwgy1gkeah1o55rj30ly06kwf3.jpg)

    因为在删除的时候list的size已经发生了改变

- Lists, ListIterator

  - ListIterator is an extended iterator, specific for lists (ListIterator is a subinterface of Iterator).
  
  ![截屏2020-11-05 下午2.41.02](https://tva1.sinaimg.cn/large/0081Kckwgy1gkeaiuyoa5j315k0pi0vn.jpg)
  
- List<E> Mehods

  ![截屏2020-11-05 下午2.41.40](https://tva1.sinaimg.cn/large/0081Kckwgy1gkeajal28cj31c60n6446.jpg)

- ListIterator<E> Methods

  ![截屏2020-11-05 下午2.42.41](https://tva1.sinaimg.cn/large/0081Kckwgy1gkeakif4cnj31a00qojx1.jpg)

- ![截屏2020-11-05 下午2.44.22](https://tva1.sinaimg.cn/large/0081Kckwgy1gkeam4p98yj317g0kydib.jpg)

- ArrayList

  ![截屏2020-11-05 下午3.04.54](https://tva1.sinaimg.cn/large/0081Kckwgy1gkeb7h1lvvj315e0n0q61.jpg)

- LinkedList

  ![截屏2020-11-05 下午3.09.11](https://tva1.sinaimg.cn/large/0081Kckwgy1gkebbxlb6nj312o0pijue.jpg)

  Additional methods specific to LinkedList:

  ![截屏2020-11-05 下午3.09.36](https://tva1.sinaimg.cn/large/0081Kckwgy1gkebccq5yaj30s40ek404.jpg)

- List Algorithms

  Most algorithms in the **Collections**(注意是Colletions) class apply specifically to List.

  ![截屏2020-11-05 下午3.19.21](https://tva1.sinaimg.cn/large/0081Kckwgy1gkebmjo5zrj316o0eeq5u.jpg)

  ![截屏2020-11-05 下午3.19.28](https://tva1.sinaimg.cn/large/0081Kckwgy1gkebmnps4uj315k0gstbl.jpg)

  ![截屏2020-11-05 下午3.19.50](https://tva1.sinaimg.cn/large/0081Kckwgy1gkebn0fb1vj31280nywu7.jpg)

- Comparable Interface

  ![截屏2020-11-05 下午3.24.18](https://tva1.sinaimg.cn/large/0081Kckwgy1gkebro4dr2j31620hw0w9.jpg)

- Queues

  ![截屏2020-11-05 下午3.25.27](https://tva1.sinaimg.cn/large/0081Kckwgy1gkebsv8de3j314y0eiac3.jpg)

  ```java
  Queue<Integer> q = new LinkedList<Integer>();
  // or
  Queue<Integer> q = new ArrayDeque<Integer>();
  ```

- Deque双端队列

  ![截屏2020-11-05 下午3.30.35](https://tva1.sinaimg.cn/large/0081Kckwgy1gkeby91dogj315o0q842h.jpg)

- TreeSet<E>(升序)

  ![截屏2020-11-05 下午3.38.38](https://tva1.sinaimg.cn/large/0081Kckwgy1gkec6kne65j315s0880ub.jpg)

  使用二叉查找树

- HashSet<E>(无序)

  ![截屏2020-11-05 下午3.39.40](https://tva1.sinaimg.cn/large/0081Kckwgy1gkec7ni53gj311m0bcabs.jpg)

- LinkedHashSet(有序的,按照插入顺序)

  ![截屏2020-11-05 下午3.44.14](https://tva1.sinaimg.cn/large/0081Kckwgy1gkeccf45t9j316e0c6q57.jpg)

- Maps(不是collection)

  ![截屏2020-11-05 下午3.46.33](https://tva1.sinaimg.cn/large/0081Kckwgy1gkecetg0n6j31380lomzy.jpg)

- Map<K, V> Methods

  ![截屏2020-11-05 下午3.48.48](https://tva1.sinaimg.cn/large/0081Kckwgy1gkech7va84j31620ms78d.jpg)

  `entrySet(返回一个集合，因为map本身不是一个集合)`的使用方法：

  ![截屏2020-11-05 下午3.51.03](https://tva1.sinaimg.cn/large/0081Kckwgy1gkecji72lhj30ya0agabr.jpg)

- TreeMap<K, V>

  ![截屏2020-11-05 下午3.49.16](https://tva1.sinaimg.cn/large/0081Kckwgy1gkechmtyzpj314m08m3zx.jpg)
  
- HashMap<K, V>
  
  ![截屏2020-11-05 下午3.52.06](https://tva1.sinaimg.cn/large/0081Kckwgy1gkeckktsk3j314q08ywfx.jpg)
  
- LinkedHashMap

  ![截屏2020-11-05 下午3.53.59](https://tva1.sinaimg.cn/large/0081Kckwgy1gkecmjfiagj314g0cugo2.jpg)

  

