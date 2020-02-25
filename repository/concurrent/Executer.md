Java线程池 Executer Framwork**

我们知道创建和销毁对象，是存在开销的（例如线程），这些会影响我们的性能，使用Java Executer框架可以通过线程池解决这一问题，Executer框架提供了如下能力

* 创建工作线程，同时通过队列来控制能够在这些线程执行任务的个数
* 检测导致线程意外终止的错误
* 等待线程执行完毕并获取执行结果
* 批量执行线程，并通过固定的顺序获取执行结果
* 在合适的时机启动后台线程，从而保证线程执行的结果很快反馈给用户

Executer是一个接口，主要目的就是分离任务的创建和执行，实现上面的功能点
	
	public interface Executor {
	    void execute(Runnable command);
	}
	
当需要实现自己的线程池的时候。只有实现Executor接口，并实现execute方法，最简单的实现就是创建线程执行任务

	public class MyExecutor implements Executor {
	    @Override
	    public void execute(Runnable command) {
	        new Thread(command).start();
	    }
	}

当然实际应用的Executor很少是这么简单的，需要增加类似队列，任务优先级等功能，最终实现线程池

>线程池是任务队列和工作线程的集合，这俩者结合起来实现生产者和消费者模式

Executor已经为我们提供了几个预定义的线程池实现，如下

* 固定大小的线程池，通过Executors.newFixedThreadPool(n)，n表示线程池中线程的大小

		 public static ExecutorService newFixedThreadPool(int nThreads) {
	        return new ThreadPoolExecutor(nThreads, nThreads,
	                                      0L, TimeUnit.MILLISECONDS,
	                                      new LinkedBlockingQueue<Runnable>());
	    }

* 可变大小的线程池，通过Executors.newCachedThreadPool()创建

> 当有新的任务需要处理的时候，线程会创建新的线程来处理它 <br>
> 空闲的线程会等待60s来执行新任务，当没有可执行的任务，就会自动销毁 <br>
> 因此线程池的大小会根据队列的大小变化而变化

* 单个线程的线程池，通过Executors.newSingleThreadExecutor()创建，这个线程池中永远只有一个线程串行的执行任务队列中的任务

以上就是预定义的线程池，它们都是基于ThreadPoolExecutor类的基础上构建的，而通过ThreadPoolExecutor我们可以自定义线程池的一些行为。

	public ThreadPoolExecutor(int corePoolSize,
	                              int maximumPoolSize,
	                              long keepAliveTime,
	                              TimeUnit unit,
	                              BlockingQueue<Runnable> workQueue)
	                              
	                              
  * corePoolSize：核心线程数。核心线程会一直存在线程池中，即使当前没有任务需要处理，当线程数小于核心线程数，即使当前有空闲的线程，线程池也会优先创建新的线程来执行新任务，这样做的好处是无需缓存
  * maximumPoolSize：最大线程数。当线程数大于核心线程数，而且任务队列已经满了，这是线程池会创新新的线程来执行任务，直到线程数等于最大线程数为止
  * keepAliveTime：线程空闲存活时间。当线程的空闲时间超过keepAliveTime时间时，线程会被销毁，知道线程数等于核心线程数
  * unit：keepAliveTime的时间单位。可选的值有TimeUnit的枚举值
  * workQueue 线程池锁使用的任务缓存队列

[Java多线程：彻底搞懂线程池](https://www.jianshu.com/p/42cba30e1ba6)
