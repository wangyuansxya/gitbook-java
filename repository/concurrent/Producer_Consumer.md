**一，使用wait()+notifyAll()的方式实现生产者和消费者**

1.生产者的实现

	public class Producer implements Runnable {
		private Proxy mProxy;
		Producer(Proxy proxy) {
		    this.mProxy = proxy;
		}
		@Override
		public void run() {
		    mProxy.produce();
		}
	}
	
2，消费者

	public class Consumer implements Runnable {
		private Proxy mProxy;
		Consumer(Proxy proxy) {
		    this.mProxy = proxy;
		}
		@Override
		public void run() {
		    mProxy.consume();
		}
	}
	
3.中间的代理商
	
	/**
	*  商品的销售商
	*/
	public interface Proxy {
		void consume();
		void produce();
	}

4.实际的销售商
	
	/**
	*  商品的具体的销售商
	*/
	public class ProxyBySynchronized implements Proxy{
	
		/**
		 * 线程锁对象
		 */
		static final String LOCK = "lock";
		/**
		 * 最大库存
		 */
		static final int ONCE_POOL = 2;
		
		/**
		 * 计划生产的商品最大数量
		 */
		static final int MAX_POOL = 8;
		
		/**
		 * 产品库存
		 */
		static final List<String> POOL_LIST = new ArrayList<>(MAX_POOL);
		
		@Override
		public void consume() {
		    synchronized (LOCK) {
		        System.out.println("开始消费了");
		        for (int i = 0; i < MAX_POOL; i++) {
		            try {
		                Thread.sleep(1000);
		            } catch (InterruptedException e) {
		                e.printStackTrace();
		            }
		            if(POOL_LIST.size() > 0) {
		                System.out.println("消费了=" + POOL_LIST.remove(0));
		            }
		            if(POOL_LIST.size() <= 0) {
		                try {
		                    System.out.println("商品消费完了， 通知生产者。当前库存=" + POOL_LIST.size());
		                    LOCK.notifyAll();
		                    LOCK.wait();
		                } catch (InterruptedException e) {
		                    e.printStackTrace();
		                }
		            }
		        }
		        System.out.println("全部消费完毕，消费结束了");
		    }
		}
		
		@Override
		public void produce() {
		    synchronized (LOCK) {
		        System.out.println("生产开始了");
		        for (int i = 0; i < MAX_POOL; i++) {
		            try {
		                Thread.sleep(1000);
		            } catch (InterruptedException e) {
		                e.printStackTrace();
		            }
		            System.out.println("生产了商品" + i);
		            POOL_LIST.add("商品" + i);
		            if(POOL_LIST.size() == ONCE_POOL) {
		                try {
		                    System.out.println("库存达到上限，当前库存=" + POOL_LIST.size());
		                    System.out.println("生产暂停了，通知消费者");
		                    LOCK.notifyAll();
		                    LOCK.wait();
		                } catch (InterruptedException e) {
		                    e.printStackTrace();
		                }
		            }
		        }
		        LOCK.notifyAll();
		        System.out.println("达到最大产量，生产结束了");
		    }
		}
	}
	
4，开始生产和消费

	Proxy proxy = new ProxyBySynchronized();
	new Thread(new Producer(proxy)).start();
	new Thread(new Consumer(proxy)).start();
	
>notifyAll()方法可使所有正在等待队列中等待同一共享资源的“全部”线程从等待状态退出，进入可运行状态。此时，优先级最高的哪个线程最先执行，但也有可能是随机执行的，这要取决于JVM虚拟机的实现。即最终也只有一个线程能被运行，上述线程优先级都相同，每次运行的线程都不确定是哪个，后来给线程设置优先级后也跟预期不一样，还是要看JVM的具体实现吧。

5.wait()和sleep()的区别

* sleep 是线程的静态方法，作用时使当前的线程暂停运行一段时间，时间到了之后继续运行
* wait 是让线程等待，等到被notify/notifyAll方法唤醒，然后继续执行，常用于线程间通信
	
