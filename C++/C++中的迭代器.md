### C++中的迭代器

[原文](https://blog.csdn.net/u013719339/article/details/80615217)

---

迭代器是为了表示容器中某个元素位置这个概念而产生的，是一种检查容器内元素并遍历元素的数据类型。C++更趋向于使用迭代器而非下标进行操作，因为标准库（STL）为每一种标准容器定义了一种迭代器类型，而只有少数容器支持下标操作访问容器元素。

![img](https://img-blog.csdn.net/20180612160734945?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM3MTkzMzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

因此，每种容器都定义了自己的迭代器类型，以vector为例：

```c++
//定义和初始化
vector<int>::iterator iter; //定义一个名为iter的变量
 
vector<int> ivec;
vector<int>::iterator iter1=ivec.bengin(); //将迭代器iter1初始化为指向ivec容器的第一个元素
vector<int>::iterator iter2=ivec.end(); //将迭代器iter2初始化为指向ivec容器的最后一个元素的下一个位置
 
//常用操作
*iter //对iter进行解引用，返回迭代器iter指向的元素的引用
iter->men //对iter进行解引用，获取指定元素中名为men的成员。等效于(*iter).men
++iter //给iter加1，使其指向容器的下一个元素
iter++
--iter //给iter减1，使其指向容器的前一个元素
iter--
iter1==iter2 //比较两个迭代器是否相等，当它们指向同一个容器的同一个元素或者都指向同同一个容器的超出末端的下一个位置时，它们相等
iter1!=iter2
//用迭代器来遍历ivec容器，把其每个元素重置为0
for(vector<int>::iterator iter=ivec.begin();iter!=ivec.end();++iter)
    *iter=0;
 
//只有vector和queue容器提供迭代器算数运算和除!=和==之外的关系运算
iter+n //在迭代器上加（减）整数n，将产生指向容器中钱前面（后面）第n个元素的迭代器。新计算出来的迭代器必须指向容器中的元素或超出容器末端的下一个元素
iter-n
iter1+=iter2 //??? 有争议  将iter1加上或减去iter2的运算结果赋给iter1。两个迭代器必须指向容器中的元素或超出容器末端的下一个元素
iter1-=iter2
iter1-iter2 //两个迭代器的减法，得出两个迭代器的距离。两个迭代器必须指向容器中的元素或超出容器末端的下一个元素
>,>=,<,<= //元素靠后的迭代器大于靠前的迭代器。两个迭代器必须指向容器中的元素或超出容器末端的下一个元素
vector<int>::iterator mid=v.begin()+v.size()/2; //初始化mid迭代器，使其指向v中最靠近正中间的元素
```

#### 迭代器const_iterator

每种容器还定义了一种名为const_iterator的类型，该类型的迭代器只能进行读操作，不能进行写操作。

```c++
for(vector<int>::const_iterator iter=ivec.begin();iter!=ivec.end();++iter)
     cout<<*iter<<endl;   //合法，读取容器中元素值
     *iter=0;             //不合法，不能进行写操作
```

**注意：**const iterator与const_iterator是不同的，前者在对迭代器进行声明时必须进行初始化，并且一旦初始化后及不能修改其值。

```c++
vector<int> ivec(10);
const vector<int>::iterator iter=ivec.begin();
*iter=0;    //合法，可以改变其指向的元素的值
++iter;    //不合法，无法改变其指向的位置
```

 由于删除元素或移动元素等操作会修改容器的内在状态，从而使原本指向被移动元素的迭代器失效，也可能同时使其他迭代器失效。而失效的迭代器是没有意义，且使用无效迭代器会导致严重的运行时错误，因此一定要特别留意哪些操作会导致迭代器失效。



----

### 区间迭代

区间迭代的基本语法

```c++
vector<int> vec;
vec.push_back(10);
vec.push_back(20);

for(int i : vec) {
  cout << i;
}
for (auto i : vec) {
  cout << i;
}
```



