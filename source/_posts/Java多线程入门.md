---
title: Java多线程入门
date: 2018-05-19 15:11:17
tags: Java多线程
categories: 编程语言
---
本文主要介绍线程的定义，创建，使用，停止，状态图和常用方法。
主要用于概念扫盲和梳理。
<!-- more -->
多进程是指操作系统能同时运行多个任务（程序）。
多线程是指在同一程序中有多个顺序流在执行。
# 进程与线程
- 进程--资源分配的最小单位
  - 每个进程都有**独立**的代码和数据空间（进程上下文）
  - 一个进程包含多个线程
  - 进程间的切换会有较大的开销
- 线程--cpu调度的最小单位
  - 同一类线程**共享**代码和数据空间，
  - 每个线程有独立的**运行栈和程序计数器(PC)**
  - 线程切换开销小
- 多进程是指操作系统能同时运行多个任务（程序）。
- 多线程是指在同一程序中有多个顺序流在执行。
- 线程和进程一样分为五个阶段：创建、就绪、运行、阻塞、终止。
# 线程创建、使用、停止
## 创建线程
- 在Java中要想实现多线程，有两种手段，一种是继承Thread类，另外一种是实现Runnable接口。有两种方式的一个原因是Java具有单继承性质，当有些类已经实现继承，但需要多线程时，我们给它另外一种办法，实现Runnable接口
### 继承   ```Thread```  类，覆盖  ```run ```  方法（推荐使用Runable）
- run()方法是多线程程序的一个约定，所有的多线程代码都在run方法里面。

```java
        class PrimeThread extends Thread {
         long minPrime;
         PrimeThread(long minPrime) {
             this.minPrime = minPrime;
         }

         public void run() {
             // compute primes larger than minPrime
              . . .
         }
     }
``` 

- 我们复写的是run()方法，但是执行的却是start()方法。
- start()方法的调用后并不是立即执行多线程代码，而是使得该线程变为可运行态（Runnable），什么时候运行是由操作系统决定的。

```java
     PrimeThread p = new PrimeThread(143);
     p.start();
 
```

### 实现  ```Runnable```   接口，覆盖  ```run```   方法

```java
     class PrimeRun implements Runnable {
         long minPrime;
         PrimeRun(long minPrime) {
             this.minPrime = minPrime;
         }

         public void run() {
             // compute primes larger than minPrime
              . . .
         }
     }
```

在启动的多线程的时候，需要先通过Thread类的构造方法Thread(Runnable target) 构造出对象，然后调用Thread对象的start()方法来运行多线程代码。

```java
     PrimeRun p = new PrimeRun(143);
     new Thread(p).start();
```

或

```java
    new Thread(new PrimeRun(143)).start();
```


实际上所有的多线程代码都是通过运行Thread的start()方法来运行的。因此，不管是扩展Thread类还是实现Runnable接口来实现多线程，最终还是通过Thread的对象的API来控制线程的，熟悉Thread类的API是进行多线程编程的基础。
### Thread和Runnable的区别
实现Runnable接口比继承Thread类所具有的优势：
1. 适合多个相同的程序代码的线程去处理同一个资源
2. 可以避免java中的**单继承**的限制
3. 增加程序的健壮性，代码可以被多个线程共享，代码和数据独立
4. **线程池**只能放入实现Runable或callable类线程，不能直接放入继承Thread的类

## 使用线程
### 方法

|类别|方法签名|简介|
|---|---|---|
构造方法 |thread()|-
   -  | thread(String name)|-
    - |thread(Runnable target)|-
    - |thread(Runnable target,String name)|-
常用方法|void start()| 启动线程
- | static void sleep(long millis) |   休眠
 - |static void sleep(long millis,int nanos) |休眠
-  |void join()|使其他线程等待当前线程终止
- | void join(long millis)|使其他线程等待当前线程终止
-  |void join(long millis,int nanos)|使其他线程等待当前线程终止
-| static void yield( )  |  当前运行线程释放处理器资源
获取线程引用| static Thread currendThread()|返回当前运行的线程引用

### 注意事项
- main方法其实也是一个线程。在java中所以的线程都是同时启动的，至于什么时候，哪个先执行，完全看谁先得到CPU的资源。
- 在java中，每次程序运行至少启动2个线程。一个是main线程，一个是垃圾收集线程。

## 停止线程
### 错误方法
- stop()方法。
它会使线程戛然而止，**我们不知道它完成了什么工作，没有完成什么工作，**因此是错误的。
- interrupt()方法。
不要使用interrupt()方法，因为在线程run()中调用sleep(),join(),yeild()方法时，**中断状态会被清除**，isinterrupe=false
### 如何正确的停止线程
- 使用退出标志。

```java
volatile boolean keepRunning = true;
```
#### 代码示例

```java
//展示了两种创建线程的方法
//ActorThread通过继承Thread创建线程
public class ActorThread extends Thread {
	// 覆盖run------------------------------------------------------
	public void run() {
		System.out.println(getName() + "是一个演员！");
		int count = 0;
		boolean keepRunning = true;
		while (keepRunning) {
			System.out.println(getName() + "登台演出：" + (++count));
			//标注法停止线程-----------------------------------------------
			if (count == 15) {
				keepRunning = false;
			}
			//测试sleep()-----------------------
			if (count % 5 == 0) {
				try {
					Thread.sleep(1000);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
		}
		System.out.println(getName() + "的演出结束了！");
	}

	public static void main(String[] args) {
		Thread actor = new ActorThread();
		actor.setName("Mr. Thread");
		actor.start();
		Thread actressThread = new Thread(new Actress(), "Ms. Runnable");
		actressThread.start();
	}

}
//Actress通过实现Runnable接口创建线程
class Actress implements Runnable {
	@Override
	public void run() {
		System.out.println(Thread.currentThread().getName() + "是一个演员！");
		int count = 0;
		boolean keepRunning = true;
		while (keepRunning) {
			System.out.println(Thread.currentThread().getName() + "登台演出：" + (++count));
			if (count == 15) {
				keepRunning = false;
			}
			if (count % 5 == 0) {
				try {
					Thread.sleep(1000);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
		}
		System.out.println(Thread.currentThread().getName() + "的演出结束了！");
	}
}
```

