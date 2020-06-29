# AQS

## 1、源码分析

~~~java
  public ReentrantLock() {
        sync = new NonfairSync();
    }
 public void lock() {
        sync.lock();
    }
/**
         * Performs lock.  Try immediate barge, backing up to normal
         * acquire on failure.
         */
        final void lock() {
            if (compareAndSetState(0, 1))
                setExclusiveOwnerThread(Thread.currentThread());
            else
                acquire(1);
        }

 protected final boolean compareAndSetState(int expect, int update) {
        // See below for intrinsics setup to support this
        return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
    }
~~~

~~~java
shuo.laoma.dynamic.c84.SimpleMapperDemo$Student
[java.lang.String shuo.laoma.dynamic.c84.SimpleMapperDemo$Student.name, 
 int shuo.laoma.dynamic.c84.SimpleMapperDemo$Student.age, 
 java.lang.Double shuo.laoma.dynamic.c84.SimpleMapperDemo$Student.score]
shuo.laoma.dynamic.c84.SimpleMapperDemo$Student
name=张三
age=18
score=89.0
~~~

