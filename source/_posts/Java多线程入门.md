---
title: Java多线程创建和常用方法
date: 2018-05-19 15:11:17
tags: 并发
categories: 并发
---
本文主要介绍线程的定义，创建，使用，停止，状态图和常用方法。
主要用于概念扫盲和梳理。
多进程是指操作系统能同时运行多个任务（程序）。
多线程是指在同一程序中有多个顺序流在执行。

<!-- more -->

# 先说并发

## 1. 为什么要用到并发

一直以来，硬件的发展极其迅速，也有一个很著名的"摩尔定律"，可能会奇怪明明讨论的是并发编程为什么会扯到了硬件的发展，这其中的关系应该是多核CPU的发展为并发编程提供的硬件基础。摩尔定律并不是一种自然法则或者是物理定律，它只是基于认为观测数据后，对未来的一种预测。
按照所预测的速度，我们的计算能力会按照指数级别的速度增长，不久以后会拥有超强的计算能力，正是在畅想未来的时候，2004年，Intel宣布4GHz芯片的计划推迟到2005年，然后在2004年秋季，Intel宣布彻底取消4GHz的计划，也就是说**摩尔定律的有效性超过了半个世纪戛然而止**。

但是，聪明的硬件工程师并没有停止研发的脚步，他们为了进一步提升计算速度，而不是再追求单独的计算单元，而是将多个计算单元整合到了一起，也就是形成了多核CPU。短短十几年的时间，家用型CPU,比如Intel i7就可以达到4核心甚至8核心。而专业服务器则通常可以达到几个独立的CPU，每一个CPU甚至拥有多达8个以上的内核。因此，摩尔定律似乎在CPU核心扩展上继续得到体验。因此，多核的CPU的背景下，催生了并发编程的趋势，通过并发编程的形式可以将多核CPU的计算能力发挥到极致，性能得到提升。
顶级计算机科学家Donald Ervin Knuth如此评价这种情况：在我看来，这种现象（并发）或多或少是由于硬件设计者无计可施了导致的，他们将摩尔定律的责任推给了软件开发者。
另外，在特殊的业务场景下先天的就适合于并发编程。比如在图像处理领域，一张1024X768像素的图片，包含达到78万6千多个像素。即时将所有的像素遍历一边都需要很长的时间，面对如此复杂的计算量就需要充分利用多核的计算的能力。又比如当我们在**网上购物时，为了提升响应速度，需要拆分，减库存，生成订单等等这些操作，就可以进行拆分利用多线程的技术完成**。面对复杂业务模型，并行程序会比串行程序更适应业务需求，而并发编程更能吻合这种业务拆分 。

## 2. 并发编程缺点
- 频繁的上下文切换
时间片是CPU分配给各个线程的时间，因为时间非常短，所以CPU不断通过切换线程，让我们觉得多个线程是同时执行的，`时间片`一般是几十毫秒。而每次切换时，需要保存当前的状态起来，以便能够进行恢复先前状态，而这个切换时非常损耗性能，过于频繁反而无法发挥出多线程编程的优势。

通常减少上下文切换可以采用无锁并发编程，CAS算法，使用最少的线程和使用协程。
1. 无锁并发编程：可以参照concurrentHashMap**锁分段**的思想，不同的线程处理不同段的数据，这样在多线程竞争的条件下，可以减少上下文切换的时间。
2. **CAS**算法，利用Atomic下使用CAS算法来更新数据，使用了乐观锁，可以有效的减少一部分不必要的锁竞争带来的上下文切换
3. 使用最少线程：避免创建不需要的线程，比如任务很少，但是创建了很多的线程，这样会造成大量的线程都处于等待状态
4. 协程：在单线程里实现多任务的调度，并在单线程里维持多个任务间的切换

-  线程安全


## 3. 一些概念

