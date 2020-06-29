# CLH同步队列

## 1. 简介

CLH 同步队列是一个 FIFO **双向**队列，AQS 依赖它来完成同步状态的管理：

- 当前线程如果获取同步状态失败时，AQS则会将当前线程已经等待状态等信息构造成一个节点（Node）并将其加入到CLH同步队列，同时会阻塞当前线程
- 当同步状态释放时，会把首节点唤醒（公平锁），使其再次尝试获取同步状态。

## 2. Node

在 CLH 同步队列中，**一个节点（Node），表示一个线程**，它保存着线程的引用（`thread`）、状态（`waitStatus`）、前驱节点（`prev`）、后继节点（`next`）。其定义如下：

> Node 是 AbstractQueuedSynchronizer 的内部静态类。

~~~java
static final class Node {

    // 共享
    static final Node SHARED = new Node();
    // 独占
    static final Node EXCLUSIVE = null;

    /**
     * 因为超时或者中断，节点会被设置为取消状态，被取消的节点时不会参与到竞争中的，他会一直保持取消状态不会转变为其他状态
     */
    static final int CANCELLED =  1;
    /**
     * 后继节点的线程处于等待状态，而当前节点的线程如果释放了同步状态或者被取消，将会通知后继节点，使后继节点的线程得以运行
     */
    static final int SIGNAL    = -1;
    /**
     * 节点在等待队列中，节点线程等待在Condition上，当其他线程对Condition调用了signal()后，该节点将会从等待队列中转移到同步队列中，加入到同步状态的获取中
     */
    static final int CONDITION = -2;
    /**
     * 表示下一次共享式同步状态获取，将会无条件地传播下去
     */
    static final int PROPAGATE = -3;

    /** 等待状态 */
    volatile int waitStatus;

    /** 前驱节点，当节点添加到同步队列时被设置（尾部添加） */
    volatile Node prev;

    /** 后继节点 */
    volatile Node next;

    /** 等待队列中的后续节点。如果当前节点是共享的，那么字段将是一个 SHARED 常量，也就是说节点类型（独占和共享）和等待队列中的后续节点共用同一个字段 */
    Node nextWaiter;
    
    /** 获取同步状态的线程 */
    volatile Thread thread;

    final boolean isShared() {
        return nextWaiter == SHARED;
    }

    final Node predecessor() throws NullPointerException {
        Node p = prev;
        if (p == null)
            throw new NullPointerException();
        else
            return p;
    }

    Node() { // Used to establish initial head or SHARED marker
    }

    Node(Thread thread, Node mode) { // Used by addWaiter
        this.nextWaiter = mode;
        this.thread = thread;
    }

    Node(Thread thread, int waitStatus) { // Used by Condition
        this.waitStatus = waitStatus;
        this.thread = thread;
    }
    
}
~~~

- `thread` 字段，Node 节点对应的**线程 Thread** 。
- `nextWaiter` 字段，Node 节点获取同步状态的**模型( Mode )**。`#tryAcquire(int args)` 和 `#tryAcquireShared(int args)` 方法，分别是**独占式**和**共享式**获取同步状态。在获取失败时，它们**都会**调用 `#addWaiter(Node mode)` 方法**入队**。而 `nextWaiter` 就是用来表示是哪种模式：
  - `SHARED` **静态 + 不可变**字段，枚举**共享**模式。
  - `EXCLUSIVE` **静态 + 不可变**字段，枚举**独占**模式。
  - `#isShared()` 方法，判断是否为共享式获取同步状态。
- `#predecessor()` 方法，获得 Node 节点的**前一个** Node 节点。在方法的内部，`Node p = prev` 的本地拷贝，是为了避免并发情况下，`prev` 判断完 `== null` 时，恰好被修改，从而保证线程安全。

**构造方法**有 **3** 个，分别是：

- `#Node()` 方法：用于 `SHARED` 的创建。

- ```
  #Node(Thread thread, Node mode)
  ```

   

  方法：用于

   

  ```
  #addWaiter(Node mode)
  ```

   

  方法。

  - 从 `mode` 方法参数中，我们也可以看出它代表获取同步状态的**模式**。
  - 在本文中，我们会看到这个构造方法的使用。

- ```
  #Node(Thread thread, int waitStatus)
  ```

   

  方法，用于

   

  ```
  #addConditionWaiter()
  ```

   

  方法。

  - 在本文中，不会使用，所以解释暂时省略。