**二，await() / signal()方法**
 
 在JDK5中，用ReentrantLock和Condition可以实现等待/通知模型，具有更大的灵活性。通过在Lock对象上调用newCondition()方法，将条件变量和一个锁对象进行绑定，进而控制并发程序访问竞争资源的安全。
 
 只需要实现具体的销售商就行，代码如下：
 
	 /**
	 *  商品的具体的销售商
	 */
	public class ProxyByLock implements Proxy {
	
	    /**
	     * 线程锁对象
	     */
	    private static final Lock LOCK = new ReentrantLock();
	    private static final Condition EMPTY = LOCK.newCondition();
	    /**
	     *  最大库存
	     */
	    private static final int ONCE_POOL = 2;
	    private static final Condition FULL = LOCK.newCondition();
	
	    /**
	     * 计划生产的商品最大数量
	     */
	    private static final int MAX_POOL = 8;
	
	
	    /**
	     * 产品库存
	     */
	    private static final List<String> POOL_LIST = new LinkedList<>();
	
	
	    @Override
	    public void consume() {
	        LOCK.lock();
	        System.out.println("开始消费了");
	        for (int i = 0; i < MAX_POOL; i++) {
	            try {
	                Thread.sleep(1000);
	            } catch (InterruptedException e) {
	                e.printStackTrace();
	            }
	            if(POOL_LIST.size() > 0) {
	                System.out.println("消费了=" + POOL_LIST.remove(0));
	            }
	            if(POOL_LIST.size() <= 0) {
	                try {
	                    System.out.println("商品消费完了， 通知生产者。当前库存=" + POOL_LIST.size());
	                    EMPTY.signalAll();
	                    FULL.await();
	                } catch (InterruptedException e) {
	                    e.printStackTrace();
	                }
	            }
	        }
	        System.out.println("全部消费完毕，消费结束了");
	        LOCK.unlock();
	
	    }
	
	    @Override
	    public void produce() {
	        LOCK.lock();
	        System.out.println("生产开始了");
	        for (int i = 0; i < MAX_POOL; i++) {
	            try {
	                Thread.sleep(1000);
	            } catch (InterruptedException e) {
	                e.printStackTrace();
	            }
	            System.out.println("生产了商品" + i);
	            POOL_LIST.add("商品" + i);
	            if(POOL_LIST.size() == ONCE_POOL) {
	                try {
	                    System.out.println("库存达到上限，当前库存=" + POOL_LIST.size());
	                    System.out.println("生产暂停了，通知消费者");
	                    FULL.signalAll();
	                    EMPTY.await();
	                } catch (InterruptedException e) {
	                    e.printStackTrace();
	                }
	            }
	        }
	        FULL.signalAll();
	        LOCK.unlock();
	        System.out.println("达到最大产量，生产结束了");
	
	    }
	
	}
	
ReentrantLock比起synchronized要好理解许多

**三，BlockingQueue阻塞队列方法**

BlockingQueue是JDK5.0的新增内容，它是一个已经在内部实现了同步的队列，实现方式采用的是我们第2种await() / signal()方法。它可以在生成对象时指定容量大小，用于阻塞操作的是put()和take()方法。

* put()方法：类似于我们上面的生产者线程，容量达到最大时，自动阻塞。 
* take()方法：类似于我们上面的消费者线程，容量为0时，自动阻塞。

依旧是实现Proxy类来做，代码如下

	public class ProxyByBlockQueue implements Proxy {
	
		/**
		 *  最大库存
		 */
		private static final int ONCE_POOL = 3;
		/**
		 * 计划生产的商品最大数量
		 */
		private static final int MAX_POOL = 9;
		
		private LinkedBlockingQueue<String> mQueue = new LinkedBlockingQueue<>(ONCE_POOL);
		
		@Override
		public void consume() {
		
		    System.out.println("开始消费了");
		    for (int i = 0; i < MAX_POOL; i++) {
		        try {
		            Thread.sleep(1000);
		        } catch (InterruptedException e) {
		            e.printStackTrace();
		        }
		        try {
		            System.out.println("消费了商品名字=" + mQueue.take());
		        } catch (InterruptedException e) {
		            e.printStackTrace();
		        }
		    }
		    System.out.println("全部消费完毕，消费结束了");
		
		}
		
		@Override
		public void produce() {
		    System.out.println("生产开始了");
		    for (int i = 0; i < MAX_POOL; i++) {
		        try {
		            Thread.sleep(1000);
		        } catch (InterruptedException e) {
		            e.printStackTrace();
		        }
		        System.out.println("生产了商品" + i);
		        try {
		            mQueue.put("商品" + i);
		        } catch (InterruptedException e) {
		            e.printStackTrace();
		        }
		    }
		    System.out.println("达到最大产量，生产结束了");
		
		
		
		    try {
		        mQueue.put("生产了商品");
		    } catch (InterruptedException e) {
		        e.printStackTrace();
		    }
		}
	}
	
**四，信号量**

Semaphore是一种基于计数的信号量。它可以设定一个阈值，基于此，多个线程竞争获取许可信号，做完自己的申请后归还，超过阈值后，线程申请许可信号将会被阻塞。Semaphore可以用来构建一些对象池，资源池之类的，比如数据库连接池，我们也可以创建计数为1的Semaphore，将其作为一种类似互斥锁的机制，这也叫二元信号量，表示两种互斥状态。计数为0的Semaphore是可以release的，然后就可以acquire（即一开始使线程阻塞从而完成其他执行。

