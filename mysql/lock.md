# lock

- 全局锁：对全局整个库进行加锁  mysql提供 FTWRL(flush tables with read lock) 命令全局添加读锁，添加锁后，整个数据库，处于只读状态

   进行数据备份时候，非innodb的引擎需要采用FTWRL来进行备份，因为只有这样的操作才能保证全局的备份是处于同一个逻辑时间点 ;innodb的备份，开始一个隔离级别为可重复读的事务来进行备份

- 表锁   ：锁住整个表，

- 意向锁

- 元数据锁（meta data lock）MDL  

   #### 二、元数据锁

   元数据锁属于表锁范畴，元数据即数据字典信息

   - MDL 不需要显式使用，在访问一个表的时候会被自动加上
   - MDL的作用是维护数据的一致性

   ##### 为什么需要元数据锁

   线程A查询数据库，线程B对表结构进行变更，比如说删除某一列，修改列名，那么查询结果和数据库表结构对应不上，这就冲突了

   - MDL锁不需要显示使用，在访问表结构的时候被自动加上
   - **对表做增删改查操作，加MDL读锁，对表结构变更的时候加MDL写锁**
   - 兼容性和正常行锁读写锁保持相同，读读兼容，读写，写写不兼容

- 行锁

- 间隙锁