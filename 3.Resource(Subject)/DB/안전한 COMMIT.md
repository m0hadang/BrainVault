실무에서 UPDATE, DELETE 문에 WHERE 절을 사용하지 않고 잘못 사용하는 경우 있음

이런 실수를 방지하기 위해 UPDATE 문 사용 전에 SELECT 문을 활용해서 업데이트 적용 할 데이터를 먼저 조회하고, 트랜잭션 안에서 UPDATE 문을 실행하는 방법도 있음

자동으로 Commit 되지 않기 위해 AUTOCOMMIT 모드는 비활성화 해야 한다
- [[AUTOCOMMIT 모드#비활성화]]


0. 수정 할 데이터 확인

```sql
MariaDB [mydb]> SELECT * FROM users;
+----+---------+----------+------+---------------------+
| id | user_id | pwd      | name | created_at          |
+----+---------+----------+------+---------------------+
|  3 | user1   | test1234 | BBBB | 2023-10-02 21:57:34 |
+----+---------+----------+------+---------------------+
1 row in set (0.001 sec)
```

1. Transaction 시작

```sql
MariaDB [mydb]> START TRANSACTION;
Query OK, 0 rows affected (0.000 sec)
```

2. UPDATE

```sql
MariaDB [mydb]> UPDATE users SET name = 'XXXX' WHERE id = 3;
Query OK, 1 row affected (0.001 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

3. 정상 UPDATE 되었는지 확인

```sql
MariaDB [mydb]> SELECT * FROM users;
+----+---------+----------+------+---------------------+
| id | user_id | pwd      | name | created_at          |
+----+---------+----------+------+---------------------+
|  3 | user1   | test1234 | XXXX | 2023-10-02 21:57:34 |
+----+---------+----------+------+---------------------+
1 row in set (0.000 sec)
```

4. COMMIT or ROLLBACK

```sql
MariaDB [mydb]> COMMIT;
Query OK, 0 rows affected (0.011 sec)
```