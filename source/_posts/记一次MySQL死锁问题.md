---
title: 记一次MySQL死锁问题
date: 2018-07-07 15:35:57
tags: MySQL
---
1. 登陆MySQL 
```sql
>mysql -u root -p 
```
2.执行 show engine innodb status \G;
```sql
*************************** 1. row ***************************
  Type: InnoDB
  Name: 
Status: 
=====================================
2018-07-07 14:15:27 7ff21aefb700 INNODB MONITOR OUTPUT
=====================================
Per second averages calculated from the last 18 seconds
-----------------
BACKGROUND THREAD
-----------------
srv_master_thread loops: 1026480 srv_active, 0 srv_shutdown, 7269604 srv_idle
srv_master_thread log flush and writes: 7531464
----------
SEMAPHORES
----------
OS WAIT ARRAY INFO: reservation count 1061681
OS WAIT ARRAY INFO: signal count 1022743
Mutex spin waits 409630, rounds 2859361, OS waits 83143
RW-shared spins 986199, rounds 29173618, OS waits 969842
RW-excl spins 12534, rounds 393908, OS waits 7684
Spin rounds per wait: 6.98 mutex, 29.58 RW-shared, 31.43 RW-excl
------------------------
LATEST DETECTED DEADLOCK
------------------------
2018-07-07 14:15:02 7ff21a1c7700
*** (1) TRANSACTION:
TRANSACTION 111780444, ACTIVE 12 sec inserting
mysql tables in use 1, locked 1
LOCK WAIT 5 lock struct(s), heap size 1184, 3 row lock(s), undo log entries 1
MySQL thread id 62038, OS thread handle 0x7ff21b491700, query id 117199921 218.18.156.246 root update
INSERT IGNORE INTO t_account_yxt_ecp (bid,uid,ecp,yxt,batch,`day`,ctime)
        VALUES
          
            ('yw',107072,0.6863,0.00000020,'892332',20180705,now())
         , 
            ('yw',107440,1.4310,0.00000054,'892332',20180705,now())
         , 
            ('yw',109510,0.1000,0.00000005,'892332',20180705,now())
         , 
            ('yw',105845,0.1000,0.00000003,'892332',20180705,now())
         , 
            ('yw',105782,9999.9991,0.00374499,'892332',20180705,now())
         , 
            ('yw',107050,1.5410,0.00000034,'892332',20180705,now())
         , 
            ('yw',107386,33.3100,0.00001114,'892332',20180705,now())
         , 
            ('yw',107322,0.1000,0.00000002,'892332',20180705,now())
         , 
            ('yw',110046,0.2420,0.00000009,'892332',20180705,now())
         , 
            ('yw',110072,100.3447,0.00003163,'892332',20180705,now())
         , 
            ('yw
*** (1) WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 4122 page no 4 n bits 144 index `bid` of table `yunzz_agent`.`t_account_yxt_ecp` trx id 111780444 lock_mode X locks gap before rec insert intention waiting
Record lock, heap no 44 PHYSICAL RECORD: n_fields 4; compact format; info bits 0
 0: len 2; hex 7977; asc yw;;
 1: len 4; hex 8133eee1; asc  3  ;;
 2: len 4; hex 8001ee51; asc    Q;;
 3: len 4; hex 0000032a; asc    *;;

*** (2) TRANSACTION:
TRANSACTION 111780445, ACTIVE 11 sec inserting
mysql tables in use 1, locked 1
5 lock struct(s), heap size 1184, 3 row lock(s), undo log entries 1
MySQL thread id 62000, OS thread handle 0x7ff21a1c7700, query id 117199924 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root update
INSERT IGNORE INTO t_account_yxt_ecp (bid,uid,ecp,yxt,batch,`day`,ctime)
        VALUES
          
            ('yw',107072,0.6863,0.00000020,'936595',20180705,now())
         , 
            ('yw',107440,1.4310,0.00000054,'936595',20180705,now())
         , 
            ('yw',109510,0.1000,0.00000005,'936595',20180705,now())
         , 
            ('yw',105845,0.1000,0.00000003,'936595',20180705,now())
         , 
            ('yw',105782,9999.9991,0.00374499,'936595',20180705,now())
         , 
            ('yw',107050,1.5410,0.00000034,'936595',20180705,now())
         , 
            ('yw',107386,33.3100,0.00001114,'936595',20180705,now())
         , 
            ('yw',107322,0.1000,0.00000002,'936595',20180705,now())
         , 
            ('yw',110046,0.2420,0.00000009,'936595',20180705,now())
         , 
            ('yw',110072,100.3447,0.00003163,'936595',20180705,now())
     
*** (2) HOLDS THE LOCK(S):
RECORD LOCKS space id 4122 page no 4 n bits 144 index `bid` of table `yunzz_agent`.`t_account_yxt_ecp` trx id 111780445 lock mode S locks gap before rec
Record lock, heap no 44 PHYSICAL RECORD: n_fields 4; compact format; info bits 0
 0: len 2; hex 7977; asc yw;;
 1: len 4; hex 8133eee1; asc  3  ;;
 2: len 4; hex 8001ee51; asc    Q;;
 3: len 4; hex 0000032a; asc    *;;

*** (2) WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 4122 page no 4 n bits 144 index `bid` of table `yunzz_agent`.`t_account_yxt_ecp` trx id 111780445 lock_mode X locks gap before rec insert intention waiting
Record lock, heap no 44 PHYSICAL RECORD: n_fields 4; compact format; info bits 0
 0: len 2; hex 7977; asc yw;;
 1: len 4; hex 8133eee1; asc  3  ;;
 2: len 4; hex 8001ee51; asc    Q;;
 3: len 4; hex 0000032a; asc    *;;

*** WE ROLL BACK TRANSACTION (2)
------------
TRANSACTIONS
------------
Trx id counter 111780536
Purge done for trx's n:o < 111780535 undo n:o < 0 state: running but idle
History list length 1010
LIST OF TRANSACTIONS FOR EACH SESSION:
---TRANSACTION 0, not started
MySQL thread id 62105, OS thread handle 0x7ff21aefb700, query id 117200464 localhost root init
show engine innodb status
---TRANSACTION 111780476, not started
MySQL thread id 62104, OS thread handle 0x7ff21b040700, query id 117200100 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111780477, not started
MySQL thread id 62103, OS thread handle 0x7ff21b081700, query id 117200108 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111780455, not started
MySQL thread id 62102, OS thread handle 0x7ff21b7de700, query id 117200015 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111780478, not started
MySQL thread id 62101, OS thread handle 0x7ff21afbe700, query id 117200115 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111780405, not started
MySQL thread id 62100, OS thread handle 0x7ff21b289700, query id 117200462 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111780453, not started
MySQL thread id 62099, OS thread handle 0x7ff21aa28700, query id 117200058 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111780402, not started
MySQL thread id 62095, OS thread handle 0x7ff21ad75700, query id 117200071 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111780475, not started
MySQL thread id 62098, OS thread handle 0x7ff21b248700, query id 117200097 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111779613, not started
MySQL thread id 62087, OS thread handle 0x7ff21a186700, query id 117195138 218.18.156.246 root cleaning up
---TRANSACTION 0, not started
MySQL thread id 62083, OS thread handle 0x7ff21b658700, query id 117195026 218.18.156.246 root cleaning up
---TRANSACTION 111779575, not started
MySQL thread id 62076, OS thread handle 0x7ff21b30b700, query id 117194924 218.18.156.246 root cleaning up
---TRANSACTION 111779456, not started
MySQL thread id 62051, OS thread handle 0x7ff21a820700, query id 117200379 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111779907, not started
MySQL thread id 62073, OS thread handle 0x7ff21ac30700, query id 117200434 218.18.156.246 root cleaning up
---TRANSACTION 111778722, not started
MySQL thread id 62069, OS thread handle 0x7ff21b71b700, query id 117190014 218.18.156.246 root cleaning up
---TRANSACTION 111777860, not started
MySQL thread id 62053, OS thread handle 0x7ff21a7df700, query id 117185336 218.18.156.246 root cleaning up
---TRANSACTION 0, not started
MySQL thread id 62052, OS thread handle 0x7ff21ad34700, query id 117184909 218.18.156.246 root cleaning up
---TRANSACTION 111777848, not started
MySQL thread id 62050, OS thread handle 0x7ff21a4d3700, query id 117185237 218.18.156.246 root cleaning up
---TRANSACTION 0, not started
MySQL thread id 62049, OS thread handle 0x7ff22589f700, query id 117182503 218.18.156.246 root cleaning up
---TRANSACTION 111780530, not started
MySQL thread id 62038, OS thread handle 0x7ff21b491700, query id 117200458 218.18.156.246 root cleaning up
---TRANSACTION 111780509, not started
MySQL thread id 62037, OS thread handle 0x7ff21ab6d700, query id 117200389 218.18.156.246 root cleaning up
---TRANSACTION 111776792, not started
MySQL thread id 62036, OS thread handle 0x7ff2257dc700, query id 117180104 218.18.156.246 root cleaning up
---TRANSACTION 111775519, not started
MySQL thread id 62020, OS thread handle 0x7ff21a861700, query id 117172351 218.18.156.246 root cleaning up
---TRANSACTION 111774683, not started
MySQL thread id 62004, OS thread handle 0x7ff21a208700, query id 117167382 218.18.156.246 root cleaning up
---TRANSACTION 111780522, not started
MySQL thread id 62000, OS thread handle 0x7ff21a1c7700, query id 117200418 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 0, not started
MySQL thread id 61998, OS thread handle 0x7ff21a924700, query id 117166732 218.18.156.246 root cleaning up
---TRANSACTION 111780527, not started
MySQL thread id 61995, OS thread handle 0x7ff21a71c700, query id 117200443 218.18.156.246 root cleaning up
---TRANSACTION 111774043, not started
MySQL thread id 61991, OS thread handle 0x7ff2240ba700, query id 117163134 218.18.156.246 root cleaning up
---TRANSACTION 111774288, not started
MySQL thread id 61990, OS thread handle 0x7ff22585e700, query id 117164677 218.18.156.246 root cleaning up
---TRANSACTION 111780535, not started
MySQL thread id 61910, OS thread handle 0x7ff21a0c3700, query id 117200463 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111780485, not started
MySQL thread id 61905, OS thread handle 0x7ff21aa69700, query id 117200131 218.18.156.246 root cleaning up
---TRANSACTION 111766303, not started
MySQL thread id 61876, OS thread handle 0x7ff21b3ce700, query id 117114984 218.18.156.246 root cleaning up
---TRANSACTION 111764451, not started
MySQL thread id 61873, OS thread handle 0x7ff21a659700, query id 117102360 218.18.156.246 root cleaning up
---TRANSACTION 111764316, not started
MySQL thread id 61870, OS thread handle 0x7ff21af7d700, query id 117101536 218.18.156.246 root cleaning up
---TRANSACTION 0, not started
MySQL thread id 61869, OS thread handle 0x7ff21a75d700, query id 117101534 218.18.156.246 root cleaning up
---TRANSACTION 0, not started
MySQL thread id 61865, OS thread handle 0x7ff21b5d6700, query id 117100991 218.18.156.246 root cleaning up
---TRANSACTION 111763282, not started
MySQL thread id 61845, OS thread handle 0x7ff21abef700, query id 117094925 218.18.156.246 root cleaning up
---TRANSACTION 0, not started
MySQL thread id 61832, OS thread handle 0x7ff2258e0700, query id 117087126 218.18.156.246 root cleaning up
---TRANSACTION 111764411, not started
MySQL thread id 61819, OS thread handle 0x7ff21a965700, query id 117115441 218.18.156.246 root cleaning up
---TRANSACTION 0, not started
MySQL thread id 61820, OS thread handle 0x7ff21acf3700, query id 117115440 218.18.156.246 root cleaning up
---TRANSACTION 111780190, not started
MySQL thread id 61805, OS thread handle 0x7ff21a8e3700, query id 117200447 218.18.156.246 root cleaning up
---TRANSACTION 111780525, not started
MySQL thread id 61594, OS thread handle 0x7ff21aeba700, query id 117200429 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111780479, not started
MySQL thread id 61372, OS thread handle 0x7ff21ab2c700, query id 117200120 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111780482, not started
MySQL thread id 61348, OS thread handle 0x7ff21b617700, query id 117200123 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111780518, not started
MySQL thread id 54732, OS thread handle 0x7ff21b1c6700, query id 117200402 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
---TRANSACTION 111780484, not started
MySQL thread id 7801, OS thread handle 0x7ff21afff700, query id 117200125 iZwz9bopxrlqc7mv1cqc0gZ 120.78.205.38 root cleaning up
--------
FILE I/O
--------
I/O thread 0 state: waiting for completed aio requests (insert buffer thread)
I/O thread 1 state: waiting for completed aio requests (log thread)
I/O thread 2 state: waiting for completed aio requests (read thread)
I/O thread 3 state: waiting for completed aio requests (read thread)
I/O thread 4 state: waiting for completed aio requests (read thread)
I/O thread 5 state: waiting for completed aio requests (read thread)
I/O thread 6 state: waiting for completed aio requests (write thread)
I/O thread 7 state: waiting for completed aio requests (write thread)
I/O thread 8 state: waiting for completed aio requests (write thread)
I/O thread 9 state: waiting for completed aio requests (write thread)
Pending normal aio reads: 0 [0, 0, 0, 0] , aio writes: 0 [0, 0, 0, 0] ,
 ibuf aio reads: 0, log i/o's: 0, sync i/o's: 0
Pending flushes (fsync) log: 0; buffer pool: 0
8559 OS file reads, 12457384 OS file writes, 6040120 OS fsyncs
0.00 reads/s, 0 avg bytes/read, 4.83 writes/s, 2.78 fsyncs/s
-------------------------------------
INSERT BUFFER AND ADAPTIVE HASH INDEX
-------------------------------------
Ibuf: size 1, free list len 1895, seg size 1897, 273 merges
merged operations:
 insert 1040, delete mark 0, delete 0
discarded operations:
 insert 0, delete mark 0, delete 0
Hash table size 276707, node heap has 87 buffer(s)
3.83 hash searches/s, 11.61 non-hash searches/s
---
LOG
---
Log sequence number 8621030500
Log flushed up to   8621030500
Pages flushed up to 8621026316
Last checkpoint at  8621026316
0 pending log writes, 0 pending chkp writes
2808463 log i/o's done, 0.94 log i/o's/second
----------------------
BUFFER POOL AND MEMORY
----------------------
Total memory allocated 137363456; in additional pool allocated 0
Dictionary memory allocated 2267341
Buffer pool size   8192
Free buffers       1024
Database pages     7081
Old database pages 2593
Modified db pages  8
Pending reads 0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 1680, not young 124037
0.00 youngs/s, 0.00 non-youngs/s
Pages read 7381, created 8238, written 8898960
0.00 reads/s, 0.00 creates/s, 3.56 writes/s
Buffer pool hit rate 1000 / 1000, young-making rate 0 / 1000 not 0 / 1000
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 7081, unzip_LRU len: 0
I/O sum[308]:cur[0], unzip sum[0]:cur[0]
--------------
ROW OPERATIONS
--------------
0 queries inside InnoDB, 0 queries in queue
0 read views open inside InnoDB
Main thread process no. 29403, id 140677885191936, state: sleeping
Number of rows inserted 2324850, updated 1008701, deleted 739761, read 9221107060
0.67 inserts/s, 0.28 updates/s, 0.00 deletes/s, 245.54 reads/s
----------------------------
END OF INNODB MONITOR OUTPUT
============================

1 row in set (0.00 sec)

ERROR: 
No query specified

```
3. 定位到LATEST DETECTED DEADLOCK 我们发现有存在事务锁，同一时间存在排他锁跟共享锁
4. 修改程序代码
```java
List<AccountYxtEcp> needAddAccountYxtEcp = null;
//设置batch
String batch = RandomUtil.generateNumber6();
log.info("过滤前ecps={}",JSON.toJSONString(ecps));
ecps.stream().filter(distinctByKey(AccountYxtEcp::getUid));
AccountYxtEcp ecp = ecps.get(0);
//查询到当天已经插入的批次
List<AccountYxtEcp> accountYxtEcps = accountYxtEcpMapper.ecpsByDay(ecp.getBid(),ecp.getDay());
if (CollectionUtil.isEmpty(accountYxtEcps)){
    log.info("当前暂时还没有插入数据");
    needAddAccountYxtEcp = ecps;
}else{
    //判断当前是否已经有交集
    accountYxtEcps.retainAll(ecps);
    if (accountYxtEcps.size()>0){
        log.info("有交集");
        needAddAccountYxtEcp = ecps.stream()
                .filter(s->!accountYxtEcps.stream().map(accountYxtEcp -> accountYxtEcp.getUid()).collect(Collectors.toList()).contains(s.getUid()))
                .collect(Collectors.toList()) ;
    }else{
        needAddAccountYxtEcp = ecps;
        log.info("没有交集,那么便利重新插入数据");
    }
}
needAddAccountYxtEcp.forEach(accountYxtEcp -> accountYxtEcp.setBatch(batch));
//新增
accountYxtEcpMapper.insertBatch(needAddAccountYxtEcp);
for (AccountYxtEcp yxtEcp : needAddAccountYxtEcp) {
    this.addTradeDetail(yxtEcp);
}
```