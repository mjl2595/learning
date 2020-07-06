# lock

- 全局锁：对全局整个库进行加锁  mysql提供 FTWRL(flush tables with read lock) 命令全局添加读锁，添加锁后，整个数据库，处于只读状态

   进行数据备份时候，非innodb的引擎需要采用FTWRL来进行备份，因为只有这样的操作才能保证全局的备份是处于同一个逻辑时间点 ;innodb的备份，开始一个隔离级别为可重复读的事务来进行备份

- 表锁   ：锁住整个表，

- 元数据锁（meta data lock）MDL  

- 行锁

- 间隙锁