# sychronized

`synchronized` 可以保证方法或者代码块在运行时，同一时刻只有一个方法可以进入到临界区，同时它还可以保证共享变  量的内存可见性

- 修饰范围

   1、实例方法

  修饰在方法之前，本质是作用在this对象上，每个对象都会包含锁对象和等待队列，锁只能被一个对象所持有，当获取锁失败，被放到等待队列，当前线程状态设置为BLOCKING,释放锁时会通知等待队列中任意，不保证公平，由于sychronized可重入，能够进入被同一个所对象保护的其他方法

   2、静态方法

  修饰在静态方法之前，实际上是作用在class对象。

   3、代码块

- 可重入性

- 内存可见性

  sychronized能够保证释放锁时的操作结果都保存到内存，获取锁后，所有的操作都从内存获取，单纯为了解决内存可见性问题，可以使用volatile。

- 死锁

  为了防止死锁，保证多个线程获取同一个资源时的顺序是一致的

- 原理

   修饰代码

  编译后的class文件中，代码块会被monitorenter和monitorexit包裹，开始执行到monitorenter时会去获取 monitor(存在对象头中)，获取成功后，锁计数器+1，执行到monitorexit，锁计数器-1，当为0时释放锁对象

  修饰方法

  修饰方法时，编译后的代码，会带有ACC_SYCHONIZED标识符，代表当前方法为是一个同步方法

-   monitor

    每个对象都会有与之相关的monitor对象，在java中体现为ObjectMonitor对象，开始执行到monitorenter时会去获取锁对象的ObjectMonitor(在重量级锁的情况下，如果不是重量级锁，不会尝试获取)。ObjectMonitor中有以下几个重要变量：

  + _owner: 指向持有ObjectMonitor对象的线程
    + _WaitSet: 等待队列
    + _EntryList: 阻塞队列
    + _recursions: 记录持有ObjectMonitor对象线程获取锁的次数
    + _count: 

- 优缺点

  - `synchronized`的获取和释放锁由JVM实现，用户不需要显示的释放锁，非常方便。

  - 然而，

    ```
    synchronized
    ```

     

    也有一定的局限性。

    - 当线程尝试获取锁的时候，如果获取不到锁会一直阻塞。
    - 如果获取锁的线程进入休眠或者阻塞，除非当前线程异常，否则其他线程尝试获取锁必须一直等待。

- 锁升级及其优化

  

  http://www.iocoder.cn/JUC/sike/synchronized/?vip