### 3.1 同步与异步
同步和异步通常用来形容`一次方法调用`。同步方法调用一开始，调用者必须等待被调用的方法结束后，调用者后面的代码才能执行。而异步调用，指的是，调用者不用管被调用方法是否完成，都会继续执行后面的代码，当被调用的方法完成后会通知调用者。
比如，在超时购物，如果一件物品没了，你得等仓库人员跟你调货，直到仓库人员跟你把货物送过来，你才能继续去收银台付款，这就类似同步调用。而异步调用了，就像网购，你在网上付款下单后，什么事就不用管了，该干嘛就干嘛去了，当货物到达后你收到通知去取就好。

### 3.2 并发与并行
并发和并行是十分容易混淆的概念。**并发指的是多个任务交替进行，而并行则是指真正意义上的“同时进行”**。实际上，如果系统内只有一个CPU，而使用多线程时，那么真实系统环境下不能并行，只能通过切换时间片的方式交替进行，而成为并发执行任务。
真正的并行也只能出现在拥有多个CPU的系统中。

### 3.3 阻塞和非阻塞
阻塞和非阻塞通常用来形容多`线程间的相互影响`，比如一个线程占有了临界区资源，那么其他线程需要这个资源就必须进行等待该资源的释放，会导致等待的线程挂起，这种情况就是阻塞，而非阻塞就恰好相反，它强调没有一个线程可以阻塞其他线程，**所有的线程都会尝试地往前运行**。

### 3.4 临界区
临界区用来表示一种公共资源或者说是共享数据，可以被多个线程使用。但是每个线程使用时，一旦临界区资源被一个线程占有，那么其他线程必须等待。


# 进程与线程

## 进程--资源分配的最小单位
  - 每个进程都有**独立**的代码和数据空间（进程上下文）
  - 一个进程包含多个线程
  - 进程间的切换会有较大的开销

## 线程--cpu调度的最小单位
  - 同一类线程**共享**代码和数据空间，
  - 每个线程有独立的**运行栈和程序计数器(PC)**
  - 线程切换开销小

## 其他
- 多进程是指操作系统能同时运行多个任务（程序）。
- 多线程是指在同一程序中有多个顺序流在执行。
- 线程和进程一样分为五个阶段：创建、就绪、运行、阻塞、终止。

# 线程创建、使用、停止

## 创建线程
- 在Java中要想实现多线程，有三种手段，一种是继承Thread类，另外一种是实现Runnable接口，三是实现Callable 接口。
实现 Runnable 和 Callable 接口的类只能当做一个可以在线程中运行的任务，不是真正意义上的线程，**因此最后还需要通过 Thread 来调用**。可以说任务是通过线程驱动从而执行的。

### 1. 继承Thread 类，覆盖run方法（推荐使用Runable）
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

### 2. 实现Runnable接口，覆盖run方法

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

在启动的多线程的时候，需要先通过Thread类的构造方法`Thread(Runnable target) `构造出对象，然后调用Thread对象的start()方法来运行多线程代码。

```java
     PrimeRun p = new PrimeRun(143);
     new Thread(p).start();
```

或

```java
    new Thread(new PrimeRun(143)).start();
```

实际上**所有的多线程代码都是通过运行Thread的start()方法来运行的**。因此，不管是扩展Thread类还是实现Runnable接口来实现多线程，最终还是通过Thread的对象的API来控制线程的，**熟悉Thread类的API是进行多线程编程的基础**。

### 3. 实现 Callable 接口
与 Runnable 相比，Callable 可以有返回值，返回值通过` FutureTask` 进行**封装**。

```
public class MyCallable implements Callable<Integer> {
    public Integer call() {
        // ...
    }
    public static void main(String[]  args) {
        MyCallable mc = new MyCallable();
        FutureTask<Integer> ft = new FutureTask<>(mc);
        Thread thread = new Thread(ft);
        thread.start();
        System.out.println(ft.get());
    }
}
```

### Thread和Runnable的区别
实现Runnable接口比继承Thread类所具有的优势：
1. 适合多个相同的程序代码的线程去处理同一个资源
2. 可以避免java中的`单继承的限制`
3. 增加程序的健壮性，代码可以被多个线程共享，代码和数据独立
4. `线程池`**只能放入实现Runable或callable类线程，不能直接放入继承Thread的类**

