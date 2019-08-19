# ThreadPool

## 元素

### Task

提交到线程池中任务的抽象，体现为 Runnable 接口，或者 Callable 接口。

### State 

- RUNNING

  接受新任务，也在处理队列中的任务

- SHUTDOWN

  不接受新任务，也处理队列中的任务

- STOP

  不接受新任务，不处理队列中的任务

- TIDYING

  所有的任务都终止，worker 线程为 0，是走向 TERMINATED 的过度状态

- TERMINATED

  terminated 方法已经完成。

五种状态的转化关系如图所示：





从以上状态可以看出：只有处于 RUNNING 状态时，才可以处理新提交的任务，其它状态下的区别就是处不处理队列中积压的任务而已。

### Queue

任务队列。Task 会排到任务队列中，Worker 线程从队列中循环取任务并且执行。

### Worker

顾名思义，就是在线程池中负责“干活”的。他们的任务上面已经提到了。他们的数量会根据参数的不同设置而定。

## Task 如何提交

每一个任务以 Runnable 或者 Callable 的形式提交。

1. 如果线程池中的 worker 线程数少于 `corePoolSize`，start 一个新 Thread，并将此任务作为该线程的第一个任务。

2. 如果线程池中的 worker 线程数大于等于 `corePoolSize`，就将该任务排到阻塞队列中

    1. 排队成功

       等待 worker 线程从队列中取任务执行

    2. 排队失败

       1. 池中总线程数小于 `maximumPoolSize`

          start 一个新 Thread，并将该任务作为该线程的第一个任务

       2. 池中总线程数大于等于 `maximumPoolSize`

          执行拒绝策略



实现部分：

```Java
public void execute(Runnable command) {
    if (command == null)
        throw new NullPointerException();
    int c = ctl.get();
    if (workerCountOf(c) < corePoolSize) {
        if (addWorker(command, true))
            return;
        c = ctl.get();
    }
    if (isRunning(c) && workQueue.offer(command)) {
        int recheck = ctl.get();
        if (! isRunning(recheck) && remove(command))
            reject(command);
        else if (workerCountOf(recheck) == 0)
            addWorker(null, false);
    }
    else if (!addWorker(command, false))
        reject(command);
}
```







## 线程 keepalive

## shutdown

