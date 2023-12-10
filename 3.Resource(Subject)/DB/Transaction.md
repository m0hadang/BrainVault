하나의 수행 또는 처리에 대해 여럴 단계로 나누어서 처리

하나의 처리를 COMMIT 명령어를 수행하기 전, 한번 더 체크할 수 있도록 함으로써 더욱 안정적인 DB 작업 수행 가능.

# 키워드

```sql
START TRANSACTION [transaction_property [, transaction_property] ...] | BEGIN [WORK]
COMMIT [WORK] [AND [NO] CHAIN] [[NO] RELEASE]
ROLLBACK [WORK] [AND [NO] CHAIN] [[NO] RELEASE]
SET autocommit = {0 | 1}
```


# 트랜잭션 처리

0. 현재 데이터 상태

```sql
MariaDB [mydb]> SELECT * FROM users;
+----+---------+----------+------+---------------------+
| id | user_id | pwd      | name | created_at          |
+----+---------+----------+------+---------------------+
|  3 | user1   | test1234 | CCCC | 2023-10-02 21:57:34 |
+----+---------+----------+------+---------------------+
1 row in set (0.001 sec)
```

1. 트랜잭션 시작(세션1)

```sql
MariaDB [mydb]> START TRANSACTION;
Query OK, 0 rows affected (0.000 sec)
```

2. 데이터 갱신(세션1)

```sql
MariaDB [mydb]> UPDATE users SET name = 'BBBB' WHERE id = 3;
Query OK, 1 row affected (0.001 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

3. 데이터 확인(세션1) - DB에 업데이트 된 것 처럼 보이지만 실제로 Commit되지 않음.

```sql
MariaDB [mydb]> SELECT * FROM users;
+----+---------+----------+------+---------------------+
| id | user_id | pwd      | name | created_at          |
+----+---------+----------+------+---------------------+
|  3 | user1   | test1234 | BBBB | 2023-10-02 21:57:34 |
+----+---------+----------+------+---------------------+
1 row in set (0.001 sec)
```

4. 데이터 확인(세션2) - 다른 세션에서는 아직 갱신되어 있지 않음.

```sql
MariaDB [mydb]> SELECT * FROM users;
+----+---------+----------+------+---------------------+
| id | user_id | pwd      | name | created_at          |
+----+---------+----------+------+---------------------+
|  3 | user1   | test1234 | CCCC | 2023-10-02 21:57:34 |
+----+---------+----------+------+---------------------+
1 row in set (0.001 sec)
```

5. 데이터 갱신(세션2) - 다른 세션에서 갱신 하려 하지만 Transaction에 의해 Lock이 걸려 있어 에러

```sql
MariaDB [mydb]> UPDATE users SET name = 'XXXX' WHERE id = 3;
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
```

6. COMMIT(세션1)

```sql
MariaDB [mydb]> COMMIT;
Query OK, 0 rows affected (0.012 sec)
```

7. 데이터 갱신(세션2) - Commit 이 완료된 후 에는 다른 세션에서 UPDATE 가능

```sql
MariaDB [mydb]> UPDATE users SET name = 'XXXX' WHERE id = 3;
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
```

- COMMIT 명령어 대신 ROLLBACK 을 하면 수정이 취소되고 Transaction 은 완료 된다

# 주의

- 현재 세션에서 Transaction을 걸어도 다른 세션에서 COMMIT 명령어를 실행하여 정상 COMMIT 가능 하다
- Transaction을 걸지 않을 경우 다른 세션에서 UPDATE 가능하고 COMMIT 명령이 수행되면 가장 마지막에 UPDATE된 명령으로 데이터가 DB에 반영된다