## 使用线程
### 方法

|类别|方法签名|简介|
|---|---|---|
|构造方法 |thread()|-
 |  -  | thread(String name)|-
|    - |thread(Runnable target)|-
 |   - |thread(Runnable target,String name)|-
|常用方法|void start()| 启动线程
|- | static void sleep(long millis) |   休眠
| - |static void sleep(long millis,int nanos) |休眠
|-  |void join()|使其他线程等待当前线程终止
|- | void join(long millis)|使其他线程等待当前线程终止
|-  |void join(long millis,int nanos)|使其他线程等待当前线程终止
|-| static void yield( )  |  当前运行线程释放处理器资源
|获取线程引用| static Thread currendThread()|返回当前运行的线程引用

### 注意事项
- main方法其实也是一个线程。在java中所以的线程都是同时启动的，至于什么时候，哪个先执行，完全看谁先得到CPU的资源。
- 在java中，每次程序运行至少启动2个线程。一个是main线程，一个是垃圾收集线程。

## 停止线程

### 错误方法
- stop()方法。
它会使线程戛然而止，**我们不知道它完成了什么工作，没有完成什么工作，**因此是错误的。
- interrupt()方法。
不要使用interrupt()方法，因为**在线程run()中调用sleep(),join(),yeild()方法时，中断状态会被清除**，`isinterrupe=false`

### 如何正确的停止线程
**使用退出标志。**

`volatile boolean keepRunning = true;`

#### 代码示例

```java
public class ActorThread extends Thread {//继承Thread创建线程
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

class Actress implements Runnable {//实现Runnable接口创建线程
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
在指定的毫秒数内让当前正在执行的线程休眠。
`sleep()` 可能会抛出 InterruptedException。因为异常不能跨线程传播回 main() 中，因此必须在本地进行处理。线程中抛出的其它异常也同样需要在本地进行处理。

##  join(long millis):指等待t线程一段时间（或终止）
- join是Thread类的一个方法，启动线程后直接调用，即join()的作用是：“等待该线程（终止）”。
- 目的
在很多情况下，主线程生成并起动了子线程，如果子线程里要进行大量的耗时的运算，主线程往往将于子线程之前结束，但是如果主线程处理完其他的事务后，需要**用到子线程的处理结果**，也就是主线程需要等待子线程执行完成之后再结束，这个时候就要用到join()方法了。

## yield():暂停当前正在执行的线程对象，并执行其他线程。
- yield()应该做的是让当前运行线程回到可运行状态，以允许具有相同优先级的其他线程获得运行机会。
- 目的
使用yield()的目的是让相同优先级的线程之间能适当的轮转执行。但是，实际中无法保证yield()达到让步目的，因为让步的线程还有可能被线程调度程序再次选中。

## wait()
本小节转载自[使用wait/notify/notifyAll实现线程间通信的几点重要说明](https://blog.csdn.net/ns_code/article/details/17225469)

### 用于线程间通信
在Java中，可以通过配合调用Object对象的`wait()`方法和`notify()`方法或`notifyAll()`方法来实现**线程间的通信**。

### 语义
在线程中调用`wait()`方法，将阻塞等待其他线程的通知（其他线程调用`notify()`方法或`notifyAll()`方法），在线程中调用`notify()`方法或`notifyAll()`方法，将通知其他线程从`wait()`方法处返回。

 Object是所有类的超类，它有5个方法组成了等待/通知机制的核心：notify()、notifyAll()、wait()、wait(long)和wait(long，int)。在Java中，所有的类都从Object继承而来，因此，所有的类都拥有这些共有方法可供使用。而且，由于他们都被声明为final，因此在子类中不能覆写任何一个方法。

### 使用中需要注意
1. `wait()` --- "我去等待了"
```java 
 public final void wait()  throws InterruptedException,IllegalMonitorStateException 
 ```   

- 该方法用来将当前线程置入休眠状态，直到接到通知或被中断为止。在调用wait（）之前，线程**必须要获得该对象的对象级别锁**，即只能在同步方法或同步块中调用wait（）方法。
- 进入wait（）方法后，当前线程释放锁。在从wait（）返回前，线程与其他线程竞争重新获得锁。如果调用wait（）时，没有持有适当的锁，则抛出`IllegalMonitorStateException`，它是RuntimeException的一个子类，因此，不需要try-catch结构。

2. `notify()`---"大家来争我"

```java   
public final native void notify() throws IllegalMonitorStateException
```

- 该方法也要在同步方法或同步块中调用，即在调用前，线程也必须要获得该对象的对象级别锁，的如果调用notify（）时没有持有适当的锁，也会抛出`IllegalMonitorStateException`
- 该方法用来通知那些可能等待该对象的对象锁的其他线程。如果有多个线程等待，则线程规划器任意**挑选出其中一个wait（）状态的线程来发出通知**，并使它等待获取该对象的对象锁（***notify后，当前线程不会马上释放该对象锁，wait所在的线程并不能马上获取该对象锁，要等到程序退出synchronized代码块后，当前线程才会释放锁，wait所在的线程也才可以获取该对象锁***），但不惊动其他同样在等待被该对象notify的线程们。当第一个获得了该对象锁的wait线程运行完毕以后，它会释放掉该对象锁，此时如果该对象没有再次使用notify语句，则即便该对象已经空闲，其他wait状态等待的线程由于没有得到该对象的通知，会继续阻塞在wait状态，直到这个对象发出一个notify或notifyAll。这里需要注意：它们等待的是被notify或notifyAll，而不是锁。这与下面的notifyAll（）方法执行后的情况不同。 

3. `notifyAll()`

```java
     public final native void notifyAll() throws IllegalMonitorStateException
