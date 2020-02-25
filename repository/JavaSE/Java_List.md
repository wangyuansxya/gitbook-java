**ArrayList Vector LinkedList的区别及其优缺点**

一，特点

1,ArrayList,LinkedList,Vector这三个类都实现了java.util.List接口，但它们有各自不同的特性

2,ArrayList Vector内部实现都是数组，LinkedList是一个双向列表

3，LinkedList 是基于链表结构实现，所以在类中包含了 first 和 last 两个指针(Node)。Node 中包含了上一个节点和下一个节点的引用，这样就构成了双向的链表。每个 Node 只能知道自己的前一个节点和后一个节点，但对于链表来说，这已经足够了。

4，Vector是线程安全的，适合多线程下的编程。ArrayList,LinkedList是线程不安全的，适合单线程下的编程。因此Vector的操作比ArrayList,LinkedList开销要大

5，LinkedList适合增删操作，因为只需要改变指针，而查询某个元素必须从第一个开始，因此时间复杂度是O(n)。ArrayList 适合查询操作，查询某个元素的时间复杂度是O(1);


二, 如何有效的遍历一个list

1,enhanced for loop是编译的时候变成了使用iterator去调用。会创建垃圾的。但是却不可以用来做添加删除操作。

2,如果是ArrayList的话，直接用普通的for循环最好。

3,如果是LinkedList，不确定size的话可以先判断size是否大于0，

4,有删除添加操作的话就直接用iterator

5,使用普通的 for loop去遍历linkedList效率是很低的

