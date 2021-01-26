
# Synchronized（基于JVM）
- **1、同步代码块:作用于对象层面**
```
public void func() {
    synchronized (this) {
        // ...
    }
}
```
- **2、同步方法：作用于对象层面**
```
public synchronized void func () {
    // ...
}
```
- **3、同步类：作用于类**
```
public class SynchronizedExample {

    public void func2() {
        synchronized (SynchronizedExample.class) {
            for (int i = 0; i < 10; i++) {
                System.out.print(i + " ");
            }
        }
    }
}
```
- **4、同步静态方法：作用于类**
```
public synchronized static void fun() {
    // ...
}
```

- **5、底层原理**  
  
有如下代码：
```
package com.paddx.test.concurrent;
public class SynchronizedDemo {
    public void method() {
        synchronized (this) {
            System.out.println("Method 1 start");
        }
    }
}
```
**反编译后结果**
```
public method()V
    TRYCATCHBLOCK L0 L1 L2 null
    TRYCATCHBLOCK L2 L3 L2 null
   L4
    LINENUMBER 10 L4
    ALOAD 0
    DUP
    ASTORE 1
    MONITORENTER
   L0
    LINENUMBER 11 L0
    GETSTATIC java/lang/System.out : Ljava/io/PrintStream;
    LDC "Method 1 start"
    INVOKEVIRTUAL java/io/PrintStream.println (Ljava/lang/String;)V
   L5
    LINENUMBER 12 L5
    ALOAD 1
    MONITOREXIT
   L1
    GOTO L6
   L2
    ASTORE 2
    ALOAD 1
    MONITOREXIT
   L3
    ALOAD 2
    ATHROW
   L6
    LINENUMBER 13 L6
    RETURN
   L7
    LOCALVARIABLE this Lconcurrent/SynchronizedTest; L4 L7 0
    MAXSTACK = 2
    MAXLOCALS = 3
```
**解析：**  
1. **monitorenter**：每个对象都是一个监视器锁（monitor）。当monitor被占用时就会处于锁定状态，线程执行monitorenter指令时尝试获取monitor的所有权，过程如下：  
1.1、如果monitor的进入数为0，则该线程进入monitor，然后将进入数设置为1，该线程即为monitor的所有者；  
1.2、如果线程已经占有该monitor，只是重新进入，则进入monitor的进入数加1；  
1.3、如果其他线程已经占用了monitor，则该线程进入阻塞状态，直到monitor的进入数为0，再重新尝试获取monitor的所有权；  
1. **monitorexit**：执行monitorexit的线程必须是objectref所对应的monitor的所有者。指令执行时，monitor的进入数减1，如果减1后进入数为0，那线程退出monitor，不再是这个monitor的所有者。其他被这个monitor阻塞的线程可以尝试去获取这个 monitor 的所有权。  
2.1、monitorexit指令出现了两次，第1次为同步正常退出释放锁；第2次为发生异步退出释放锁；

**延伸：**  
1. Synchronized的语义底层是通过一个monitor的对象来完成，其实wait/notify等方法也依赖于monitor对象，这就是为什么只有在同步的块或者方法中才能调用wait/notify等方法，否则会抛出java.lang.IllegalMonitorStateException的异常的原因。


# ReentrantLock（JDK）
```
public class LockExample {

    private Lock lock = new ReentrantLock();

    public void func() {
        lock.lock();
        try {
            for (int i = 0; i < 10; i++) {
                System.out.print(i + " ");
            }
        } finally {
            lock.unlock(); // 确保释放锁，从而避免发生死锁。
        }
    }
}
public static void main(String[] args) {
    LockExample lockExample = new LockExample();
    ExecutorService executorService = Executors.newCachedThreadPool();
    executorService.execute(() -> lockExample.func());
    executorService.execute(() -> lockExample.func());
}
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9
```
# 两者区别  
1. synchronized 是 JVM 实现的，而 ReentrantLock 是 JDK 实现的。
1. 新版本 Java 对 synchronized进行了很多优化，例如自旋锁等，synchronized 与 ReentrantLock 大致相同。
1. 当持有锁的线程长期不释放锁的时候，正在等待的线程可以选择放弃等待，改为处理其他事情。ReentrantLock 可中断，而 synchronized 不行。
1. 公平锁是指多个线程在等待同一个锁时，必须按照申请锁的时间顺序来依次获得锁。synchronized 中的锁是非公平的，ReentrantLock 默认情况下也是非公平的，但是也可以是公平的。
1. 一个 ReentrantLock 可以同时绑定多个 Condition 对象。