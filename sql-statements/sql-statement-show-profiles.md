---
title: SHOW PROFILES
summary: An overview of the usage of SHOW PROFILES for the TiDB database.
---

# SHOW PROFILES {#show-profiles}

The `SHOW PROFILES` statement currently only returns an empty result.

## Synopsis {#synopsis}

**ShowStmt:**

![ShowStmt](/media/sqlgram/ShowStmt.png)

## Examples {#examples}

{{< copyable "" >}}

```sql
SHOW PROFILES;
```

```
Empty set (0.00 sec)
```

## MySQL compatibility {#mysql-compatibility}

This statement is included only for compatibility with MySQL. Executing `SHOW PROFILES` always returns an empty result.