```

该方法与notify（）方法的工作方式相同，重要的一点差异是：
notifyAll使所有原来在该对象上wait的线程统统退出wait的状态（即全部被唤醒，不再等待notify或notifyAll，但由于此时还没有获取到该对象锁，因此还不能继续往下执行），变成等待获取该对象上的锁，一旦该对象锁被释放（notifyAll线程退出调用了notifyAll的synchronized代码块的时候），他们就会去竞争。如果其中一个线程获得了该对象锁，它就会继续往下执行，在它退出synchronized代码块，释放锁后，其他的已经被唤醒的线程将会继续竞争获取该锁，一直进行下去，直到所有被唤醒的线程都执行完毕。
4. wait（long）和wait（long,int）
显然，这两个方法是设置等待超时时间的，后者在超值时间上加上ns，精度也难以达到，因此，该方法很少使用。对于前者，如果在等待线程接到通知或被中断之前，已经超过了指定的毫秒数，则它通过竞争重新获得锁，并从wait（long）返回。另外，需要知道，如果设置了超时时间，当wait（）返回时，我们不能确定它是因为接到了通知还是因为超时而返回的，因为wait（）方法不会返回任何相关的信息。但一般可以通过设置标志位来判断，在notify之前改变标志位的值，在wait（）方法后读取该标志位的值来判断，当然为了保证notify不被遗漏，我们还需要另外一个标志位来循环判断是否调用wait（）方法。
5. 深入理解：
 如果线程调用了对象的wait（）方法，那么线程便会处于该对象的
**等待池**中，等待池中的线程不会去竞争该对象的锁。
当有线程调用了对象的notifyAll（）方法（唤醒所有wait线程）或notify（）方法（只随机唤醒一个wait线程），被唤醒的的线程便会进入该**对象的锁池**中，锁池中的线程会去**竞争该对象锁**。
优先级高的线程竞争到对象锁的概率大，假若某线程没有竞争到该对象锁，它还会留在锁池中，唯有线程再次调用wait（）方法，它才会重新回到等待池中。而竞争到对象锁的线程则继续往下执行，直到执行完了synchronized代码块，它会释放掉该对象锁，这时锁池中的线程会继续竞争该对象锁。

##  wait和sleep区别

### 共同点： 
1. 他们都是在多线程的环境下，都可以在程序的调用处**阻塞指定的毫秒数，并返回** 
2. wait()和sleep()都可以通过interrupt()方法 打断线程的暂停状态 ，从而使线程立刻抛出InterruptedException。 （不建议使用该方法）
- 如果线程A希望立即结束线程B，则可以对线程B对应的Thread实例调用interrupt方法。如果此刻线程B正在wait/sleep /join，则线程B会立刻抛出InterruptedException，在catch() {} 中直接return即可安全地结束线程。 
- 需要注意的是，InterruptedException是线程自己从内部抛出的，并不是interrupt()方法抛出的。对某一线程调用 interrupt()时，如果该线程正在执行普通的代码，那么该线程根本就不会抛出InterruptedException。但是，一旦该线程进入到 wait()/sleep()/join()后，就会立刻抛出InterruptedException 。 

### 不同点： 
1. 所属的类
Thread类的方法：sleep(),yield()等 
Object的方法：wait()和notify()等 
2.  是否释放锁
**每个对象都有一个锁来控制同步访问。Synchronized关键字可以和对象的锁交互，来实现线程的同步**。 
**sleep方法没有释放锁，而wait方法释放了锁**，使得其他线程可以使用同步控制块或者方法。 
3.  使用限制
wait，notify和notifyAll只能在同步控制方法或者同步控制块里面使用，而sleep可以在任何地方使用 。

## sleep()和yield()的区别
- sleep 方法使当前运行中的线程睡眼一段时间，进入不可运行状态，**这段时间的长短是由程序设定的**，yield 方法使当前线程让出 CPU 占有权，但让出的时间是**不可设定的**。实际上，yield()方法对应了如下操作：先检测当前是否有相同优先级的线程处于同可运行状态，如有，则把 CPU  的占有权交给此线程，否则，继续运行原来的线程。所以yield()方法称为“退让”，它把运行机会让给了同等优先级的其他线程
- 另外，sleep 方法允许较低优先级的线程获得运行机会，但 yield()  方法执行时，当前线程仍处在可运行状态，所以，不可能让出较低优先级的线程些时获得 CPU 占有权。在一个运行系统中，如果较高优先级的线程没有调用 sleep 方法，又没有受到 I\O 阻塞，那么，较低优先级线程只能等待所有较高优先级的线程运行结束，才有机会运行。

# 三线程打印ABC

## 要求
java实现三个线程A B C：A线程打印10次A，B线程打印10次B,C线程打印10次C，要求线程同时运行，交替打印10次ABC。

## 方法一.使用synchronized() + wait(),nitify()
- 题意
问题为三线程间的同步唤醒操作，主要的目的就是ThreadA->ThreadB->ThreadC→ThreadA……循环执行三个线程。为了控制线程执行的顺序，那么就必须要**确定唤醒、等待的顺序**，所以每一个线程必须同时持有两个对象锁，才能继续执行。一个对象锁是prev，就是前一个线程所持有的对象锁。还有一个就是自身对象锁。
- 思路
主要的思想就是，为了控制执行的顺序，必须要先持有prev锁，也就是前一个线程要释放自身对象锁，再去申请自身对象锁，两者兼备时打印字母，之后首先调用self.notifyAll()释放自身对象锁，唤醒下一个等待线程，再调用prev.wait()释放prev对象锁，终止当前线程，等待循环结束后再次被唤醒。

程序运行的主要过程就是
A线程最先运行，持有C,A对象锁，后释放A,C锁，唤醒B；
线程B等待A锁，再申请B锁，后打印B，再释放B,A锁，唤醒C；
线程C等待B锁，再申请C锁，后打印C，再释放C,B锁，唤醒A……

- 注意
为了避免JVM启动ThreadA、ThreadB、ThreadC三个线程顺序的不确定性。需要让A,B,C三个线程以确定的顺序启动，中间加一段`sleep()`**确保前一个线程已启动**。

### 代码实现

```java
public class ABC {
	public static class ThreadPrinter implements Runnable {
		private String name;
		private Object prev;//前一个对象的锁
		private Object self;//自己的锁

