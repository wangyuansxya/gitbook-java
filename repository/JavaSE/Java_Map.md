---
layout: post
category: "android"
title:  "Android Map集合"
tags: [Map, 区别，优缺点]
---
**Android hashMap和hashTable的区别**

HashMap的内部实现是数组+链表

1，每个元素在数组中的位置都是根据key的hash值来确定的

2，由于key不同的时候hash值可能也相同，所以数组的每个个位置保存的是一个Entry对象，内部也是key,value的形式保存

3，例如我们添加元素的时候，当前位置有相同的hash的Entry对象，此时循环这个Entry对象如果有相同的key，则替换，没有则添加都表头，并且指向之前位于表头的Entry对象

4，读取元素的时候，先通过key的hash,获取元素的数组中位置，然后通过比较Entry对象的key值，如果有，则返回对应的value
	
	ps：当某个位置的链表只有一个的时候，此时的hashMap性能的最好的

5，HashMap的线程非安全的，HashTable是线程安全，因此在速度上Hashmap比HashTable快，HashTable每次都需要获得同步锁和放弃同步锁

6，HashMap的默认加载因子的0.75，其长度一般都是2的n次幂，默认长度的16,每次扩容都是*2倍

7，加载因子(HashMap最多能放size*加载因子个元素)，提高查询效率。加载因子过高虽然可以降低空间的开销，但是却提高了查询某个元素的时间

8，Java5以上，提供ConcurrentHashMap线程安全,比HashTable有更好的效率,比HashTable的扩展性更好。之所以ConcurrentHashMap更好是因为使用了局部锁，而HashTable是类锁

9，hashMap的key值可以为null值，但是hashTable不行，从效率上来说，HashMap比HashTable更好
