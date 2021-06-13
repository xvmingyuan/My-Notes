>>>>>>>>List set map特点及线程安全与否
List特点：元素有放入顺序，元素可重复 
Map特点：元素按键值对存储，无放入顺序 
Set特点：元素无放入顺序，元素不可重复（注意：元素虽然无放入顺序，但是元素在set中的位置是有该元素的HashCode决定的，其位置其实是固定的） 
List接口有三个实现类：LinkedList，ArrayList，Vector 

LinkedList：底层基于链表实现，链表内存是散乱的，每一个元素存储本身内存地址的同时还存储下一个元素的地址。链表增删快，查找慢 

ArrayList和Vector的区别：ArrayList是非线程安全的，效率高；Vector是基于线程安全的，效率低 

Set接口有两个实现类：HashSet(底层由HashMap实现)，LinkedHashSet 

SortedSet接口有一个实现类：TreeSet（底层由平衡二叉树实现） 

Query接口有一个实现类：LinkList 

Map接口有三个实现类：HashMap，HashTable，LinkeHashMap 
  HashTable线程安全，低效，不支持null
	整体使用synchronized包裹的方法和Collections.synchronizedCollection()工具包来实现线程安全
	并发性能较低
  HashMap非线程安全，高效，支持null 
	注解:1.6版 HashMap结构为:数组+链表
	注解:1.8版 HashMap结构为:数组+(链表或红黑树)
 		default_initial_capacity=16 (1<<4默认数组长度[2^4])
		default_load_factor=0.75(数组的负载因子) 
		treeify_threshold=8(链表长度,binCount[红黑树扩容条件之一] >= 7时,走treeifyBin()[红黑树转换		方法],当数组长度>=64时,链表转为红黑树存储,否则走resize()方法,扩容数组长度) 
		min_treeify_capacity=64(最小数组容量[红黑树扩容条件之一]) 
		resize() 最小触发条件(size>=0.75*16=12,容量翻一倍,即1<<5[2^5] => 32)
	警告!!:HashMap多线程环境会形成链表循环,造成CPU利用率飙升接近100%

Collections.synchronizedMap(HashMap):性能低,HashMap读写全加锁.

Java并发包:基于CAS操作和volatile基础上建立，CAS是基础，那么AQS(AbstractQueuedSychronizer)就是整个Java并发包的核心
	AtomicInteger(CAS缺陷):aI.compareAndSet(100,110);
	CAS缺陷主要表现在三个方法：循环时间太长、只能保证一个共享变量原子操作、ABA问题.

	循环时间太长问题:自旋CAS长时间地不成功，则会给CPU带来非常大的开销.
	  解决方案:限制了CAS自旋的次数，例如BlockingQueue的SynchronousQueue

	一个共享变量原子问题:只能针对一个共享变量,多个共享变量就只能使用锁,
	  解决方案:办法把多个变量整成一个变量,例如读写锁中state的高低位

	ABA问题:CAS需要检查操作值有没有发生改变，如果没有发生改变则更新,
	  解决方案:添加版本号,例如自增版本号
	AtomicStampedReference(CAS善):aSR.compareAndSet(100,110,aSR.getStamp(),aSR.getStamp()+1)
  
  Java并发包中CopyOnWriteArrayList, ConcurrentHashMap
	CopyOnWriteArrayList(线程安全的动态数组):
	
	
	ConcurrentHashMap(线程安全的Map):
	线程安全(分段锁设计,技术:CAS,volatile,synchronized),默认支持16个线程,不允许key/value为空
	注解:1.6版 ConcurrentHashMap结构为:数组+链表,并发操作:使用Segment分段锁机制(设计臃肿,效率偏低)
	注解:1.8版 ConcurrentHashMap结构为:数组+(链表或红黑树),并发操作:使用CAS+synchronized(性能提升)
		concurrencyLevel:16(默认并发16个线程)
		default_initial_capacity=16 (1<<4默认数组长度[2^4])
		default_load_factor=0.75(数组的负载因子) 
		treeify_threshold=8(链表长度,binCount[红黑树扩容条件之一] >= 7时,走treeifyBin()[红黑树转换		方法],当数组长度>=64时,链表转为红黑树存储,否则走resize()方法,扩容数组长度) 
		min_treeify_capacity=64(最小数组容量[红黑树扩容条件之一]) 
		resize() 最小触发条件(size>=0.75*16=12,容量翻一倍,即1<<5[2^5] => 32)

  Java并发包中4种线程池:
	newCachedThreadPool(缓存版):接口(ExecutorService)     
	  创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程
	newFixedThreadPool(定长版):接口(ExecutorService)
	  创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待
	newScheduledThreadPool(定长,定时,周期版):接口(ScheduledExecutorService)
	  创建一个定长线程池，支持定时及周期性任务执行
	newSingleThreadExecutor(单线程版):接口(ExecutorService)
	  创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行
	ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, 
			TimeUnit unit, BlockingQueue workQueue, ThreadFactory threadFactory,
			RejectedExecutionHandler handler) 
	corePoolSize： 线程池维护线程的最少数量 
	maximumPoolSize：线程池维护线程的最大数量 
	keepAliveTime： 线程池维护线程所允许的空闲时间 
	unit： 线程池维护线程所允许的空闲时间的单位 
	workQueue： 线程池所使用的缓冲队列 
	threadFactory:线程工厂
	handler： 线程池对拒绝任务的处理策略
  Java并发包中的7个阻塞队列，当然他们都是线程安全的。
