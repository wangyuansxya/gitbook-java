AtomicInteger位于java.util.concurrent.atomic包下，是对int的封装，提供原子性的访问和更新操作，其原子性操作的实现是基于CAS。

包下还有其他的类似原子操作类，AtomicBoolean，AtomicLong等等，大家可以自行查阅，实现的原理都是类似的

都是提供原子性的访问和更新操作，其原子性操作的实现是基于CAS。

**1，什么CAS算法**

* CAS(compare-and-swap)直译即比较并交换，提供原子化的读改写能力，是Java 并发中所谓 lock-free 机制的基础。
* CAS的思想很简单：三个参数，一个当前内存值V、旧的预期值A、即将更新的值B，当且仅当预期值A和内存值V相同时，将内存值修改为B并返回true，否则什么都不做，并返回false。
* 可能会有面试官问 CAS 底层是如何实现的，在JAVA中,CAS通过调用C++库实现，由C++库再去调用CPU指令集。不同体系结构中，cpu指令还存在着明显不同。比如，x86 CPU 提供 cmpxchg 指令；而在精简指令集的体系架构中，（如“load and reserve”和“store conditional”）实现的，在大多数处理器上 CAS 都是个非常轻量级的操作，这也是其优势所在。

**2，CAS的缺点**

* ABA问题

如果某个线程在CAS操作时发现，内存值和预期值都是A，就能确定期间没有线程对值进行修改吗？答案未必，如果期间发生了 A -> B -> A 的更新，仅仅判断数值是 A，可能导致不合理的修改操作。针对这种情况，Java 提供了 AtomicStampedReference 工具类，通过为引用建立类似版本号（stamp）的方式，来保证 CAS 的正确性。

* 循环时间长开销大

CAS中使用的失败重试机制，隐藏着一个假设，即竞争情况是短暂的。大多数应用场景中，确实大部分重试只会发生一次就获得了成功。但是总有意外情况，所以在有需要的时候，还是要考虑限制自旋的次数，以免过度消耗 CPU。

* 只能保证一个共享变量的原子操作

**3，AtomicInteger原理浅析**

	public class AtomicInteger extends Number implements java.io.Serializable {
	private static final long serialVersionUID = 6214790243416807050L;
	
	private static final sun.misc.Unsafe U = sun.misc.Unsafe.getUnsafe();
	private static final long VALUE;
	
	static {
	    try {
	        VALUE = U.objectFieldOffset
	            (AtomicInteger.class.getDeclaredField("value"));
	    } catch (ReflectiveOperationException e) {
	        throw new Error(e);
	    }
	}
	
	private volatile int value;
	}

从上面的代码可以看出

* 从 AtomicInteger 的内部属性可以看出，它依赖于Unsafe 提供的一些底层能力，进行底层操作；
* 如根据valueOffset代表的该变量值在内存中的偏移地址，从而获取数据的。
变量value用volatile修饰，保证了多线程之间的内存可见性。

下面以getAndIncrement为例，说明其原子操作过程

    /**
     * Atomically increments by one the current value.
     *
     * @return the previous value
     */
    public final int getAndIncrement() {
        return U.getAndAddInt(this, VALUE, 1);
    }

Unsafe的源码

    public final int getAndAddInt(Object var1, long var2, int var4) {
        int var5;
        do {
            var5 = this.getIntVolatile(var1, var2);
        } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

        return var5;
    }

* 假设线程1和线程2通过getIntVolatile拿到value的值都为1，线程1被挂起，线程2继续执行
* 线程2在compareAndSwapInt操作中由于预期值和内存值都为1，因此成功将内存值更新为2
* 线程1继续执行，在compareAndSwapInt操作中，预期值是1，而当前的内存值为2，CAS操作失败，什么都不做，返回false
* 线程1重新通过getIntVolatile拿到最新的value为2，再进行一次compareAndSwapInt操作，这次操作成功，内存值更新为3

[参考文章](https://www.jianshu.com/p/cea1f9619e8f)

