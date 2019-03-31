---
title: Java并发
date: 2019.3.29
---

## Runnable接口

线程可以驱动任务，Runnable接口是一种用来描述任务的方式。要想定义任务，只需要实现Runnable接口并编写run()方法，使得该任务可以执行你的命令。
```Java
class LiftOff implements Runnable {

    protected  int countDown = 10;
    private static int taskCount = 0;
    private final int id = taskCount++;

    public LiftOff() {}

    public LiftOff(int countDown) {
        this.countDown = countDown;
    }

    public String status() {
        return "#" + id + "(" + (countDown > 0 ? countDown : "LiftOff!") + "), ";
    }

    @Override
    public void run() {
        while (countDown-- > 0) {
            System.out.print(status());
            // Thread.yield();
        }
    }
}
```
任务的run()方法通常总会有某种形式的循环，使得任务一直运行下去直到不再需要，所以要设定跳出循环的条件（有一种选择是直接从run()返回）。通常，run()被写成无限循环的形式，这就意味着，除非有某个条件使得run()终止，否则它将永远运行下去。

在run()中对静态方法Thread.yield()的调用是对线程调度器的一种建议，它在声明：“我已经执行完成生命周期中最重要的部分，此刻正是切换给其他任务执行一段时间的大好时机。”这完全是选择性的。

## Thread类

将Runnable对象转变为工作任务的传统方式是把它提交给一个Thread构造器：
```Java
public class BasictThreads {

    public static void main(String[] args) {

        Thread t = new Thread(new LiftOff());
        t.start();
        System.out.println("Waiting for LiftOff");
    }
}
```
Thread构造器只需要一个Runnable对象。调用Thread对象的start()方法为该线程执行必需的初始化操作，然后调用Runnable的run()方法，以便在这个新线程中启动该任务。代码中，产生的是对LiftOff.run()的方法调用，由于LiftOff.run()是由不同的线程执行的，因此你仍旧可以执行main()线程中的其他操作（任何线程都可以启动另一个线程）。因此，程序会同时运行两个方法，main()和LiftOff.run()是程序中与其他线程“同时”执行的代码。

当main()创建Thread对象时，它并没有捕获任何对这些对象的引用。每个Thread都“注册”了它自己，因此确实有一个对它的引用，而且在它的任务退出其run()并死亡之前，垃圾回收器无法清除它。一个线程会创建一个单独的执行线程，在对start()的调用完成之后，它仍旧会继续存在。

## Executor

Executor将为你管理Thread对象，从而简化了并发编程。Executor在客户端和任务执行之间提供了一个间接层；与客户端直接执行任务不同，这个中介对象将执行任务。Executor允许你管理异步任务的执行，无须显式管理线程的生命周期。

我们可以使用Executor来代替Thread对象。LiftOff对象知道如何运行具体的任务，与命令设计模式一样，它暴露了要执行的单一方法。ExecutorService（具有服务生命周期的Executor）知道如何构建恰当的上下午来执行Runnable对象。CachedThreadPool将为每个任务都创建一个线程。ExecutorService对象是使用静态的Executors方法创建的，这个方法可以确定其Executor类型：
```Java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class CachedThreadPool {

    public static void main(String[] args) {
        ExecutorService exec = Executors.newCachedThreadPool();
        for (int i = 0; i < 5; i++) {
            exec.execute(new LiftOff());
        }
        exec.shutdown();
    }
}
```
常见的情况是，单个Executor被用来创建和管理系统中所有的任务。对shutdown()方法的调用可以防止新任务被提交给这个Executor，当前线程将继续运行在shutdown()被调用之前提交的所有任务。这个程序将在Executor中的所有任务完成之后尽快退出。

同时，也可以将CachedThreadPool替换为不同类型的Executor。FixedThreadPool使用了有限的线程集来执行所提交的任务：
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class FixedThreadPool {

    public static void main(String[] args) {
        ExecutorService exec = Executors.newFixedThreadPool(5);
        for (int i = 0; i < 5; i++) {
            exec.execute(new LiftOff());
        }
        exec.shutdown();
    }
}
```
FixedThreadPool可以一次性预先执行代价昂贵的线程分配，因此也就可以限制线程的数量了。这可以节省时间，因为不用为每个任务都固定地付出创建线程的开销。在事件驱动的系统中，需要线程的事件处理器，通过直接从池中获取线程，也可以尽快得到服务。使用FixedThreadPool可以避免滥用可获得的资源，因为使用的Thread对象是有界的。

在任何线程池中，现有线程在可能的情况下，都会被自动复用。CachedThreadPool在程序执行过程中通常会创建与所需数量相同的线程，然后在它回收旧线程时停止创建新线程，因此它是合理的Executor的首选。只有当这种方式会引发问题时，你才需要切换到FixedThreadPool。

SingleThreadExecutor就像是线程数量为1的FixedThreadPool。这对于你希望在另一个线程中连续运行的任何事物（长期存活的任务）都是很有用的，例如监听进入的套接字连接的任务。它对于希望在线程中运行的短任务也同样很方便，例如，更新本地或远程日志的小任务，或者是事件分发线程。

如果向SingleThreadExecutor提交了多个任务，那么这些任务将排队，每个任务都会在下一个任务开始之前运行结束，所有的任务将使用相同的线程。SingleThreadExecutor会序列化所有提交给它的任务，并会维护它自己（隐藏）的悬挂任务队列。
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class SingleThreadExecutor {

    public static void main(String[] args) {
        ExecutorService exec = Executors.newSingleThreadExecutor();
        for (int i = 0; i < 5; i++) {
            exec.execute(new LiftOff());
        }
        exec.shutdown();
    }
}
```
假如你有大量的线程，那它们运行的任务将使用文件系统。你可以用SingleThreadExecutor来运行这些线程，以确保任意时刻在任何线程中都只有唯一的任务在运行。在这种方式中，你不需要在共享资源上处理同步（同时不会过度使用文件系统）。有时更好的解决方案是在资源上同步，但是SingleThreadExecutor可以让你省去只是为了维持某些事物的原型而进行的各种协调努力。通过序列化任务，你可以消除对序列化对象的需求。


参考：《Java编程思想》
