<!--
 * @Author: your name
 * @Date: 2020-06-21 20:00:43
 * @LastEditTime: 2020-06-29 19:13:40
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \GitHubnotebook\面试题笔记\JavaJUC.md
--> 

# Java多线程

## 1.线程基础

- 程序：程序是指令和数据的有序集合，其本身没有任何运行的含义，是一个静态的概念
- 进程(Process)：是执行程序的一次执行过程，是一个动态的概念，是系统资源分配的单位
- 通常一个进程中可以包含若干个线程(Thread)（至少一个），线程是CPU调度和执行的单位。
- 注意：很多多线程是模拟出来的，真正的多线程是指多个CPU即多核。如果是模拟出来的多线程，即只有一个CPU的情况下，在同一个时间点CPU只能执行一个代码，因为切换的很快，所有就有同时执行的错觉。
- 核心概念：
  - 线程是独立的执行路径
  - 在程序运行时，即使自己没有创建线程，后台也会有多个线程，如主线程，gc线程；
  - main()称之为主线程，为系统的入口，用于执行整个程序；
  - 在同一个进程中，如果开辟了多个线程，线程的顺序由调度器安排调度，调度器与操作系统紧密相关，先后顺序是不能人为干预的
  - 对同一份资源访问时，会存在资源抢夺问题，需要加入并发控制
  - 线程会带来额外的开销，如cpu调度时间，并发控制开销
  - 每个线程在自己的工作内存交互，内存控制不当会造成数据的不一致。

## 2.线程创建

普通方法(run)调用,只有主线程一条执行路径
多线程方法(start)调用，多条执行路径，主线程和子线程并行交替。

### 2.1 Thread(class)

将一个类声明为Thread的子类，这个子类应该重写Thread类的方法run()

```java
public class TestThread1 extends Thread{
   @Override
   public void run(){
      //run方法线程体
      for(int i=0;i<2000;i++){
         System.out.print("我在看代码"+i);
      }
   }
   public static void main(String[] args){
      //main线程，主线程
      //创建一个线程对象
      TestThread1 testThread1 = new TestThread1();
      //调用start()方法并且开启线程
      testThread1.start();

      for(int i=0;i<2000;i++){
         System.out.print("我在学习多线程"+i);
      }
   }
}
```

- 总结：
  - 线程开启并不一定立即执行。
  - 不建议使用：避免oop单继承局限性


### 2.2 Runnable(interface)

声明实现类Runnable接口，然后实现run()方法。创建该方法的实例作为参数传递给Thread

```java
//实现Runnable接口，重写run()方法执行线程需要丢入runnable接口实现类，调用start方法
public class TestThread2() implements Runnable {
   @Override
   public void run(){
      //run方法线程体
      for(int i=0;i<2000;i++){
         System.out.print("我在看代码"+i);
      }
   }

   public static void main(String[] args){
      //main线程，主线程
      //创建一个线程对象
      TestThread1 testThread2 = new TestThread2();
      //常见线程对象传入Runnable接口实现类，调用start()方法并且开启线程
      new Thread(testThread2).start();
      
      for(int i=0;i<2000;i++){
         System.out.print("我在学习多线程"+i);
      }
   }
}
```

- 总结:
  - 避免单继承局限性，灵活方便，方便同一个对象被多个线程同时使用。

### 2.3 Callable(interface)

1. 实现Callable接口，需要返回值类型。
2. 重写call方法,需要抛出异常。
3. 常见目标对象
4. 创建执行服务：ExecutorService ser = Executors.newFixedThreadPool(1);
5. 提交执行：Future<Boolean> result1 = ser.submit(t1);
6. 获取结果:boolean r1 = result1.get();
7. 关闭服务:ser.shutdownNow();

  
### 2.4 Lambda表达式

- 函数式接口：
  - 任何借口就，如果只包含**唯一一个抽象方法**，那么他就是一个函数式接口
  - 对于函数式接口，我们可以通过Lamdba表达式来创建该接口对象。
- Lambda
  - ()->{...}

### 2.5 线程状态

- new
- runnable
- blocked
- waiting
- timed_waiting
- terminated 

