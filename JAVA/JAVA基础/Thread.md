# 使用线程

- **线程的生命周期**
  
![thread-01](./assets/Thread-01.png)
![thread-02](./assets/Thread-02.png)

- **实现Runnable接口**
```
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        // ...
    }
}

public static void main(String[] args) {
    MyRunnable instance = new MyRunnable();
    Thread thread = new Thread(instance);
    thread.start();
}
```

- **实现Callable接口**
```
public class MyCallable implements Callable<Integer> {
    public Integer call() {
        return 123;
    }
}
public static void main(String[] args) throws ExecutionException, InterruptedException {
    MyCallable mc = new MyCallable();
    FutureTask<Integer> ft = new FutureTask<>(mc);
    Thread thread = new Thread(ft);
    thread.start();
    System.out.println(ft.get());
}
```
- **继承Thread**
```
public class MyThread extends Thread {
    public void run() {
        // ...
    }
}
public static void main(String[] args) {
    MyThread mt = new MyThread();
    mt.start();
}
```

# 基础线程机制
- CachedThreadPool：一个任务创建一个线程；
- FixedThreadPool：所有任务只能使用固定大小的线程；
- SingleThreadExecutor：相当于大小为 1 的 FixedThreadPool。

```
public static void main(String[] args) {
    ExecutorService executorService = Executors.newCachedThreadPool();
    for (int i = 0; i < 5; i++) {
        executorService.execute(new MyRunnable());
    }
    executorService.shutdown();
}
```

# 中断
- InterruptedException
  通过调用一个线程的 interrupt() 来中断该线程，如果该线程处于阻塞、限期等待或者无限期等待状态，那么就会抛出 InterruptedException，从而提前结束该线程。但是不能中断 I/O 阻塞和 synchronized 锁阻塞
- interrupted()
  
- Executor 的中断操作
  调用 Executor 的 shutdown() 方法会等待线程都执行完毕之后再关闭，但是如果调用的是 shutdownNow() 方法，则相当于调用每个线程的 interrupt() 方法。

# 协作

- join()  
  在线程中调用另一个线程的 join() 方法，会将当前线程挂起，而不是忙等待，直到目标线程结束。
- wait()、notify()、notifyAll()
  1. 它们都属于 Object 的一部分，而不属于 Thread。
  2. 只能用在同步方法或者同步控制块中使用，否则会在运行时抛出 IllegalMonitorStateException。
- wait() 和 sleep() 的区别
  1. wait() 是 Object 的方法，而 sleep() 是 Thread 的静态方法；
  2. wait() 会释放锁，sleep() 不会。
- await() 、signal() 、signalAll()
  1. java.util.concurrent 类库中提供了 Condition 类来实现线程之间的协调，可以在 Condition 上调用 await() 方法使线程等待，其它线程调用 signal() 或 signalAll() 方法唤醒等待的线程。相比于 wait() 这种等待方式，await() 可以指定等待的条件，因此更加灵活。

# 线程状态
- 新建（NEW）  
   创建后尚未启动。
- 可运行（RUNABLE）  
   正在 Java 虚拟机中运行。但是在操作系统层面，它可能处于运行状态，也可能等待资源调度（例如处理器资源），资源调度完成就进入运行状态。所以该状态的可运行是指可以被运行，具体有没有运行要看底层操作系统的资源调度。
- 阻塞（BLOCKED）  
  请求获取 **monitor lock** 从而进入 synchronized 函数或者代码块，但是其它线程已经占用了该 monitor lock，所以出于阻塞状态。要结束该状态进入从而 RUNABLE 需要其他线程释放 monitor lock。
- 无限期等待（WAITING）  
  等待其它线程显式地唤醒。
  阻塞和等待的区别在于，阻塞是被动的，它是在等待获取 monitor lock。而等待是主动的，通过调用 Object.wait() 等方法进入。
- 限期等待（TIMED_WAITING）  
  无需等待其它线程显式地唤醒，在一定时间之后会被系统自动唤醒。
- 死亡（TERMINATED）  
  可以是线程结束任务之后自己结束，或者产生了异常而结束。