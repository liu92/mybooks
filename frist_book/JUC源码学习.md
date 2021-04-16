## 5、condition源码分析

5.1、什么是Condition

  Conditon是JUC里面提供于控制线程释放锁，然后进行等待其他获取的线程发生signal信号来进行唤醒的工具类。

Condition 是一种广义上的条件队列。他为线程提供了一种更为灵活的**等待 / 通知**模式，线程在调用 await 方法后执行**挂起**操作，直到线程等待的某个条件为真时才会被**唤醒**。Condition 必须要配合 Lock 一起使用，因为对共享状态变量的访问发生在多线程环境下。一个 Condition 的实例必须与一个 Lock 绑定，因此 Condition 一般都是作为 Lock 的内部实现。

5.2、condition接口定义的一些方法

```java
//阻塞
// 造成当前线程在接到信号或被中断之前一直处于等待状态。
void await() throws InterruptedException; 
// 造成当前线程在接到信号之前一直处于等待状态。【注意：该方法对中断不敏感】。
void awaitUninterruptibly(); 
// 造成当前线程在接到信号、被中断或到达指定等待时间之前一直处于等待状态。返回值表示剩余时间，如果在`nanosTimeout` 之前唤醒，那么返回值 `= nanosTimeout - 消耗时间` ，如果返回值 `<= 0` ,则可以认定它已经超时了。
long awaitNanos(long nanosTimeout) throws InterruptedException;
// 造成当前线程在接到信号、被中断或到达指定等待时间之前一直处于等待状态。
boolean await(long time, TimeUnit unit) throws InterruptedException; 
// 造成当前线程在接到信号、被中断或到达指定最后期限之前一直处于等待状态。如果没有到指定时间就被通知，则返回 true ，否则表示到了指定时间，返回返回 false 。
boolean awaitUntil(Date deadline) throws InterruptedException; 


//唤醒
// 唤醒一个等待线程。该线程从等待方法返回前必须获得与Condition相关的锁。
void signal(); 
// 唤醒所有等待线程。能够从等待方法返回的线程必须获得与Condition相关的锁。
void signalAll(); 

```

5.3、在调用lock.newCondition()其实调用的是ReentrantLock类中的方法

```java
public Condition newCondition() {
        return sync.newCondition();
    }
```

其中`sync`为继承自`AQS`的同步器。

```java
 final ConditionObject newCondition() {
            return new ConditionObject();
        }
```



5.4、Condition的实现ConditionObject类

这个类是AQS中的一个内部类



```java
 public class ConditionObject implements Condition, java.io.Serializable {
   
   private static final long serialVersionUID = 1173984872572414699L;
        /** First node of condition queue. */
        //头节点
        private transient Node firstWaiter;
        /** Last node of condition queue. */
        //尾节点
        private transient Node lastWaiter;

        /**
         * Creates a new {@code ConditionObject} instance.
         */
        public ConditionObject() { }

        // Internal methods

        /**
         * Adds a new waiter to wait queue.
         * @return its new wait node
         condition queue是一个单向链表，链表中我们使用nextWaiter属性来串联链表。
         就像在sync queue中不会使用nextWaiter属性来串联链表一样，在condition queue中，
         也并不会用到prev, next属性，它们的值都为null。
         也就是说，在条件队列中，Node节点真正用到的属性只有三个：
         
          thread：代表当前正在等待某个条件的线程
          waitStatus：条件的等待状态
          nextWaiter：指向条件队列中的下一个节点
         */
        private Node addConditionWaiter() {
            Node t = lastWaiter;
            // If lastWaiter is cancelled, clean out.
            //如果尾节点被cancelled，则先遍历整链表，清除所有被cancel的节点
            if (t != null && t.waitStatus != Node.CONDITION) {
                unlinkCancelledWaiters();
                t = lastWaiter;
            }
            // 将当前线程包装成Node 放到条件队列中
            // 注意: 存在不同的线程同时进入队列吗？不存在。 因为能调用await()方法的线程必然是已经获得了锁的，而获取到锁的线程只有一个，所以这里不存在并发。
            Node node = new Node(Thread.currentThread(), Node.CONDITION);
            if (t == null)
                firstWaiter = node;
            else
                t.nextWaiter = node;
            lastWaiter = node;
            return node;
        }

 }
```





5.5、await()方法,调用的是 AQS中 内部类 ConditionObject的方法。

```java
public final void await() throws InterruptedException {
   //1.线程如果中断，那么抛出中断异常
   if (Thread.interrupted())
     throw new InterruptedException();
   //2.将当前线程保证称为一个Node节点，加入FIFO队列中
   Node node = addConditionWaiter();
   //3.释放锁
   long savedState = fullyRelease(node);
   int interruptMode = 0;
   //4、判断节点是否在同步队列(注意非Condition队列)中，如果没有，则挂起当前线程，因为该线程尚未具备数据竞争资格。
   while (!isOnSyncQueue(node)) {
      //5、挂起线程
     LockSupport.park(this);
     //6、中断直接返回
     if ((interruptMode = checkInterruptWhileWaiting(node)) != 0)
       break;
   }
  //7、参与数据竞争(非中断执行)
   if (acquireQueued(node, savedState) && interruptMode != THROW_IE)
     interruptMode = REINTERRUPT;
  //8、清楚条件队列中状态为cancelled的节点
   if (node.nextWaiter != null) // clean up if cancelled
     unlinkCancelledWaiters();
   if (interruptMode != 0)
     reportInterruptAfterWait(interruptMode);
 }
```





6、CopyOnWriteArrayList写时复制

```java
 public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            newElements[len] = e;
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }
```



