代码如下：

	public class ProxyBySemaphore implements Proxy {
	
	    private Semaphore EMPTY = new Semaphore(0);
	    /**
	     *  最大库存
	     */
	    private static final int ONCE_POOL = 2;
	    private Semaphore FULL = new Semaphore(MAX_POOL);
	
	    /**
	     * 计划生产的商品最大数量
	     */
	    private static final int MAX_POOL = 8;
	
	    // 互斥锁
	    private final Semaphore mutex = new Semaphore(1);
	
	    /**
	     * 产品库存
	     */
	    private static final List<String> POOL_LIST = new LinkedList<>();
	
	
	    @Override
	    public void consume() {
	        System.out.println("开始消费了");
	        try {
	            EMPTY.acquire();
	            mutex.acquire();
	            for (int i = 0; i < ONCE_POOL ; i++) {
	                String s =POOL_LIST.remove(0);
	                System.out.println("消费了=" + s);
	                System.out.println("生产了商品 库存" + POOL_LIST.size());
	                //Thread.sleep(1000);
	            }
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        } finally {
	            EMPTY.release();
	            mutex.release();
	        }
	        System.out.println("全部消费完毕，消费结束了");
	    }
	
	    @Override
	    public void produce() {
	        System.out.println("生产开始了");
	        try {
	            FULL.acquire();
	            mutex.acquire();
	            for (int i = 0; i < ONCE_POOL; i++) {
	                POOL_LIST.add("商品" + i);
	                System.out.println("生产了商品 库存" + POOL_LIST.size());
	                Thread.sleep(1000);
	            }
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        } finally {
	            FULL.release();
	            mutex.release();
	        }
	        System.out.println("达到最大产量，生产结束了");
	    }
	}
 
**5，管道**

一种特殊的流，用于不同线程间直接传送数据，一个线程发送数据到输出管道，另一个线程从输入管道中读数据。

inputStream.connect(outputStream)或outputStream.connect(inputStream)作用是使两个Stream之间产生通信链接，这样才可以将数据进行输出与输入。

这种方式只适用于两个线程之间通信，不适合多个线程之间通信。

依旧是实现Proxy类来做， 使用字节流操作代码如下：

	public class ProxyByPiped implements Proxy{
	
	    private PipedInputStream mIs;
	    private PipedOutputStream mOs;
	
	    ProxyByPiped() {
	        try {
	            mIs = new PipedInputStream();
	            mOs = new PipedOutputStream();
	            mOs.connect(mIs);
	        } catch (IOException e) {
	            e.printStackTrace();
	        }
	    }
	
	    /**
	     * 最大库存
	     */
	    static final int ONCE_POOL = 2;
	
	    /**
	     * 计划生产的商品最大数量
	     */
	    static final int MAX_POOL = 8;
	
	
	    @Override
	    public void consume() {
	        int len = -1;
	        byte[] buffer = new byte[1024];
	        try {
	            while ((len = mIs.read(buffer)) != -1) {
	                System.out.println(new String(buffer, 0, len));
	            }
	            mIs.close();
	        } catch (IOException e) {
	            e.printStackTrace();
	        }
	    }
	
	    @Override
	    public void produce() {
	        try {
	            for (int i = 0; i < MAX_POOL; i++) {
	                mOs.write(("product " + i + "\n").getBytes());
	            }
	            mOs.close();
	        } catch (IOException e) {
	            e.printStackTrace();
	        }
	    }
	}

字符流操作代码如下：

	public class ProxyByPipedWriter implements Proxy{
	
	    private PipedReader mIs;
	    private PipedWriter mOs;
	
	    ProxyByPipedWriter() {
	        try {
	            mIs = new PipedReader();
	            mOs = new PipedWriter();
	            mOs.connect(mIs);
	        } catch (IOException e) {
	            e.printStackTrace();
	        }
	    }
	
	    /**
	     * 最大库存
	     */
	    static final int ONCE_POOL = 2;
	
	    /**
	     * 计划生产的商品最大数量
	     */
	    static final int MAX_POOL = 8;
	
	
	    @Override
	    public void consume() {
	        int len = -1;
	        char[] buffer = new char[1024];
	        try {
	            while ((len = mIs.read(buffer)) != -1) {
	                System.out.println(new String(buffer, 0, len));
	            }
	            mIs.close();
	        } catch (IOException e) {
	            e.printStackTrace();
	        }
	    }
	
	    @Override
	    public void produce() {
	        try {
	            for (int i = 0; i < MAX_POOL; i++) {
	                mOs.write(("product " + i + "\n"));
	            }
	            mOs.close();
	        } catch (IOException e) {
	            e.printStackTrace();
	        }
	    }
	}

以上就是Java线程通信的所有方式，如有错误欢迎指正

[ 参考文章](https://blog.csdn.net/ldx19980108/article/details/81707751)



 