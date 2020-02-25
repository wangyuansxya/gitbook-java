**java 单例模式**

使用场景

 * 1.需要频繁实例化然后销毁的对象。
 * 2.创建对象时耗时过多或者耗资源过多，但又经常用到的对象。
 * 3.有状态的工具类对象。
 * 4.频繁访问数据库或文件的对象。

1. 恶汉式

优点：简单易实现
缺点：容易造成系统资源浪费。例如在实现数据库连接池，开始就创建暂时不使用的对象

	private static MySingleController msc = new MySingleController();

    public static MySingleController getIntance() {
        return msc;
    }

2. 懒汉式：

	private static MySingleController msc = null;

	    public static MySingleController getIntance() {
	        if(msc == null)msc = new MySingleController();
	        return msc;
	    }
	}


3. 最优创建方式

上述的俩种方法在单线程的模式下是没有问题的。

方法1：如果是多线程的模式下的话需要使用锁synchronized来避免多线程问题

	private static MySingleController msc = null;
	    public static synchronized MySingleController getIntance() {
        	if(msc == null)msc = new MySingleController();
        	return msc;
		}
    }

方法2：上述方法虽然可以避免多线的问题，但是由于对整个方法使用了synchronized，因此会造成性能问题

	public static MySingleController getIntance() {
        if(msc == null){
            synchronized (MySingleController.class) {
                //这样做也是为了避免多线程环境下new出多个单例
                if(msc == null)msc = new MySingleController();
            }
        }
        return msc;
    }

4. 使用枚举：

在effective java中介绍的一种，使用枚举实现单例。在Java中枚举天然实现了单例

	public enum MySingleControllerEnum {
	    INTANCE;
	    public static MySingleControllerEnum getIntance() {
	        return INTANCE;
	    }
	}