|方法|说明|
|--|--|
|setPriority|更该线程的优先级|
|static void sleep(long millis)|在指定的毫秒数内让当前正在执行的线程休眠|
|void join()|等待该线程终止|
|static void yield()|暂停当前正在执行的线程对象，并执行其他线程|
|void interrupt()|中断线程，别用这种方式|
|boolean isAlive()|测试线程是否处于活动状态|

1. 停止线程
   
  - 不推荐使用JDK提供的stop()，destory()方法（已废弃）。
  - 推荐线程自己停下来
  - 建议使用一个标志位进行终止变量，当flag=false，则终止线程运行。

```java
public class TestStop implements Runnable{
   //1.定义线程体使用的标识
   private boolean flag = true;

   @Override
   public void run(){
      //2. 线程体使用该标识
      while(flag){
         System.out.println("run... Thread");
      }
   }
   //3. 对外提供方法改变标识
   public void stop(){
      this.flag = false;
   }
} 
```

2. 线程休眠

- sleep(时间)指定当前线程阻塞的毫秒数
- sleep存在异常InterruptedException;
- sleep时间到达后线程进入就绪状态
- sleep可以模拟网络延迟，倒计时等
- 每个对象都有一个锁 sleep不会释放锁。

3. 线程礼让

- 让当前正在执行的线程暂停，但不阻塞
- 将线程从运行状态转为就绪状态
- 让cpu重新调度，礼让不一定成功！

4. join()

- Join合并线程，待此线程执行完成后，再执行其他线程，其他线程阻塞
- 可以想象成插队 

5. 线程优先级

- Java提供一个线程调度器来监控程序中启动后进入就绪状态的所有线程，线程调度器按照优先级决定应该调度那个线程来执行
- 线程优先级用数字(权重)表示，范围从1-10。
- 优先级底只是意味着或者调度的概率低，并不是优先级低的就不会被调用。 
- 使用以下方式获得或者改变优先级
  - getPriority setPriority

6. 守护(daemon)线程

- 线程分为**用户线程**和**守护线程**
- 虚拟机必须确保用户线程(如main)执行完毕
- 虚拟机不用等待守护线程(gc)执行完毕
- 如，后台记录操作日志，监控内存，垃圾回收。

###　2.6 线程同步

- 并发：同一个对象被多个线程同时操作。
- 线程的同步其实就是一种等待机制，多个需要同时访问此对象的线程进入这个对象的等待池形成队列。，等待前面的线程使用完毕，下一个线程再操作
- 由于同一进程的多个线程共享同一块存储空间，在带来方便的同时也带来了访问冲突问题，为了保证数据在方法中被访问的正确性，在访问时加入锁机制**synchronized**，当一个线程获得对象的排他锁，独占资源，其他线程必须等待，使用后释放锁即可
  - 一个线程持有锁会导致其他所有需要此锁的线程挂机
  - 在多线程竞争下，枷锁，释放锁会导致比较多的上下文切换和调度延迟，引起性能问题
  - 如果一个优先级高的线程等待一个优先级低的线程释放锁会导致优先级倒置，引起性能问题

### 2.7 同步方法

- 由于我们可以通过private关键字保证数据对象只能被方法访问到，所以我们只需要针对方法提出一套机制，这套机制就是synchronized关键字，它包括两种方法：synchronized方法和synchronized块。同步方法：**public synchronized void method(int args){}**
- synchronized方法控制堆对象的访问，每个对象都对应一把锁，每个synchronized方法都必须获得调用该方法的对象的锁才能执行，否则会线程阻塞，方法一旦执行，就会独占该锁，知到方法返回才会释放锁，后面被阻塞的线程才能获得这个锁，继续执行。
- 弊端：若将一个大的方法申明为synchronized将会影响效率。
  - 方法里面需要修改的地方才需要锁，锁太多会浪费资源
- synchronized默认锁的对象是this

### 2.8 同步块

- 同步块：sychronized(Obj){}
- Obj称之为同步监视器
  - Obj可以是任何对象，，但是推荐使用共享资源作为同步监视器
  - 同步方法中无需指定同步监视器因为同步方法中的同步监视器就是this，就是对象本身，或者是class
- 同步监视器的执行过程
  1. 第一个线程访问，锁定同步监视器，执行其中的代码
  2. 第二个线程访问，发现同步监视器被锁定，无法访问
  3. 第一个线程访问完毕，解锁同步监视器
  4. 第二个线程访问，发现同步监视器没有锁，然后锁定并访问。

