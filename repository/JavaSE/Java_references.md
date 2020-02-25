**Android 强引用（strong referenced），弱引用（WeakReference），软应用（soft referenced），LruCache(内存缓存)区别和联系**

1. 强引用（strong referenced）：StrongReference 是 Java 的默认引用实现,  它会尽可能长时间的存活于 JVM 内， 当没有任何对象指向它时 GC 执行后将会被回收

2. 弱引用（WeakReference）：当所引用的对象在 JVM 内不再有强引用时, GC 后 weak reference 将会被自动回收
 WeakHashMap 使用 WeakReference 作为 key， 一旦没有指向 key 的强引用, WeakHashMap 在 GC 后将自动删除相关的 entry

3. 软应用（soft referenced）：SoftReference 于 WeakReference 的特性基本一致， 最大的区别在于 SoftReference 会尽可能长的保留引用直到 JVM 内存不足时才会被回收(虚拟机保证), 这一特性使得 SoftReference 非常适合缓存应用

4. 内存缓存(LruCache)：常用于缓存bitmap,实现方式是[LinkedHashMap]保存最近引用的对象，并且在缓存超出设置大小的时候剔除（evict）最近最少使用到的对象。

5. Disk Cache(磁盘缓存)：
