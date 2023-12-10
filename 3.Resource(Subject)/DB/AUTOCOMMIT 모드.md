# 정의

AUTOCOMMIT이란 Commit 명령을 따로 하지 않아도 자동으로 모든 명령을 Commit되어 즉시 반영하는 기능

# AUTOCOMMIT 확인

```sql
MariaDB [mydb]> SELECT @@AUTOCOMMIT;
+--------------+
| @@AUTOCOMMIT |
+--------------+
|            1 |
+--------------+
1 row in set (0.010 sec)
```

# 비활성화

```sql
MariaDB [mydb]> SET autocommit=0;
Query OK, 0 rows affected (0.010 sec)

MariaDB [mydb]> SELECT @@AUTOCOMMIT;
+--------------+
| @@AUTOCOMMIT |
+--------------+
|            0 |
+--------------+
1 row in set (0.000 sec)
```

# 주의

- MariaDB 에서는 기본적으로 AUTOCOMMIT 모드 활성화 되어 있다
- AUTOCOMMIT을 활성화 한 상태에서 사용하면 실수가 곧바로 DB에 반영
- Commit된 트랜잭션은 Rollback이 불가능