　　ArrayBlockingQueue：一个由数组结构组成的有界阻塞队列。其内部按先进先出的原则对元素进行排序,其中put方法和			take方法为添加和删除的阻塞方法，
		应用场景：FIFO(先入先出)顺序排序的

　　LinkedBlockingQueue：一个由链表结构组成的有界阻塞队列。但大小默认值为Integer.MAX_VALUE，所以我们在使用			时建议手动传值，为其提供我们所需的大小，避免队列过大造成机器负载或者内存爆满等情况
		应用场景：FIFO(先入先出)顺序排序的
   
   LinkedTransferQueue：一个由链表结构组成的无界阻塞队列。
		应用场景： 消费者先启动，生产者生产，如果有消费者等待，就直接将产物给消费者，如果找不到消费者，
			生产者会阻塞
   
   LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列。默认值为Integer.MAX_VALUE，所以我们在使用			时建议手动传值，且指定长度之后不允许进行修改
		应用场景：工作密取模式，其他线程从尾部偷取工作

　　PriorityBlockingQueue：一个支持优先级排序的无界阻塞队列。 
		应用场景：含优先级顺序的任务的队列

　　DealyQueue：一个使用优先级队列实现的无界阻塞队列，延迟队列。 
		应用场景：无界队列 (带时间，周期性队列）: 执行定时任务，延迟指定时间消费队列

   DelayedWorkQueue:无界队列,数组来储存队列中的元素，优先级队列，定时队列，周期队列

　　SynchronousQueue：一个不存储元素的阻塞队列。每次要进行offer操作时必须等待poll操作，否则不能继续添加元素
		应用场景：一对一同步队列

Java并发包中RejectedExecutionHandler提供了4种方式来处理任务拒绝策略。
   1、DiscardPolicy:直接丢弃不抛异常（DiscardPolicy）
   2、AbortPolicy:直接丢弃抛异常(AbortPolicy)
   3、DiscardOldestPolicy:将最早进入队列的任务删，之后再尝试加入队列(DiscardOldestPolicy)。
   4、CallerRunsPolicy:如果添加到线程池失败，那么主线程会自己去执行该任务(CallerRunsPolicy)。


Java并发包中发令枪:CountDownLatch()
	 CyclicBarrier和CountDownLatch的区别，两个看上去有点像的类，都在java.util.concurrent下，都可以用来表示代码运行到某个点上，二者的区别在于：
		（1）CyclicBarrier的某个线程运行到某个点上之后，该线程即停止运行，直到所有的线程都到达了这个点，所有线程才重新运行；xccqv则不是，某线程运行到某个点上之后，只是给某个数值-1而已，该线程继续运行
		
		（2）CyclicBarrier只能唤起一个任务，CountDownLatch可以唤起多个任务
		
		（3）CyclicBarrier可重用，CountDownLatch不可重用，计数值为0该CountDownLatch就不可再用了
        
  Java并发包中包:atomic(原子)
  Java并发包中包:locks(锁)
　　
SortedMap有一个实现类：TreeMap 
 其实最主要的是，list是用来处理序列的，而set是用来处理集的。Map是知道的，存储的是键值对 
 set 一般无序不重复.map kv 结构 list 有序
HashSet，存储object的集合，既然是集合，就不允许有重复元素。判断两个元素是否相同，是由hashCode与equals方法共同完成的。

>>>>>>>>JAVA中线程安全的map有
Hashtable、synchronizedMap(HashMap)、ConcurrentHashMap。

>>>>>>>>JAVA中线程安全的list有
CopyOnWriteArrayList

synchronized锁升级策略(可升不可降):偏向锁—>轻量级锁—->重量级锁
	偏向锁(优化了只有一个线程进入同步代码块的情况):偏向锁提高的是那些带同步但无竞争的代码的性能，也就是说如果你的同步代码块很长时间都是同一个线程访问，偏向锁就会提高效率，因为他减少了重复获取锁和释放锁产生的性能消耗。如果你的同步代码块会频繁的在多个线程之间访问，可以使用参数-XX：-UseBiasedLocking来禁止偏向锁产生，避免在多个锁状态之间切换。
	轻量级锁(优化了多个线程进入同步代码块的情况):轻量级锁的思想是当多个线程进入同步代码块后，多个线程未发生竞争时一直保持轻量级锁，通过CAS来获取锁,减少锁状态切换。如果发生竞争,不是直接阻塞线程,首先会采用CAS自旋操作来获取锁,减少了阻塞线程的概率,自旋在极短时间内发生，有固定的自旋次数，一旦自旋获取失败，则升级为重量级锁。
	重量级锁(悲观锁):一个一个排队访问...


synchronized关键字实现的同步机制由如下几点补充说明：
  1.如果同一个方法有多个线程访问，那么每个线程都有自己的线程拷贝（拷贝存储在工作内存中）
  2.类的实例都有自己的对象锁，如果一个线程成功获取到该实例的对象锁那么当其他线程需要获取该实例的对象锁时，便需要阻塞等待，直到该实例的对象锁被成功释放。对象锁可以作用在同步方法或者同步代码块中
  3.如果不同的线程访问的是不同实例的对象锁，那么不会互相阻塞，因为不同实例的对象锁是不同的
  4.获得实例的对象锁的线程会让其他想要获取相同对象锁的线程阻塞在synchronized代码外，比如由两个synchronized方法a()、b()，那么如果线程A成功进入了同步方法a()，那么该线程便获取了该实例（比如实例obj）的对象锁，而如果其他线程想要执行另一个同步方法b()，就会阻塞在外面，因为a()和b()持有的都是对象obj的对象锁
  5.持有一个实例的对象锁不会阻止该线程被置换出来，也不会阻塞其他线程执行非synchronized方法，因为非synchronized方法执行的时候不需要获取实例的对象锁
  6.使用同步代码块的时候，括号的对象可以为任意Object实例，当为this时，指的是当前对象的对象锁
  7.类锁主要用于控制对static成员变量的并发访问
  8.synchronized块（可以是同步方法或者同步代码块）是可重入的，每次重入会把锁的计数器加1，每次退出将计数器减1，当计数器的值为0的时候，锁便被释放了
  9.Java SE 1.6 为了减少获得锁和释放锁的性能消耗引入了偏向锁和轻量级锁，所以使用synchronized也没有那么重量级了


sleep和wait有什么区别？
  对时间的指定。 
  1，sleep方法必须指定时间。 http://ifeve.com/tag/netty/
  2，wait方法有重载形式，可以指定时间，也可以不指定时间。
  对于执行权和锁的操作.
  1，sleep():释放执行权，不释放锁，因为肯定能醒，肯定可以恢复到临时阻塞状态。 
  2，wait()：释放执行权，释放锁，因为sleep不释放锁，如果没有时间指定，那么其他线程都进行不了同步中，无法将其唤醒。

IO输入输出流
  节点流类型常见:
	文件操作字节流:FileInputStream/FileOutputStream
	文件操作字符流:FileReader/FileWriter 
	
  处理流类型常见:
	1.缓冲流：缓冲流要“套接”在相应的节点流之上，对读写的数据提供了缓冲的功能，提高了读写效率，同事增加了一些新的方法。
		字节缓冲流有BufferedInputStream/BufferedOutputStream，字符缓冲流有BufferedReader/
		BufferedWriter，字符缓冲流分别提供了读取和写入一行的方法ReadLine和NewLine方法。对于输出地缓冲流，写
		出的数据，会先写入到内存中，再使用flush方法将内存中的数据刷到硬盘。所以，在使用字符缓冲流的时候，一定要先
		flush，然后再close，避免数据丢失。
	
	2.转换流：用于字节数据到字符数据之间的转换。 
		仅有字符流InputStreamReader/OutputStreamWriter。其中，InputStreamReader需要与
		InputStream“套接”，OutputStreamWriter需要与OutputStream“套接”。
	
	3.数据流：提供了读写Java中的基本数据类型的功能。 
		DataInputStream和DataOutputStream分别继承自InputStream和OutputStream，需要“套接”在
		InputStream和OutputStream类型的节点流之上。
	
	4.对象流：用于直接将对象写入写出。 
		流类有ObjectInputStream和ObjectOutputStream，本身这两个方法没什么，但是其要写出的对象有要求，该对		象必须实现Serializable接口，来声明其是可以序列化的。否则，不能用对象流读写。

位运算(<< >> >>>) 小记:二进制中length为2^n(模/位等价运算)下:x%length = x&(length-1)

invokestatic（静态重载）方法重载 invokespecial（特殊执行）invokevirtual（动态[事实]重写) invokeinterface(接口加载）invokedynamic（动态加载）
invokestatic:调用静态方法
invokespecial:调用实例构造器<init>方法、私有方法和父类方法（super(),super.method()）
invokevirtual:调用所有的虚方法(静态方法、私有方法、实例构造器、父类方法、final方法都是非虚方法)
invokeinterface:调用接口方法，会在运行时期再确定一个实现此接口的对象

Java基础 Java虚拟机 Java高并发编程  netty 设计模式使用  算法与数据结构 源码 高并发分布式


------------------------Java随笔----Start----------------------------

集合排序:
升序(ASC):list.sort(Comparator.comparing(FantaiCompManager::getSrank));
降序(DESC)list.sort(Comparator.comparing(FantaiCompManager::getSrank).reversed());
排除空值排序:infos.sort(Comparator.comparing(IPOStock::getApplyBuyDate, Comparator.nullsLast(Date::compareTo)).reversed());



统计(总和,最大值,最小是,均值)
	System.out.println("用户：" + list);
        double sum = list.stream().mapToDouble(User::getHeight).sum();
        System.out.println("身高 总和：" + df.format(sum));
        double max = list.stream().mapToDouble(User::getHeight).max().getAsDouble();
        System.out.println("身高 最大：" + df.format(max));
        double min = list.stream().mapToDouble(User::getHeight).min().getAsDouble();
        System.out.println("身高 最小：" + df.format(min));
        double average = list.stream().mapToDouble(User::getHeight).average().getAsDouble();
        System.out.println("身高 平均：" + df.format(average));

BigDecimal 加减乘除
 加:add() 减:subtract() 乘:multiply() 除:divide() 绝对值:abs()

BigDecimal sum = shareHolder.stream().map(FantaiShareHolder::getHolderrto).reduce(BigDecimal.ZERO, BigDecimal::add);

idea 正则替换技巧 (.*)offSiteFundAssetManager(.*)
