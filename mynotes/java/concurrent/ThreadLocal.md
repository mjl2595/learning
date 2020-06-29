# ThreadLocal

- 线程本地变量

每个线程都有变量的独有拷贝

- 使用场景：多线程环境下，共享一个变量，要求对当前变量需要有不同的值，就需要用到ThreadLocal,可以理解为线程安全的一种手段

  比如你可以用 ThreadLocal 让 SimpleDateFormat 变成线程安全的，因为那个类创建代价高昂且每次调用都需要创建不同的实例所以不值得在局部范围使用它，如果为每个线程提供一个自己独有的变量拷贝，将大大提高效率。

  - 首先，通过复用减少了代价高昂的对象的创建个数。
  - 其次，你在没有使用高代价的同步或者不变性的情况下获得了线程安全。

- 原理

本质每个线程下都有一个ThreadLocalMap,本质是个Map,key用来存储当前ThreadLocal对象，值就是需要设置的值，读取就是根据当前ThreadLocal的this对象获取

- 内存泄露：因为ThreadLocalMap的key采用的弱引用，value采用的强引用，可能出现value永远不会被GC回收，所以ThreadLocal用完记得调用remove方法