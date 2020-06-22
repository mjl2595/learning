## ArrayList

+ 存储结构

  ```java
  transient Object[] elementData; //实际存储元素的数组
  private int size;//包含了多少个元素
  ```

  

+ 扩容策略

  ```java
  private void grow(int minCapacity) {
          int oldCapacity = elementData.length;
          int newCapacity = oldCapacity + (oldCapacity >> 1); //右移一位等于除以二，所以是1.5倍扩容
          if (newCapacity - minCapacity < 0)
              newCapacity = minCapacity;//扩容后，还是小于需要的长度，就按照需要的长度来扩容
          if (newCapacity - MAX_ARRAY_SIZE > 0) //MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
              newCapacity = hugeCapacity(minCapacity);//这里的hugeCapacity方法是为了防止整数溢出
          // minCapacity is usually close to size, so this is a win:
          elementData = Arrays.copyOf(elementData, newCapacity);//通过复制到一个新的数组
      }
  ```

  

+ 迭代

  1. 增强for循，本质是被编译成使用iterator
  2. 通过listIterator()方法可以获得一个能向前向后循环的ListIterator

+ 特点

  1. 随机访问，效率为O(1)，因为是访问数组
  2. 根据内容查询效率较低，为O(N)，因为是遍历查找
  3. 插入和删除效率较低，为O(N)，因为需要移动元素
  4. 线程不安全