### 2.9 死锁

- 多个线程各自占有一些共享资源，并且相互等待其他线程占有的资源才能运行，而导致两个或多个线程都在等待对方法释放资源，都停止执行的情形。某个同步块同时拥有连个以上对象的锁时会方法死锁问题。
- 产生死锁的条件
  - 互斥条件：一个资源每次只能被一个进程使用
  - 请求与保持条件：一个进程因请求资源而阻塞时，对以获得的资源保持不放
  - 不剥夺条件：进程已获得的资源，在未使用之前，不能强行剥夺
  - 循环等待条件：若干个进程之间形成一种头尾相接的循环等待资源关系

### 3.0 Lock(锁)

- 从JDK5.0开始，Java提供了更强大的线程同步机制——通过显示定义同步锁对象来实现。同步锁使用Lock对象充当。
- java.util.concurrent.locks.Lock接口是控制多个线程对共享资源进行访问的工具。锁提供了对共享资源的独占访问，每次只能有一个线程对Lock对象加锁，线程开始访问共享资源之前应先获得Lock对象。
- ReentrantLock类(可重入锁)实现了Lock,它拥有与synchronized相同的并发性和内存语义，在实现线程安全的控制中，比较常用的是RenntrantLock，可以显示的加锁释放锁
- synchronzied与Lock的对比
  - Lock是显示锁(手动开启和关闭锁) synchronized是隐式锁，出了作用域自动释放
  - Lock只有代码块锁，synchronized有代码块锁和方法锁
  - 使用lock锁,JVM将花费较少的时间来调度线程，性能最好。并且具有更好的扩展性(提供更多的子类)
  - 使用的优先顺序
    - Lock > 同步代码块(已经进入方法体，分配了相应的资源)>同步方法(在方法体之外)
  1. synchronized是关键字属于jvm层面，Lock是类属于API层面
  2. 使用方法
     1. synchronized 不需要手动释放，执行完毕后会自动释放
     2. ReentrantLock则需要用户手动释放锁，若没有主动释放则可能导致死锁
  3. synchronized不可中断，除非抛出异常或者正常运行完成；ReentrantLock 可中断，1 设置超时方法 trylock(long timeout,TimeOut unit) 2.lockInterrupt() 放在代码块中调用interrupt()方法可中断。
  4. synchronized是非公平锁，ReentrantLock两者都可以，默认非公平锁，构造方法可以传入boolean值，true为公平锁，false为非公平锁。
  5. 锁绑定多个Condition
     1. synchronized没有
     2. RenntrantLock用来实现分组唤醒，精确唤醒。而不像synchronized随即唤醒一个或者全部唤醒。

### 4.0 线程协作

生产者消费者模式

- java提供了几个方法解决线程之间的通信问题
  |方法名||
  |--|--|
  |wait()|表示线程一直等待，直到其他线程通知，与sleep不同，会释放锁|
  |wait(long timeout)|指定等待的毫秒数|
  |notify()|唤醒一个处于等待状态的线程|
  |notifyAll()|唤醒同一个对象上所有调用wait()方法的线程，优先级别高的线程优先调用|
  **注意**: 均是Object类的方法，都只能在同步方法或者同步代码块中使用，否则会抛出异常llegalMonitorStateException
- 解决方式
  并发写作模式"生产者/消费者模式"-->管城法
  - 生产者:负责生产数据的模块(可能是方法，对象，线程，进程)
  - 消费者:负责处理数据的模块(可能是方法，对象，线程，进程)
  - 缓冲区:消费者不能直接使用生产者的数据,他们之间有个缓冲区
  **生产者将生产好的数据放入缓冲区，消费者从缓冲区中拿出数据**

### 4.1 使用线程池

- 背景：经常创建和销毁，使用量特别大的资源，比如在并发的情况下的线程，对性能影响很大
- 思路：提前创建好多个线程，放入线程池中，使用时直接获取，使用后放回池中
- 好处：
  - 提高响应速度(减少了创建新线程的时间)
  - 降低资源消耗
  - 便于线程管理
    - corePoolSize:核心池的大小
    - maximumPoolSize:最大线程数
    - keepAliveTime:线程没有任务时最多保持多长时间后终止

## 锁

