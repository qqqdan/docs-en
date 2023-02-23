---
title: SHOW ENGINES | TiDB SQL Statement Reference
summary: An overview of the usage of SHOW ENGINES for the TiDB database.
---

# SHOW ENGINES {#show-engines}

This statement is used to list all supported storage engines. The syntax is included only for compatibility with MySQL.

## Synopsis {#synopsis}

**ShowEnginesStmt:**

![ShowEnginesStmt](/media/sqlgram/ShowEnginesStmt.png)

```sql
SHOW ENGINES;
```

## Examples {#examples}

```sql
mysql> SHOW ENGINES;
+--------+---------+------------------------------------------------------------+--------------+------+------------+
| Engine | Support | Comment                                                    | Transactions | XA   | Savepoints |
+--------+---------+------------------------------------------------------------+--------------+------+------------+
| InnoDB | DEFAULT | Supports transactions, row-level locking, and foreign keys | YES          | YES  | YES        |
+--------+---------+------------------------------------------------------------+--------------+------+------------+
1 row in set (0.00 sec)
```

## MySQL compatibility {#mysql-compatibility}

-   This statement will always only return InnoDB as the supported engine. Internally, TiDB will typically use TiKV as the storage engine.