		private ThreadPrinter(String name, Object prev, Object self) {
			this.name = name;
			this.prev = prev;
			this.self = self;
		}

		@Override
		public void run() {
			int count = 10;
			while (count > 0) {// 多线程并发，不能用if，必须用循环测试等待条件，避免虚假唤醒
				synchronized (prev) { // 先获取 prev 锁
					synchronized (self) {// 再获取 self 锁
						System.out.print(name);
						count--;
						self.notifyAll();// 先释放 self，唤醒其他线程竞争self锁
					}
					try {
						prev.wait(); // 再释放 prev，休眠等待唤醒
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
			}
		}
	}

	public static void main(String[] args) throws Exception {
		Object a = new Object();
		Object b = new Object();
		Object c = new Object();
		ThreadPrinter pa = new ThreadPrinter("A", c, a);
		ThreadPrinter pb = new ThreadPrinter("B", a, b);
		ThreadPrinter pc = new ThreadPrinter("C", b, c);

		new Thread(pa).start();
		Thread.sleep(10);
		new Thread(pb).start();
		Thread.sleep(10);
		new Thread(pc).start();
		Thread.sleep(10);
	}
}
```

## 输出
![打印结果](http://upload-images.jianshu.io/upload_images/11861611-51be4bd0c5ff8705.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 方法二. 使用Lock

### 代码实现

```java
import java.util.concurrent.locks.*;

public class ABC_Lock {
    private static Lock lock = new ReentrantLock();//通过Lock锁来保证线程的访问的互斥
    private static int state = 0;//控制ABC的打印语句是否执行，获取Lock之后的判断
 