1. 公平锁(ReentrantLock())和非公平锁(RenntrantLock(True))
   1. 公平锁是指多个多个线程按照申请锁的顺序来获取，类似排队打饭，先来后到
   2. 非公平锁：多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的宪曾比先申请的线程优先获取锁，在高并发的情况下，有可能造成优先级反转或者饥饿现象
   3. 区别
      1. 公平锁在并发环境中，每个线程获取锁时会先查看此锁维护的等待队列，如果为空，或者当前线程是等待队列的第一个，就占有，否则就会加入到等待队列，以后按照FIFO的规则从队列中获取到自己
      2. 非公平锁，直接尝试占有锁，如果尝试失败，就在采用类似公平锁的机制
2. 可重入锁(递归锁)
   1. 同一线程外层函数获取锁之后，内层递归函数仍然能获取该锁的代码，在同一线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。即线程可以进入任何一个它已经拥有的锁所同步的代码块。
   2. ReentrantLock/Synchronized就是一个典型的可重入锁
   3. 可重入锁最大作用避免死锁
3. 自旋锁
   1. 是指尝试获取锁的线程不会立即阻塞，而是采用循环的方式尝试获取锁，这样的好处是减少线程上下文切换的消耗，缺点是循环会消耗cpu
4. 独占锁(写锁)/共享锁(读锁)/互斥锁
   1. 独占锁：指该锁一次只能被一个线程所持有。对ReentrantLock和Synchronized而言都是独占锁
   2. 共享锁：指该锁能够被多个线程所持有
   3. 对于ReentWriteLock而言其读锁是共享锁，写锁是独占锁。

### 独占锁


## Java.util.concurrent

### 1. volatile

内存可见性问题是，当多个线程操作**共享数据**时，彼此不可见。相较于synchronized 是一种轻量级的同步策略。
**volatile**:当多个线程操作共享数据时，可以保存内存中的数据可见。
1. volitale不具备“互斥性”
2. volatile不能保证变量的"原子性"
3. 禁止指令重排

### 2.变量的原子性问题

1. i++的原子性问题：i++操作实际分为三个步骤：“读改写”

```java
int i = 10
i = i++ // i=10
//实际操作
int tem = i //读
i = i+1 //改
i = tem //写
```
2. 原子变量：jdk1.5以后java.util.concurrent.atomic包下提供了常用的原子变量.
   1. volitale 保证内存可见性

### 3. CAS(compare-and-swap)算法保证数据原子性

1. CAS是硬件对于并发操作共享数据的支持
2. CAS包含了三个数：内存值V 预估值A 更新值B 当且仅当V == A时，V==B，否则不做任何操作。
3. CAS的全称为Compare-And-Swap,他是一条CPU并发语言。他的功能时判断内存某个位置的值是否为预期值，如果是则更改新的值，这个过程是原子的。
4. unsafe.getAndAddInt() 底层汇编类

假设线程A线程B同时执行getAndAddInt操作(分别跑在不同CPU上):
1. AtomicInteger里面的value原始值为3，即主内存中AtomicInteger的value为3，根据JMM模型，线程A和线程B各自持有一份值为3的value的副本到各自的工作内存。
2. 线程A通过getIntVolatile(var1,var2)拿到value值为3，这时线程A被挂起。
3. 线程B也通过getIntVolatile(var1,var2)方法取到value值为3，此时刚好线程B没有被挂起并执行compareAndSwapInt方法比较内存值也为3，成功修改内存值为4，线程B打完收工，一切OK。
4. 这时线程A恢复，执行compareAndSwaplint方法比较，发现自己手里的值数字3和主内存的值数字4不一样，说明该值已经被其他线程抢先一步修改，那线程A本次修改失败，**只能重新读取再来一遍**
5. 线程A重新获取value值，因为变量value被volatile修饰，所以其他线程对它的修改，线程A总是能够看到的，线程A继续执行compareAndSwaplint进行比较替换，直到成功。

CAS总结：比较当前工作内存中的值和主内存中的值，如果相同执行规定操作，否则继续比较直到主内存和工作内存中的值一致为止。

### CAS缺点

- 循环时间长开销很大
  - 如果CAS失败，会一直进行尝试。如果CAS长时间一直不成功，可能给CPU带来很大的开销
