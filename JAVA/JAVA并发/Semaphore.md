# 概念
- Semaphore 类似于操作系统中的信号量，可以控制对互斥资源的访问线程数。

# 源码
- 主要方法
```
// 创建具有给定的许可数和非公平的公平设置的 Semaphore。
Semaphore(int permits)
// 创建具有给定的许可数和给定的公平设置的 Semaphore。
Semaphore(int permits, boolean fair)
// 释放给定数目的许可，将其返回到信号量。
release(int permits)
// 从此信号量获取一个许可，在提供一个许可前一直将线程阻塞，否则线程被中断。
acquire()

```

# 举例
```
public class SemaphoreTest {

	 static Semaphore semaphore = new Semaphore(5,true); // 最大线程数
	 public static void main(String[] args) {
		 for(int i=0;i<100;i++){
			 new Thread(new Runnable() {
	
		 @Override
		 public void run() {
			 test();
		 }
			 }).start();
		 }
	
	 }
	
	 public static void test(){
		 try {
		 //申请一个请求
			 semaphore.acquire(); // 如果线程超过5个,其他线程会阻塞到这来,知道线程执行结束释放许可
		} catch (InterruptedException e1) {
				e1.printStackTrace();
		 }
		System.out.println(Thread.currentThread().getName()+"进来了");
		 try {
			 Thread.sleep(1000);
		 } catch (InterruptedException e) {
			 e.printStackTrace();
		}
		 System.out.println(Thread.currentThread().getName()+"走了");
		 //释放一个请求
		 semaphore.release();
	 }
 }
```