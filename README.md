# 线程池使用
```java
public class ThreadPoolExecutorDemo {
    public static void main(String[] args) {

        ThreadPoolExecutor pool = new ThreadPoolExecutor(  //创建一个线程池
                1,  //线程数
                2,  //扩容线程最大数
                4000,   //扩容线程存活时间
                TimeUnit.MILLISECONDS,  //存活时间的单位（毫秒）
                new LinkedBlockingQueue<>(1),   //队列 [长度为1]（没参数则是~27亿）
                new ThreadPoolExecutor.AbortPolicy()    //拒绝策略 [是当队列满了就拒绝，抛出RejectedExecutionException异常 ]（队列满了之后则使用这里的拒绝策略）
        );
        for (int i = 0; i < 20; i++) {
            Runnable task = new Task("Task"+i);
            try {
                pool.execute(task);
            } catch (RejectedExecutionException e) {
                synchronized (Object.class){
                    System.out.println("队伍满载拒绝接收");
                }

            }
        }
        pool.shutdown();

    }
}

class Task implements Runnable {
    private String name;

    public Task(String name) {
        this.name = name;
    }

    @Override
    public void run() {
        System.out.println("Thread  " + Thread.currentThread().getName() + " 开始 " + name);
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        System.out.println("Thread  " + Thread.currentThread().getName() + " 结束 " + name);
    }
}
```
```java
        for (int i = 0; i < 20; i++) {
            Runnable task = new Task("Task"+i);
            try {
                pool.execute(task);
            } catch (RejectedExecutionException e) {
                synchronized (Object.class){
                    System.out.println("队伍满载拒绝接收");
                }

            }
        }
```
这段代码就是循环20次创建使用线程，线程池当使用.execute()方法的时候就会执行线程，在新线程中睡眠了2秒在这两秒的时间足以让20次循环结束，创建了20个线程在pool线程池中，但是设置线程池的线程数量为1，最大为2
线程池的经历：↓
1. 线程0开始执行 > 睡眠2秒（线程数：1；队列：0；扩容线程数：0）
2. 线程1想开始，但是线程数满了，就存放在队列中 > 等待（线程数：1；队列：1；扩容线程数：0）
3. 线程2时线程数满了，队列也满了就在扩容线程执行 > 睡眠2秒（线程数：1；队列：1；扩容线程数：1）
4. 线程3 ~ 线程19因为线程数、队列和扩容线程数都到了设定的容量，那就使用拒绝策略处理这些，代码中的是直接拒绝 > 抛出异常
