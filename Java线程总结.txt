/**
 * synchronized 关键字
 * 对某个对象加锁（对象锁记录在堆内存中）
 * 2个线程同时申请o对象锁，当一个线程拿到o对象的锁，其他线程拿不到o对象锁
 * @author xmy
 * @time：2019年1月24日 下午4:37:17
 */

/**
 * synchronized 关键字 对某个对象加锁（对象锁记录在堆内存中） 
 * 锁定的是什么？ 对象（无论自身（this），还是其他对象）
 * 
 * @author xmy
 * @time：2019年1月24日 下午4:37:17
 */

/**
 * synchronized 关键字 对某个对象加锁（对象锁记录在堆内存中） 锁定的是什么？ 对象（无论自身（this），还是其他对象）
 * 由于静态属性和方法没有实例对象，所以锁定的是当前方法的class对象
 * @author xmy
 * @time：2019年1月24日 下午4:37:17
 */

/**
 * 一个同步方法可以调用另外一个同步方法，一个线程已经拥有某个对象的锁，再次申请的时候仍然会得到该对象锁
 * 也就是说 synchronized 获得的锁是可重入的
 * @author xmy
 * @time：2019年1月24日 下午5:15:06
 */

/**
 * 程序在执行过程中，如果出现异常，默认情况锁会被释放
 * 所以，在并发处理的过程中，有异常要多加小心，不然可能会发生不一致的情况
 * 比如，在一个web app 处理过程中，多个servlet线程共同访问同一个资源，这是如果异常处理不合适
 * 在第一个线程抛出异常，其他线程会进入同步代码区，有可能会访问到异常产生时的数据
 * 因此要非常小心的处理同步业务逻辑的异常
 * @author xmy
 * @time：2019年1月24日 下午5:46:45
 */

/**
 * volatile 关键字， 使用一个变量在多线程间可见
 * A B线程都用到一个变量，java默认是A线城中保留一份copy，这样如果B线程修改了该变量，则A线城未必知道。
 * 使用volatile 关键字 会让所有线程都会读到变量的修改值
 * 
 * 在下面的代码中，running是存在于堆内存的t对象中
 * 当线程t1开始运行的时候，会吧runing值从内存中读到t1线程的工作区，在运行过程中世界使用这个copy，并不会每次都去
 * 读取堆内存，这样，当主线程修改running的值之后，t1线程感知不到，所以不会停止运行
 * 
 * 使用volatile， 将会强制多有线程都去堆内存中读取running的值
 * 
 * volatile并不能保存多个线程共同修改running变量是所带来的不一致问题（一致写不能保证），volatile不能代替synchronized
 * @author xmy
 * @time：2019年1月27日 下午2:53:43
 */

/**
 * 解决同样的问题的更高效的方法，使用AtomicXXX类
 * AtomicXXX类本身方法都是原子性的，但是不能保证多个方法连续调用是原子性的
 * @author xmy
 * @time：2019年1月27日 下午4:14:46
 */

/**
 * 警示：不要以字符串常量作为锁对象
 * 在下面的例子中，m1和m2其实锁定的是同一个对象
 * 这种情况还会发生比较诡异的现象，比如你用到的了一个类库，在该类库中代码锁定了字符串“Hello”
 * 但是你读不到源码，所以你在自己的代码中也锁定了“Hello”，这时候就有可能发生非常诡异的死锁阻塞
 * @author xmy
 * @time：2019年1月27日 下午4:32:42
 */

