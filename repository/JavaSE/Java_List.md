**ArrayList Vector LinkedList的区别及其优缺点**

**一，特点**

1,ArrayList,LinkedList,Vector这三个类都实现了java.util.List接口，但它们有各自不同的特性

2,ArrayList Vector内部实现都是数组，LinkedList是一个双向列表

3，LinkedList 是基于链表结构实现，所以在类中包含了 first 和 last 两个指针(Node)。Node 中包含了上一个节点和下一个节点的引用，这样就构成了双向的链表。每个 Node 只能知道自己的前一个节点和后一个节点，但对于链表来说，这已经足够了。

4，Vector是线程安全的，适合多线程下的编程。ArrayList,LinkedList是线程不安全的，适合单线程下的编程。因此Vector的操作比ArrayList,LinkedList开销要大

5，LinkedList适合增删操作，因为只需要改变指针，而查询某个元素必须从第一个开始，因此时间复杂度是O(n)。ArrayList 适合查询操作，查询某个元素的时间复杂度是O(1);


**二, 如何有效的遍历一个list**

1,enhanced for loop是编译的时候变成了使用iterator去调用。会创建垃圾的。但是却不可以用来做添加删除操作。

2,如果是ArrayList的话，直接用普通的for循环最好。

3,如果是LinkedList，不确定size的话可以先判断size是否大于0，

4,有删除添加操作的话就直接用iterator

5,使用普通的 for loop去遍历linkedList效率是很低的

6,list的遍历：

推荐使用普通的For Loop访问时间是O（1），不会创建任何对象

LinkedList，推荐使用Enhanced for loop

对于未知的List泛型，使用Enhanced for loop，性能最好

LinkedList适用于频繁的增删操作的情况

**三，Iterator和ListIterator的区别**

　　最近看到集合类，知道凡是实现了Collection接口的集合类，都有一个Iterator方法，用于返回一个实现了Iterator接口的对象，用于遍历集合；（Iterator接口定义了3个方法分别是hasNext（），next（），remove（）；）　　

　　我们在使用List,Set的时候，为了实现对其数据的遍历，我们经常使用到了Iterator(迭代器)。使用迭代器，你不需要干涉其遍历的过程，只需要每次取出一个你想要的数据进行处理就可以了。

　　但是在使用的时候也是有不同的。List和Set都有iterator()来取得其迭代器。对List来说，你也可以通过listIterator()取得其迭代器，两种迭代器在有些时候是不能通用的，Iterator和ListIterator主要区别在以下方面：

　　1. iterator()方法在set和list接口中都有定义，但是ListIterator（）仅存在于list接口中（或实现类中）；

　　2. ListIterator有add()方法，可以向List中添加对象，而Iterator不能

　　3. ListIterator和Iterator都有hasNext()和next()方法，可以实现顺序向后遍历，但是ListIterator有hasPrevious()和previous()方法，可以实现逆向（顺序向前）遍历。Iterator就不可以。

　　4. ListIterator可以定位当前的索引位置，nextIndex()和previousIndex()可以实现。Iterator没有此功能。

　　5. 都可实现删除对象，但是ListIterator可以实现对象的修改，set()方法可以实现。Iierator仅能遍历，不能修改。　　

　　因为ListIterator的这些功能，可以实现对LinkedList等List数据结构的操作。其实，数组对象也可以用迭代器来实现。