#### 输出
![测试sleep方法与停止线程](http://upload-images.jianshu.io/upload_images/11861611-715eb94d0c2d8d8d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 线程状态转换
![线程状态转换](http://upload-images.jianshu.io/upload_images/11861611-ec59f10a718dba88.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 新建状态（New）：新创建了一个线程对象。
2. 就绪状态（Runnable）：线程对象创建后，其他线程调用了该对象的start()方法。该状态的线程位于可运行线程池中，变得可运行，等待获取CPU的使用权。
3. 运行状态（Running）：就绪状态的线程获取了CPU，执行程序代码。
4. 阻塞状态（Blocked）：阻塞状态是线程因为某种原因放弃CPU使用权，暂时停止运行。直到线程进入就绪状态，才有机会转到运行状态。阻塞的情况分三种：
    - 等待阻塞：运行的线程执行wait()方法，JVM会把该线程放入等待池中。(wait会**释放**持有的锁)
   - 同步阻塞：运行的线程在获取对象的同步锁时，若该同步锁被别的线程占用，则JVM会把该线程放入锁池中。
   - 其他阻塞：运行的线程执行sleep()或join()方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。（sleep是**不会释放**持有的锁）
5. 死亡状态（Dead）：线程执行完了或者因异常退出了run()方法，该线程结束生命周期。
# 常用函数说明
## sleep(long millis):线程暂停执行
在指定的毫秒数内让当前正在执行的线程休眠
##  join(long millis):指等待t线程一段时间（或终止）
- join是Thread类的一个方法，启动线程后直接调用，即join()的作用是：“等待该线程（终止）”。
- 目的
在很多情况下，主线程生成并起动了子线程，如果子线程里要进行大量的耗时的运算，主线程往往将于子线程之前结束，但是如果主线程处理完其他的事务后，需要**用到子线程的处理结果**，也就是主线程需要等待子线程执行完成之后再结束，这个时候就要用到join()方法了。
## yield():暂停当前正在执行的线程对象，并执行其他线程。
- yield()应该做的是让当前运行线程回到可运行状态，以允许具有相同优先级的其他线程获得运行机会。
- 目的
使用yield()的目的是让相同优先级的线程之间能适当的轮转执行。但是，实际中无法保证yield()达到让步目的，因为让步的线程还有可能被线程调度程序再次选中。
## wait()
- Obj.wait()，与Obj.notify()必须要与synchronized(Obj)一起使用，也就是wait,与notify是针对已经获取了Obj锁进行操作。
- 从语法角度来说就是Obj.wait(),Obj.notify必须在synchronized(Obj){...}语句块内。从功能上来说wait就是说线程在获取对象锁后，主动释放对象锁，同时本线程休眠。直到有其它线程调用对象的notify()唤醒该线程，才能继续获取对象锁，并继续执行。相应的notify()就是对对象锁的唤醒操作。
- 但有一点需要注意的是notify()调用后，并不是马上就释放对象锁的，而是在相应的synchronized(){}语句块执行结束，自动释放锁后，JVM会在wait()对象锁的线程中随机选取一线程，赋予其对象锁，唤醒线程，继续执行。这样就提供了在线程间同步、唤醒的操作。
- Thread.sleep()与Object.wait()二者都可以暂停当前线程，释放CPU控制权，主要的区别在于**Object.wait()在释放CPU同时，释放了对象锁的控制。**
##  wait和sleep区别
### 共同点： 
1. 他们都是在多线程的环境下，都可以在程序的调用处阻塞指定的毫秒数，并返回。 
2. wait()和sleep()都可以通过interrupt()方法 打断线程的暂停状态 ，从而使线程立刻抛出InterruptedException。 （不建议使用该方法）
- 如果线程A希望立即结束线程B，则可以对线程B对应的Thread实例调用interrupt方法。如果此刻线程B正在wait/sleep /join，则线程B会立刻抛出InterruptedException，在catch() {} 中直接return即可安全地结束线程。 
- 需要注意的是，InterruptedException是线程自己从内部抛出的，并不是interrupt()方法抛出的。对某一线程调用 interrupt()时，如果该线程正在执行普通的代码，那么该线程根本就不会抛出InterruptedException。但是，一旦该线程进入到 wait()/sleep()/join()后，就会立刻抛出InterruptedException 。 
### 不同点： 
1. 所属的类
Thread类的方法：sleep(),yield()等 
Object的方法：wait()和notify()等 
2.  是否释放锁
每个对象都有一个锁来控制同步访问。Synchronized关键字可以和对象的锁交互，来实现线程的同步。 
sleep方法没有释放锁，而wait方法释放了锁，使得其他线程可以使用同步控制块或者方法。 
3.  使用限制
wait，notify和notifyAll只能在同步控制方法或者同步控制块里面使用，而sleep可以在任何地方使用 


参考文章
[慕课网深入浅出Java多线程视频](http://www.imooc.com/learn/202 )
[java多线程入门学习（一）](https://blog.csdn.net/sunfc_nbu/article/details/51133477)
[Java多线程学习（吐血超详细总结）](https://blog.csdn.net/evankaka/article/details/44153709)
[oracle官方文档](https://docs.oracle.com/javase/8/docs/api/)
