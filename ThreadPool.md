# 一、线程池理解

## 1.1 线程池

线程池涉及以下几个概念：

* 核心线程数(corePoolSize)：即初始可调用线程数目
* 等待队列(BlockingQueue<Runnable>)：当线程池中的线程都被占用时，将后续的业务放入等待队列
* 线程池最大容量(maximumPoolSize)：当线程池中的线程都被占用，并且等待队列也都被占用，可以创建新的线程处理后续业务。
* 线程存活时间(keepAliveTime)：当一定时间没有业务调用线程，则杀死多余线程。
* 存活时间单位(TimeUnit unit)
* 线程工厂(ThreadFactory)
* 拒绝业务处理方式(RejectedExecutionHandler)：当业务所需线程数超过线程池最大容量时，如何处理后续业务。

```java
public class ThreadPool {
    public static void main(String[] args) {
        // 新建一个ThreadPoolExecutor，传入核心线程数、线程池最大容量、线程存活时间、
        // 等待队列大小、Executors.defaultThreadFactory()和线程数超出最大容量的处理方式
        // 结果由executorService接收
        ExecutorService executorService = new ThreadPoolExecutor(3, 5, 1L, TimeUnit.SECONDS, new ArrayBlockingQueue<>(3), Executors.defaultThreadFactory(), new ThreadPoolExecutor.AbortPolicy());
        // 测试线程池处理并发业务
        for (int i = 0; i < 9; i++) {
            // 使用lmbda表达式，输出当前线程
            executorService.execute(() ->{
                System.out.println(Thread.currentThread()+"==>在办理业务");
            });
        }
    }
}
```

