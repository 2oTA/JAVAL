# 线程池使用
```
ThreadPoolExecutor pool = new ThreadPoolExecutor(
        1,  //线程数
        2,  //扩容线程最大数
        4000,   //扩容线程存活时间
        TimeUnit.MILLISECONDS,  //存活时间的单位（毫秒）
        new LinkedBlockingQueue<>(1),   //队列 [长度为1]（没参数则是~27亿）
        new ThreadPoolExecutor.AbortPolicy()    //拒绝策略 [是当队列满了就拒绝，抛出RejectedExecutionException异常 ]（队列满了之后则使用这里的拒绝策略）
);
```
