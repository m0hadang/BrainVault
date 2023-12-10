# 락이 걸려 있는 프로세스 확인

```sql
MariaDB [mydb]> SHOW PROCESSLIST;
+----+------+-----------------+--------------------+---------+------+----------+------------------+----------+
| Id | User | Host            | db                 | Command | Time | State    | Info             | Progress |
+----+------+-----------------+--------------------+---------+------+----------+------------------+----------+
|  9 | root | localhost:53022 | information_schema | Sleep   |    8 |          | NULL             |    0.000 |
| 13 | root | localhost:60230 | mydb               | Sleep   |  261 |          | NULL             |    0.000 |
| 15 | root | localhost:61958 | mydb               | Query   |    0 | starting | SHOW PROCESSLIST |    0.000 |
+----+------+-----------------+--------------------+---------+------+----------+------------------+----------+
3 rows in set (0.005 sec)
```

`KILL 255;` 명령어를 위해 lock을 해제할 수 있다

# 배타 잠금(Exclusive Lock, X Lock)

Write에 대한 Lock. SELECT ... FOR Update, UPDATE, DELETE 등의 수정 쿼리를 날릴 때 각 Row에 걸리는 Lock

X Lock이 걸려 있으면 다른 트랜잭션은 S Lock, X Lock 걸 수 없음

# 공유 잠금(Shared Lock, S Lock)

Read에 대한 Lock

일반적인 SELECT 쿼리가 아닌 SELECT ... LOCK IN SHARE MODE 또는 SELECT ... FOR SHARE 를 사용해 Read 작업을 수행할 떄 사용

# 레코드 잠금(Record Lock)

InnoDB에서 Record Lock은 Row가 아닌 DB Index Record에 걸리는 Lock

테이블에 index가 없다면 숨겨져 있는 clustered index를 사용하여 Record를 잠금

# 갭 잠금(Gap Lock)

index record의 gap에 걸리는 lock

gap이란 index record가 없는 부분

조건에 해당하는 새로운ㅇ row가 추가되는 것을 방지


# 데드락

Deadlock detection, Lock Wait Timeout

Deadlock Detection이 활성화 되었으면 Rollback 할 작은 트랜잭션 선택(트랜잭션의 크기는 insert, update, delete된 행 수에 의해 결정 그러나 oracle 에서는 반드시 그렇지 않음, 벤더사 마다 다름)

각 트랜잭션의 자신의 Row를 Lcok 걸고 다른 Row를 락을 걸려고 시도 할 때 문제 발생.

Transaction 1

```sql
MariaDB [mydb]> start transaction;
Query OK, 0 rows affected (0.000 sec)


MariaDB [mydb]> update users set name = 'BBBB' WHERE id = 4;
Query OK, 1 row affected (0.001 sec)
Rows matched: 1  Changed: 1  Warnings: 0


MariaDB [mydb]> update users set name = 'BBBB' WHERE id = 3;
Query OK, 1 row affected (12.111 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

Transaction 2

```sql
MariaDB [mydb]> start transaction;
Query OK, 0 rows affected (0.011 sec)


MariaDB [mydb]> UPDATE users SET name = 'AAAA' WHERE id = 3;
Query OK, 1 row affected (0.001 sec)
Rows matched: 1  Changed: 1  Warnings: 0


MariaDB [mydb]> UPDATE users SET name = 'AAAA' WHERE id = 4;
ERROR 1213 (40001): Deadlock found when trying to get lock; try restarting transaction
```