- 只能保证一个共享变量的原子操作
- 引出来的ABA问题：
  - CAS算法实现的一个重要前提是需要取出内存中某个时刻的数据并在当下时刻比较并替换，那么在这个时间差类会导致数据的变化
  - 比如说一个线程one从内存位置V取出A，这时候另一个线程two也从内存中取出A，并且线程two进行一些操作将值变为B，然后线程two又将V位置的数据变为A，这时候线程one进行CAS操作发现内存中仍然是A，然后线程one操作成功。
  - 尽管线程one的CAS操作成功，但是不代表这个过程没有问题。

### 原子类AtomicInteger如何避免ABA问题

- 如何解决ABA问题：理解原子引用+新增一种机制，那就是修改版本号(类似时间戳)

### Class CountDownLatch/CyclicBarrier/Semaphore

## 阻塞队列

- 性质
  - 当阻塞队列是空时，从队列中获取元素的操作将会被阻塞
  - 当阻塞队列时满的时候，往队列里添加元素的操作将被阻塞
  - 试图从空的阻塞队列中获取元素的线程将会被阻塞，直到其他的线程往空的队列插入新的元素
  - 试图往已满的阻塞队列中添加新元素的线程同样也会被阻塞，直到其他的线程从列中移除一个或者多个元素或者完全清空队列

- 接口(BlockingQueue)的实现类
  - **ArrayBlockingQueue**: 由数组结构组成的有界阻塞队列
  - **LinkedBlockingQueue**：由链表结构组成的有界(但大小默认值为Integer.Max_value)阻塞队列
  - PriorityBlockingQueue: 支持优先级排序的无界阻塞队列
  - DelayQueue: 使用优先级队列实现的延迟无界阻塞队列
  - **SynchronousQueue**: 不存储元素的阻塞队列，即单个元素的队列
  - LinkedTransferQueue: 由链表结构组成的无界阻塞队列
  - LinkBlockingDeque: 由链表结构组成的双向阻塞队列

- BlockingQueue核心方法
  |方法类型|抛出异常|特殊值|阻塞|超时|
  |--|--|--|--|--|
  |插入|adde(e)|offer(e)|put(e)|offer(e,time,unit)|
  |移除|remove()|poll()|take()|poll(time,unit)|
  |检查|element()|peek()|不可用|不可用|

  - 抛出异常
    - 当阻塞队列满时，在往队列里add插入元素会抛出lllegalStateException:Queue full
    - 当阻塞队列空时，再往队列里remove移除元素会抛出NoSuchElementException
  - 特殊值
    - 插入方法offer() 成功返回true失败返回false
    - 移除方法，成功返回队列的元素，队列里没有就返回null
  - 一直阻塞
    - 当阻塞队列满时，生产者线程试图继续往队列里put元素，队列会一直阻塞生产线程直到put数据or相应中断推出。
    - 当阻塞队列空时，消费者线程试图从队列里take元素，队列会一直阻塞消费者线程直到队列可用。
  - 超时
    - 当阻塞队列满时，队列会阻塞生产者线程一定时间，超时后生产者线程会退出

- SynchronousQueue
  - 生产一个消费一个
  - 线程安全
  - 使用
    - 生产者消费者模式
      ```java
      /*题目：一个初始值为0的变量，两个线程分别对齐进行交替操作，一个加一一个减一
      */
      class ShareDate{
        private int number = 0;
        private Lock lock = new ReentrantLock();
        private Condition condition = lock.newCondition();

        public void increment() throws Exception{
          lock.lock();
          try{
            //1. 判断
            while(number !=0){
              condition.await();
            }
            //2. 干活
            number++;
            System.out.println(Thread.currentThread().getName()+"\t"+number);
            condition.singalAll();
          }catch(Exception e){
            e.printStackTrace();
          }finally{
            lock.unlock();
          }
        }

        public void decrement() throws Exception{
          lock.lock();
          try{
            //1. 判断
            while(number ==0 ){
              condition.await();
            }
            //2. 干活
            number--;
            System.out.println(Thread.currentThread().getName()+"\t"+number);
            condition.singalAll();
          }catch(Exception e){
            e.printStackTrace();
          }finally{
            lock.unlock();
          }
        }
      }

      public class ProdConsumer_TraditionDemo{
        public static void main(String[] args){

        }
      }
      ```
    - 线程池
    - 消息中间件
