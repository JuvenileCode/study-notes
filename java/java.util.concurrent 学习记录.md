### java.util.concurrent 学习记录

> 在工作代码中出现`concurrent`并发包下的一些代码，早就想自己学习了解下`concurrent`一些重要工具类的使用，在此首先感谢`Doug Lea`大神提供这些工具类。其实早就想了解并发包，但是由于自己感觉很难托了很久，然而使用下来并不难，以此警醒自己！

#### Executor线程池相关

> 在实际开发用到`spring`的情况下，基本上都会用`spring`封装的[ThreadPoolTaskExecutor](https://blog.csdn.net/foreverling/article/details/78073105)来进行异步任务处理，当然，spring的多线程也离不开 concurrent 里的基本工具类。

##### Executor

`Executor` Java里面线程池的顶级接口，但是严格意义上讲Executor并不是一个线程池，而只是一个执行线程的工具 ，只提供了一个方法`void execute(Runnable command);`

##### ThreadPoolExecutor

**`java.uitl.concurrent.ThreadPoolExecutor`类是线程池中最核心的一个类，是java多线程池的基础**

- ThreadPoolExecutor 是 ExecutorService 接口的一个实现。

- ThreadPoolExecutor 使用其内部池中的线程执行给定任务（Callable 或者 Runnable）

**`ThreadPoolExecutor`提供了四个构造器，其实前三个构造器都是调用第四个构造器来初始化，直接来观察第四个构造器：**

```java
ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, ThreadFactory threadFactory, RejectedExecutionHandler handler) 	
```

- corePoolSize：核心池的大小，这个参数跟后面讲述的线程池的实现原理有非常大的关系。在创建了线程池后，默认情况下，线程池中并没有 任何线程，而是等待有任务到来才创建线程去执行任务，除非调用了prestartAllCoreThreads()或者 prestartCoreThread()方法，从这2个方法的名字就可以看出，是预创建线程的意思，即在没有任务到来之前就创建 corePoolSize个线程或者一个线程。默认情况下，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线 程池中的线程数目达到corePoolSize后，就会把到达的任务放到缓存队列当中；

- maximumPoolSize：线程池最大线程数，这个参数也是一个非常重要的参数，它表示在线程池中最多能创建多少个线程；

- keepAliveTime：表示线程没有任务执行时最多保持多久时间会终止。默认情况下，只有当线程池中的线程数大于corePoolSize 时，keepAliveTime才会起作用，直到线程池中的线程数不大于corePoolSize。即当线程池中的线程数大于corePoolSize 时，如果一个线程空闲的时间达到keepAliveTime，则会终止，直到线程池中的线程数不超过corePoolSize。但是如果调用了 allowCoreThreadTimeOut(boolean)方法，在线程池中的线程数不大于corePoolSize 时，keepAliveTime参数也会起作用，直到线程池中的线程数为0；

- unit：参数keepAliveTime的时间单位，有7种取值，在TimeUnit类中有7种静态属性

- ```java
  TimeUnit.DAYS;               //天
  TimeUnit.HOURS;             //小时
  TimeUnit.MINUTES;           //分钟
  TimeUnit.SECONDS;           //秒
  TimeUnit.MILLISECONDS;      //毫秒
  TimeUnit.MICROSECONDS;      //微妙
  TimeUnit.NANOSECONDS;       //纳秒
  ```

- workQueue：一个阻塞队列，用来存储等待执行的任务，这个参数的选择也很重要，会对线程池的运行过程产生重大影响，一般来说，这里的阻塞队列有以下几种选择：

  ```java
  ArrayBlockingQueue; //有界队列
  LinkedBlockingQueue; //无界队列
  SynchronousQueue; //特殊的一个队列，只有存在等待取出的线程时才能加入队列，可以说容量为0，是无界队列
  ```

   ArrayBlockingQueue和PriorityBlockingQueue使用较少，一般使用LinkedBlockingQueue和Synchronous。线程池的排队策略与BlockingQueue有关。

- threadFactory：线程工厂，主要用来创建线程；

- handler：表示当拒绝处理任务时的策略，有以下四种取值：

  ```
  *ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。 
  ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。 
  ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
  ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务 
  ```

  **ThreadPoolExecutor是`Executors`类的底层实现。**

  在JDK帮助文档中，有如此一段话：

  >  强烈建议程序员使用较为方便的 `Executors` 工厂方法 `Executors.newCachedThreadPool()`（无界线程池，可以进行自动线程回收）、`Executors.newFixedThreadPool(int)`（固定大小线程池）`Executors.newSingleThreadExecutor()`（单个后台线程）

##### Executors

**Executors类，提供了一系列工厂方法用于创先线程池，返回的线程池都实现了ExecutorService接口**

* `newSingleThreadExecutor`创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行。

* `newFixedThreadPool` 创建固定大小的线程池。每次提交一个任务就创建一个线程，直到线程达到线程池的最大大小。线程池的大小一旦达到最大值就会保持不变，在提交新任务，任务将会进入等待队列中等待。如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。

* `newCachedThreadPool`创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60秒处于等待任务到来）的线程，当任务数增加时，此线程池又可以智能的添加新线程来处理任务。此线程池的最大值是Integer的最大值(2^31-1) ，`普遍用这个比较多`。

* `newScheduledThreadPool`创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求

##### ExecutorService

真正的线程池接口，`ExecutorService` 接口表示一个异步执行机制，使我们能够在后台执行任务。

> 使用`ExecutorService threadPool = Executors.newCachedThreadPool();` Executors可以快速创建ExecutorService。

```java
//通过线程池管理多线程
ExecutorService threadPool = Executors.newCachedThreadPool();
//线程池提交一个异步任务
System.out.println("====提交异步任务");
Future<HashMap<String, String>> future = threadPool.submit(new Callable<HashMap<String, String>>() {
    @Override
    public HashMap<String, String> call() throws Exception {
        System.out.println("异步任务开始执行....");
        Thread.sleep(2000);
        System.out.println("异步任务执行完毕，返回执行结果!!!!");
        return new HashMap<String, String>() {
            {
                this.put("futureKey", "成功获取future异步任务结果");
            }
        };
    }
});
```

##### Callable、Runnable

- `java.lang.Runnable`它是一个接口，在它里面只声明了一个run()方法，由于run()方法返回值为void类型，所以在执行完任务之后无法返回任何结果。
- `java.util.concurrent`它也是一个接口，在它里面也只声明了一个方法，只不过这个方法叫做call()，这是一个泛型接口，call()函数返回的类型就是传递进来的V类型。
  - 那么怎么使用Callable呢？一般情况下是配合`ExecutorService`来使用的。


##### Future

`java.util.concurrent` 就是对于具体的Runnable或者Callable任务的执行结果进行取消、查询是否完成、获取结果。必要时可以通过get方法获取执行结果，该方法会阻塞直到任务返回结果。

在Future接口中声明了5个方法，下面依次解释每个方法的作用：

- cancel方法用来取消任务，如果取消任务成功则返回true，如果取消任务失败则返回false。参数mayInterruptIfRunning表示是否允许取消正在执行却没有执行完毕的任务，如果设置true，则表示可以取消正在执行过程中的任务。如果任务已经完成，则无论mayInterruptIfRunning为true还是false，此方法肯定返回false，即如果取消已经完成的任务会返回false；如果任务正在执行，若mayInterruptIfRunning设置为true，则返回true，若mayInterruptIfRunning设置为false，则返回false；如果任务还没有执行，则无论mayInterruptIfRunning为true还是false，肯定返回true。
- isCancelled方法表示任务是否被取消成功，如果在任务正常完成前被取消成功，则返回 true。
- isDone方法表示任务是否已经完成，若任务完成，则返回true；
- get()方法用来获取执行结果，这个方法会产生阻塞，会一直等到任务执行完毕才返回；
- get(long timeout, TimeUnit unit)用来获取执行结果，如果在指定时间内，还没获取到结果，就直接返回null。

　　也就是说Future提供了三种功能：

　　1）判断任务是否完成；

　　2）能够中断任务；

　　3）能够获取任务执行结果。

**因为Future只是一个接口，所以是无法直接用来创建对象使用的，因此就有了下面的FutureTask**

##### FutureTask

`java.util.concurrent.FutureTask` 所以它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值