    static class ThreadA extends Thread {
        @Override
        public void run() {
        	int count = 10;
            while(count > 0) {
                try {
                    lock.lock();
                    while (state % 3 == 0) {//多线程并发，不能用if，必须用循环测试等待条件，避免虚假唤醒
                        System.out.print("A");
                        state++;
                        count--;
                    }
                } finally {
                    lock.unlock();// lock()和unlock()操作结合try/catch使用
                }
            }
        }
    }
 
    static class ThreadB extends Thread {
        @Override
        public void run() {
        	int count = 10;
            while(count > 0) {
                try {
                    lock.lock();
                    while (state % 3 == 1) {//多线程并发，不能用if，必须用循环测试等待条件，避免虚假唤醒
                        System.out.print("B");
                        state++;
                        count--;
                    }
                } finally {
                    lock.unlock();// lock()和unlock()操作结合try/catch使用
                }
            }
        }
    }
 
    static class ThreadC extends Thread {
        @Override
        public void run() {
        	int count = 10;
            while(count > 0){
                try {
                    lock.lock();
                    while (state % 3 == 2) {//多线程并发，不能用if，必须用循环测试等待条件，避免虚假唤醒
                        System.out.print("C");
                        state++;
                        count--;
                    }
                } finally {
                    lock.unlock();// lock()和unlock()操作结合try/catch使用
                }
            }
        }
    }
 
    public static void main(String[] args) {
        new ThreadA().start();
        new ThreadB().start();
        new ThreadC().start();
    }
}
```

## 输出
![打印结果](http://upload-images.jianshu.io/upload_images/11861611-51be4bd0c5ff8705.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

参考文章
[并发编程的优缺点](https://juejin.im/post/5ae6c3ef6fb9a07ab508ac85)
[java多线程入门学习（一）](https://blog.csdn.net/sunfc_nbu/article/details/51133477)
[Java多线程学习（吐血超详细总结）](https://blog.csdn.net/evankaka/article/details/44153709)
[oracle官方文档](https://docs.oracle.com/javase/8/docs/api/)