总结：
 volatile 使线程间可见

 wait 线程等待，让出监视器CPU资源
 notify 唤醒一个等待线程（无法指定）
 notifyAll 唤醒所有等待的线程

 CountDownLatch(num)(num = 线程等待数）-—>发令枪
 	await()：需要的线程等待
 	countDown() ：当CountDownLatch 减到0是 await() 线程被启动继续执行

 LockSupport.park()和unpark()的灵活之处:解决线程之间时序问题，park/unpark模型真正解耦了线程之间的同步，线程之间不再需要一个Object或者其它变量来存储状态，不再需要关心对方的状态。
	注意事项：许可是一次性的，unpark(Thread)<n次> —-> park()<一次许可> —-> park()<许可执行等待>
	
总结：
 使用reentrantlock 可以完成同样的功能 需要注意的是，必须必须必须要手动释放锁（重要的事说三遍） 使用sync锁定的话，
 如果遇到异常，jvm会自动释放锁，但是lock必须手动释放锁，因此经常在finally中进行锁的释放。
 使用reentrantlock 可以进行“尝试锁定” trylock，这样无法锁定，或者在指定时间内无法锁定，线程可以决定是否继续等待。
 使用ReetrantLock还可以调用lockInterruptibly方法，可以对线程interrupt方法做出响应，在一个线程等待锁的过程中，可以被打断
 ReentrantLock(true) 指定为公平锁 

总结：
 题目：写一个固定容量同步容器，拥有put和get方法，以及getCount方法
 MyContainer1：使用synchronized关键字，实现同步容器
/**
 * 写一个固定容量同步容器，拥有put和get方法，以及getCount方法，
 * 能够支持2个生产者线程和10个消费者线程的阻塞调用
 * 
 * 使用 wait 和 nofityAll实现
 * @author xmy
 * @time：2019年1月28日 下午3:24:26
 * @param <T>
 */

import java.util.LinkedList;
import java.util.concurrent.TimeUnit;

public class MyContainer1<T> {
	final private LinkedList<T> list = new LinkedList<>();
	final private int MAX = 10;
	private int count = 0;

	public synchronized void put(T t) {
		while (list.size() == MAX) {
			try {
				this.wait(); //effective java: wait 往往和 while一起使用
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
		list.add(t);
		++count;
		this.notifyAll(); // 通知消费者进行消费
	}

	public synchronized T get() {
		T t = null;
		while (list.size() == 0) {
			try {
				this.wait();
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
		t = list.removeFirst();
		count--;
		this.notifyAll(); // 通知生产者 进行生产
		return t; 
	}
	public static void main(String[] args) {
		MyContainer1<String> c = new MyContainer1<>();
		// 启动消费者线程
		for (int i = 0; i < 10; i++) {
			new  Thread(()->{
				for (int j = 0; j < 5; j++) {
					System.out.println(c.get());
				}
			},"c"+i).start();
		}
		try {
			TimeUnit.SECONDS.sleep(2);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		//启动生产者线程
		for (int i = 0; i < 2; i++) {
			new Thread(()->{
				for (int j = 0; j < 25; j++) {
					c.put(Thread.currentThread().getName()+" "+j);
					
				}
			},"p"+i).start();
		}
	}
	

}

 MyContainer2：使用ReentrantLock()方法，实现同步容器 
/**
 * 写一个固定容量同步容器，拥有put和get方法，以及getCount方法，
 * 能够支持2个生产者线程和10个消费者线程的阻塞调用
 * 
 * 使用 lock和Condition 来实现
 *
 * 操作： 创建producer Condition 用来控制生产者线程，创建consumer Condition 用来控制消费者线程，
 *  当list到达MAX时，调用生产者await()方法，停止生产，并使用signalAll通知消费者消费。
 *  当list到达0时，调用消费者await()方法，停止消费，并使用signalAll通知生产者生产。
 * 
 * @author xmy
 * @time：2019年1月28日 下午3:24:26
 * @param <T>
 */

import java.util.LinkedList;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class MyContainer2<T> {

	final private LinkedList<T> list = new LinkedList<>();
	final private int MAX = 10;
	private int count = 0;

	private Lock lock = new ReentrantLock();
	private Condition producer = lock.newCondition();
	private Condition consumer = lock.newCondition();

	public void put(T t) {
		try {
			lock.lock();
			while (list.size() == MAX) {
				producer.await();// 生产者等待
			}
			list.add(t);
			++count;
			consumer.signalAll();// 通知消费者进行消费
		} catch (InterruptedException e) {
			e.printStackTrace();
		} finally {
			lock.unlock();
		}
	}

	public T get() {
		T t = null;
		try {
			lock.lock();
			while (list.size() == 0) {
				consumer.await();//消费者等待
			}
			t = list.removeFirst();
			count--;
			producer.signalAll();// 通知生产者 进行生产
		} catch (InterruptedException e) {
			e.printStackTrace();
		} finally {
			lock.unlock();
		}
		return t;
	}

	public static void main(String[] args) {
		MyContainer2<String> c = new MyContainer2<>();
		// 启动消费者线程
		for (int i = 0; i < 10; i++) {
			new Thread(() -> {
				for (int j = 0; j < 5; j++) {
					System.out.println(c.get());
				}
			}, "cc" + i).start();
		}
		try {
			TimeUnit.SECONDS.sleep(3);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		// 启动生产者线程
		for (int i = 0; i < 2; i++) {
			new Thread(() -> {
				for (int j = 0; j < 25; j++) {
					c.put(Thread.currentThread().getName() + " " + j);

				}
			}, "pp" + i).start();
		}
	}

}

总结：
 * ThreadLocal线程局部变量
 * 
 * ThreadLocal 是 使用空间换时间，synchronized是使用时间换空间
 * 比如在hibernate中session就纯在与ThreadLocal中，避免synchronized的使用
 * 
 * 每个线程都复制一个份 各自维护自己的那一份数据 互不影响
 * 每个线程可以有自己的Session，这样多线程时Session 不会冲突

总结：
使用内部类方式，实现懒加载版单例模式
/**
 * 单例模式 （使用内部类实现）
 * 
 * 不需要加锁 也可以实现懒加载
 * @author xmy
 * @time：2019年1月29日 上午11:23:32
 */
public class Singleton {
	private Singleton() {
		System.out.println("single");
	};

	private static class Inner {
		private static Singleton s = new Singleton();
	}

	public static Singleton newSingle() {
		return Inner.s;
	}
	
	public static void main(String[] args) {
		Singleton single = newSingle();
	}
}

/**
 * 单例模式 （使用双重检测）
 * @author xmy
 * @time：2019年1月29日 上午11:23:32
 */
public class Singleton2check {
	private volatile static Singleton2check s;
	private Singleton2check() {
		System.out.println("Singleton2check");
	};


	public static Singleton2check newSingle() {
		if(s==null) {
			synchronized (Singleton2check.class) {
				if(s==null) {
					s = new Singleton2check();
				}
			}
		}
		return s;
	}
	
	public static void main(String[] args) {
		Singleton2check single = newSingle();
	}
}

总结：
ArrayList Vector synchronized ConcurrentLinkedQueue 实现售票机制对比
TicketSeller1.java 不是线程安全的 不能再线程中使用
TicketSeller2.java ：while判断和remove组合之间是非原子性的，会引起不同步移除问题
TicketSeller3.java ：使用synchronized 效率不高
TicketSeller4.java : ConcurrentLinkedQueue 使用并发队列提高效率，简化代码


总结：
map/set :key value 和 key
不加锁
hashmap 无顺序 线程不安全
treemap(红黑树) 有顺序 线程不安全
linkedhashmap

低并发
Hashtable(使用较少)
Collections.synchronizedXXX

并发高：
concurrenthashmap 无顺序 线程安全
concurrentskiplistmap 有顺序 线程安全


List 接口
ArrayList 线程不安全
LinkedList 线程不安全
Collections.synchronizedXXX 低并发 同步手段
ConcurrentLinkedQueue 无界队列
CopyOnWriteList 写非常少，读特别多
Queue
	ConcurrentLinkedQueue 无界队列，高并发队列
	BlockingQueue 阻塞队列
		LinkedBQ 无界
		ArrayBQ  有界
		TransferQueue 消费者线程先行，否则阻塞，0容量队列
		SynchronusQueue 0容量队列
	DelayQueue 执行定时任务队列
	
总结：
Executor
ExecutorService
Callable = Runnable (Callable 有返回值)
Executors 线程池工厂工具
ThreadPool 线程池
Future 带返回值线程池

6种线程池：
	fixed ：固定线程池 （LinkedBlockingQueue:无界存放阻塞任务队列，大小于分配内存有关）
	cached ：缓存线程池 （SynchronousQueue:容量为0的队列，每次要进行offer操作时必须等待poll操作，否则不能继续添加元素）
	single ：串行线程池 （LinkedBlockingQueue）
	scheduled ：定周期线程池 （DelayedWorkQueue:无界队列,数组来储存队列中的元素，优先级队列，定时队列，周期队列）
	workstealing ：工作偷取线程池
	forkjoin ：归并线程池
	
底层基础：ThreadPoolExecutor(1,2,3,4,5,6) ->fixed,cached,single,scheduled
ParallerStreamAPI： 并发流式编程
