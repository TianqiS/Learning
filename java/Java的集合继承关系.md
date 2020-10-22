### Java的集合继承关系

![1010726-20170621004734695-988542448](https://images2015.cnblogs.com/blog/1010726/201706/1010726-20170621004734695-988542448.png)

#### Iterator接口

Iterator接口，这是一个用于遍历集合中元素的接口，主要包含hashNext(),next(),remove()三种方法。它的一个子接口LinkedIterator在它的基础上又添加了三种方法，分别是add(),previous(),hasPrevious()。也就是说如果是先Iterator接口，那么在遍历集合中元素的时候，只能往后遍历，被遍历后的元素不会在遍历到，通常无序集合实现的都是这个接口，比如HashSet，HashMap；而那些元素有序的集合，实现的一般都是LinkedIterator接口，实现这个接口的集合可以双向遍历，既可以通过next()访问下一个元素，又可以通过previous()访问前一个元素，比如ArrayList

---

#### 集合和数组的关系([转自掘金](https://juejin.im/entry/6844903741229891597))

![截屏2020-10-21 下午7.35.11](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjx6q4fc4bj30y20asq71.jpg)



#### 集合类型

java中集合类型分为`Set`、`List`、`Map`、`Queue`四种

#### 集合的继承关系

![截屏2020-10-21 下午7.39.14](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjx6uc7rkqj31ek0fgtc9.jpg)

![截屏2020-10-21 下午7.39.38](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjx6uqc01lj30x00ca771.jpg)

#### Collection接口

```java
Boolean add(Object o)； // 向集合中添加一个对象的引用

Boolean remove(Object o)； // 从集合中删除一个对象的引用
Void clear()；// 删除集合中的所有对象，即不再持有这些对象的引用

Boolean contains(Object o)；// 判断在集合中是否持有特定对象
Object[] toArray()；// 返回1个数组，该数组包含集合中的所有元素

Iterator iterator()； // 返回1个Iterator对象：用于遍历集合中的元素

Boolean isEmpty()；// 判断集合是否为空
Int size()；// 返回集合中元素的数目

Boolean equals(Object o)； // 对象比较
Int hashCode() ；// 返回hash码 
```

与collection的区别

![截屏2020-10-21 下午7.44.25](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjx6zoj5baj30xo0b0q6h.jpg)

**Collections的使用**

```java
// 主要功能有：搜索元素、获取最大最小值、排序集合、对象线程安全化、将1个List所有元素复制到另1个 等
// 1. 获取最大最小值
static T max(Collection<? extends T> coll, Comparator<? super T> comp); 
static T min(Collection<? extends T> coll, Comparator<? super T> comp); 

// 2. 排序 
static void sort(List<T> list, Comparator<? super T> c); 

// 3. 将线程不安全的Collection转为线程安全的Collection 
static Collection<T> synchronizedCollection(Collection<T> c); 
static List<T> synchronizedList(List<T> list); 
static Map<K,V> synchronizedMap(Map<K,V> m); 
static Set<T> synchronizedSet(Set<T> s);

// 交换指定位置的两个元素 
static void swap(List<?> list, int i, int j) 

// 主要操作对象 = 集合类 = `Collection`接口的实现类
List list = new ArrayList(); 
list.add(XXX); 
//··· 
list.add(XXX); 
Collectoions.sort(list);
```

- List的接口

  ![截屏2020-10-21 下午8.10.32](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjx7qv62hlj30wa0n20xc.jpg)

  ```java
  void add(int index, Object element)； // 将元素elment插入在集合list的index处
  boolean addAll(Collection<? extends E> c);  // 将集合c 中的所有元素都插入到列表中的指定位置index处
  
  E set(int index, E element); // 将集合中index索引处的元素替换成element
  
  Object get(int index)； // 返回List集合index索引处的元素
  Int indexOf(Object o) ；// 返回集合中元素o的index索引
  int lastIndexOf(Object o)； // 返回集合中最后一个元素的索引
  List subList(int fromIndex,int toIndex)；// 返回集合中从索引fromIndex到toIndex索引处的元素集合
  
  Object remove(int index)；// 删除集合index索引处的元素
  
  void clear();  // 清空集合
  int size();  // 获得集合长度
  boolean isEmpty(); // 判断集合是否为空
  ```

- Set的接口

  ![截屏2020-10-21 下午8.10.51](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjx7r6yi6ij30xu0jm0x8.jpg)

  ```java
  boolean add(E e);  // 添加元素
  boolean remove(Object o);  // 删除元素
  
  boolean addAll(Collection<? extends E> c); // 插入集合c 中的所有元素到集合中
  boolean removeAll(Collection<?> c);  // 移除原集合中的集合c中的元素
  
  boolean contains(Object o);  // 判断是否包含该元素
  boolean containsAll(Collection<?> c);  // 判断是否包含该集合中的元素
  
  Object[] toArray();  // 返回1个数组，该数组包含集合中的所有元素
  
  void clear();  // 清空集合
  int size();  // 获得集合长度
  boolean isEmpty(); // 判断集合是否为空
  ```

- Queue接口

  ![截屏2020-10-21 下午8.12.42](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjx7t4zrhrj30zo0iodkt.jpg)

  ```java
  //插入新元素到队列：插入成功则返回true； 若队列已满，抛出IllegalStateException异常 
  boolean add(E e); 
   
  //插入新元素到队列，若插入成功则返回true ；若队列已满，返回false，但不抛出异常 
  boolean offer(E e); 
   
  E remove();  // 返回第1个元素 & 从队列中删除；若队列为空，抛出异常 
  E poll();  // 返回第1个元素 & 从队列中删除 ；若队列为空，返回null，但不抛出异常 
  E element();  //返回的第1个元素 & 不从队列中删除；若队列为空，则抛异常 
  E peek(); // 返回第1元素 & 不从队列中删除；若队列为空，则返回null
  
  // 时间复杂度分析
  // 1. 插入方法：offer()、poll()、remove() 、add ()= O(log(n)) 
  // 2. 删除方法：remove() 、 contains()  = O(n) 
  // 3. 检索方法：peek()、element() 、size() = O(1)
  
  
  // 使用建议
  // 1. 遍历时，若使用但不需删除元素，使用element()、peek()（时间效率高）
  // 2. 使用offer（）加入元素、poll（）取出元素
      // a. 避免使用Collection的add() 、remove()
      // b. 原因 = 通过返回值可判断成功与否，但add()、remove()在失败时会抛出异常
  ```

#### Map相关

![截屏2020-10-21 下午8.45.56](https://tva1.sinaimg.cn/large/007S8ZIlgy1gjx8rp1ublj311c0l2gp8.jpg)

```java
Set<K> keySet();  // 单独抽取key序列，将所有key生成一个Set
Collection<V> values();  // 单独value序列，将所有value生成一个Collection
 
V remove(Object key);  // 删除该映射关系
V get(Object key); // 获得指定键的值
V put(K key, V value);  // 添加键值关系
void putAll(Map<? extends K, ? extends V> m);  // 将指定Map中的映射关系 复制到 此Map中

boolean containsKey(Object key); // 若存在该键的映射关系，则返回true
boolean containsValue(Object value);  // 若存在该值的映射关系，则返回true
 
void clear(); // 清除所有映射关系
int size();  // 返回键值关系的数量
boolean isEmpty(); // 若未包含键值关系，则返回true


// Map中还包括1个内部类：Entry
// 该类封装了一个key-value对
// Entry类包含如下方法：
boolean equals（Object o ）；// 比较指定对象 与 此项 的相等性

K getKey（）；// 返回 与 此项 对应的键
V getValue（）；// 返回 与 此项 对应的值

int hashCode（）；// 返回此映射项的哈希值
V setValue（V value） ；// 使用指定值替换 与 此项对应的值
```



