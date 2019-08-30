# Spring 中的事务

基本原则：

> 事务管理的关注点和数据访问的关注点相分离。





PlatformTransactionManager 顶层抽象接口



transaction object 事务的必要信息

TransactionSynchronization 注册到事务的回调接口，类似事务处理的监听器

TransactionSynchronizationManager 管理TransactionSynchronization、当前的事务状态以及具体的事务资源







AbstractPlatformTransactionManager 提供了默认实现模板，封装了固定的事务处理逻辑，只将与事务资源相关的操作以protected或者abstract方法形式留给子类实现。

1. 判断当前是否存在事务
2. 根据TransactionDefinition中指定的传播行为的不同语义执行后面的逻辑
3. 根据情况挂起或者恢复事务
4. 提交事务之前检查 readonly 字段是否设置，如果已经设置，以事务的回滚代替事务的提交
5. 事务回滚情况下，清理并恢复事务状态
6. 如果事务的 Synchronization 处于 active 状态，在指定点触发其回调接口





