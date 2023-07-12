---
title: TiDB 6.1.0 Release Notes
---

# TiDB 6.1.0 Release Notes {#tidb-6-1-0-release-notes}

Release date: June 13, 2022

TiDB version: 6.1.0

Quick access: [<a href="https://docs.pingcap.com/tidb/v6.1/quick-start-with-tidb">Quick start</a>](https://docs.pingcap.com/tidb/v6.1/quick-start-with-tidb) | [<a href="https://docs.pingcap.com/tidb/v6.1/production-deployment-using-tiup">Production deployment</a>](https://docs.pingcap.com/tidb/v6.1/production-deployment-using-tiup) | [<a href="https://www.pingcap.com/download/?version=v6.1.0#version-list">Installation packages</a>](https://www.pingcap.com/download/?version=v6.1.0#version-list)

In 6.1.0, the key new features or improvements are as follows:

-   List partitioning and list COLUMNS partitioning become GA, compatible with MySQL 5.7
-   TiFlash partitioned table (dynamic pruning) becomes GA
-   Support user-level lock management, compatible with MySQL
-   Support non-transactional DML statements (only support `DELETE`)
-   TiFlash supports on-demand data compaction
-   MPP introduces the window function framework
-   TiCDC supports replicating changelogs to Kafka via Avro
-   TiCDC supports splitting large transactions during replication, which significantly reduces replication latency caused by large transactions
-   The optimistic mode for merging and migrating sharded tables becomes GA

## New Features {#new-features}

### SQL {#sql}

-   List partitioning and list COLUMNS partitioning become GA. Both are compatible with MySQL 5.7.

    User documents: [<a href="/partitioned-table.md#list-partitioning">List partitioning</a>](/partitioned-table.md#list-partitioning), [<a href="/partitioned-table.md#list-columns-partitioning">List COLUMNS partitioning</a>](/partitioned-table.md#list-columns-partitioning)

-   TiFlash supports initiating a compact command. (experimental)

    TiFlash v6.1.0 introduces the `ALTER TABLE ... COMPACT` statement, which provides a manual way to compact physical data based on the existing background compaction mechanism. With this statement, you can update data in earlier formats and improve read/write performance any time as appropriate. It is recommended that you execute this statement to compact data after upgrading your cluster to v6.1.0. This statement is an extension of the standard SQL syntax and therefore is compatible with MySQL clients. For scenarios other than TiFlash upgrade, usually there is no need to use this statement.

    [<a href="/sql-statements/sql-statement-alter-table-compact.md">User document</a>](/sql-statements/sql-statement-alter-table-compact.md), [<a href="https://github.com/pingcap/tiflash/issues/4145">#4145</a>](https://github.com/pingcap/tiflash/issues/4145)

-   TiFlash implements the window function framework and supports the following window functions:

    -   `RANK()`
    -   `DENSE_RANK()`
    -   `ROW_NUMBER()`

    [<a href="/tiflash/tiflash-supported-pushdown-calculations.md">User document</a>](/tiflash/tiflash-supported-pushdown-calculations.md), [<a href="https://github.com/pingcap/tidb/issues/33072">#33072</a>](https://github.com/pingcap/tidb/issues/33072)

### Observability {#observability}

-   Continuous Profiling supports the ARM architecture and TiFlash.

    [<a href="/dashboard/continuous-profiling.md">User document</a>](/dashboard/continuous-profiling.md)

-   Grafana adds a Performance Overview dashboard to provide a system-level entry for overall performance diagnosis.

    As a new dashboard in the TiDB visualized monitoring component Grafana, Performance Overview provides a system-level entry for overall performance diagnosis. According to the top-down performance analysis methodology, the Performance Overview dashboard reorganizes TiDB performance metrics based on database time breakdown and displays these metrics in different colors. By checking these colors, you can identify performance bottlenecks of the entire system at the first glance, which significantly reduces performance diagnosis time and simplifies performance analysis and diagnosis.

    [<a href="/performance-tuning-overview.md">User document</a>](/performance-tuning-overview.md)

### Performance {#performance}

-   Support customized Region size (experimental)

    Setting Regions to a larger size can effectively reduce the number of Regions, make Regions easier to manage, and improve the cluster performance and stability. This feature introduces the concept of bucket, which is a smaller range within a Region. Using buckets as the query unit can optimize concurrent query performance when Regions are set to a larger size. Using buckets as the query unit can also dynamically adjust the sizes of hot Regions to ensure the scheduling efficiency and load balance. This feature is currently experimental. It is not recommended to use it in production environments.

    [<a href="/tune-region-performance.md">User document</a>](/tune-region-performance.md), [<a href="https://github.com/tikv/tikv/issues/11515">#11515</a>](https://github.com/tikv/tikv/issues/11515)

-   Use Raft Engine as the default log storage engine

    Since v6.1.0, TiDB uses Raft Engine as the default storage engine for logs. Compared with RocksDB, Raft Engine can reduce TiKV I/O write traffic by up to 40% and CPU usage by 10%, while improving foreground throughput by about 5% and reducing tail latency by 20% under certain loads.

    [<a href="/tikv-configuration-file.md#raft-engine">User document</a>](/tikv-configuration-file.md#raft-engine), [<a href="https://github.com/tikv/raft-engine/issues/95">#95</a>](https://github.com/tikv/raft-engine/issues/95)

-   Support the join order hint syntax

    -   The `LEADING` hint reminds the optimizer to use the specified order as the prefix of join operations. A good prefix of join can quickly reduce the amount of data at the early phase of join and improve the query performance.
    -   The `STRAIGHT_JOIN` hint reminds the optimizer to join tables in an order that is consistent with the order of tables in the `FROM` clause.

    This provides a method for you to fix the order of table joins. A proper use of the hints can effectively enhance the SQL performance and cluster stability.

    User document: [<a href="/optimizer-hints.md#leadingt1_name--tl_name-">`LEADING`</a>](/optimizer-hints.md#leadingt1_name--tl_name-), [<a href="/optimizer-hints.md#straight_join">`STRAIGHT_JOIN`</a>](/optimizer-hints.md#straight_join), [<a href="https://github.com/pingcap/tidb/issues/29932">#29932</a>](https://github.com/pingcap/tidb/issues/29932)

-   TiFlash supports four more functions:

    -   `FROM_DAYS`
    -   `TO_DAYS`
    -   `TO_SECONDS`
    -   `WEEKOFYEAR`

    [<a href="/tiflash/tiflash-supported-pushdown-calculations.md">User document</a>](/tiflash/tiflash-supported-pushdown-calculations.md), [<a href="https://github.com/pingcap/tiflash/issues/4679">#4679</a>](https://github.com/pingcap/tiflash/issues/4679), [<a href="https://github.com/pingcap/tiflash/issues/4678">#4678</a>](https://github.com/pingcap/tiflash/issues/4678), [<a href="https://github.com/pingcap/tiflash/issues/4677">#4677</a>](https://github.com/pingcap/tiflash/issues/4677)

-   TiFlash supports partitioned tables in dynamic pruning mode.

    To enhance performance in OLAP scenarios, dynamic pruning mode is supported for partitioned tables. If your TiDB is upgraded from versions earlier than v6.0.0, it is recommended that you manually update statistics of existing partitioned tables, so as to maximize the performance (not required for new installations or new partitions created after upgrade to v6.1.0).

    User documents: [<a href="/tiflash/use-tiflash-mpp-mode.md#access-partitioned-tables-in-the-mpp-mode">Access partitioned tables in the MPP mode</a>](/tiflash/use-tiflash-mpp-mode.md#access-partitioned-tables-in-the-mpp-mode), [<a href="/partitioned-table.md#dynamic-pruning-mode">Dynamic pruning mode</a>](/partitioned-table.md#dynamic-pruning-mode), [<a href="https://github.com/pingcap/tiflash/issues/3873">#3873</a>](https://github.com/pingcap/tiflash/issues/3873)

### Stability {#stability}

-   Automatic recovery from SST corruption

    When RocksDB detects a damaged SST file in the background, TiKV will try to schedule the affected Peer and recover its data using other replicas. You can set the maximum allowable time for the recovery using the `background-error-recovery-window` parameter. If the recovery operation is not completed within the time window, TiKV will panic. This feature automatically detects and recovers recoverable damaged storage, thus improving the cluster stability.

    [<a href="/tikv-configuration-file.md#background-error-recovery-window-new-in-v610">User document</a>](/tikv-configuration-file.md#background-error-recovery-window-new-in-v610), [<a href="https://github.com/tikv/tikv/issues/10578">#10578</a>](https://github.com/tikv/tikv/issues/10578)

-   Support non-transactional DML statement

    In the scenarios of large data processing, a single SQL statement with a large transaction might have a negative impact on the cluster stability and performance. Since v6.1.0, TiDB supports providing a syntax in which a `DELETE` statement is split into multiple statements for batch processing. The split statements compromise transactional atomicity and isolation but greatly improve the cluster stability. For detailed syntax, see [<a href="/sql-statements/sql-statement-batch.md">`BATCH`</a>](/sql-statements/sql-statement-batch.md).

    [<a href="/non-transactional-dml.md">User document</a>](/non-transactional-dml.md)

-   TiDB supports configuring the maximum GC wait time

    The transaction of TiDB adopts the Multi-Version Concurrency Control (MVCC) mechanism. When the newly written data overwrites the old data, the old data is not replaced, and both versions of data are stored. The old data is cleaned up by the Garbage Collection (GC) task periodically, which helps reclaim storage space to improve the performance and stability of the cluster. GC is triggered every 10 minutes by default. To ensure that long-running transactions can access the corresponding historical data, when there are transactions in execution, the GC task is delayed. To ensure that the GC task is not delayed indefinitely, TiDB introduces the system variable [<a href="/system-variables.md#tidb_gc_max_wait_time-new-in-v610">`tidb_gc_max_wait_time`</a>](/system-variables.md#tidb_gc_max_wait_time-new-in-v610) to control the maximum delay time of the GC task. If the maximum delay time is exceeded, the GC will be forcibly executed. The default value of the variable is 24 hours. This feature enables you to control the relationship between the GC waiting time and the long-running transaction, which improves the stability of the cluster.

    [<a href="/system-variables.md#tidb_gc_max_wait_time-new-in-v610">User document</a>](/system-variables.md#tidb_gc_max_wait_time-new-in-v610)

-   TiDB supports configuring the maximum execution time for automatic statistics collection tasks

    Databases can effectively understand the distribution of data by collecting statistics, which helps generate reasonable execution plans and improve the efficiency of SQL execution. TiDB regularly collects statistics on frequently changed data objects in the background. However, collecting statistics takes up cluster resources and might affect the stable operation of the business during business peaks.

    Starting from v6.1.0, TiDB introduces [<a href="/system-variables.md#tidb_max_auto_analyze_time-new-in-v610">`tidb_max_auto_analyze_time`</a>](/system-variables.md#tidb_max_auto_analyze_time-new-in-v610) to control the maximum execution time for background statistics collection, which is 12 hours by default. When the application does not encounter a resource bottleneck, it is recommended not to modify this variable so that TiDB can timely collect statistics.

    [<a href="/system-variables.md">User document</a>](/system-variables.md)

### Ease of use {#ease-of-use}

-   Support a one-stop online data recovery when multiple replicas are lost

    Before TiDB v6.1.0, when multiple Region replicas are lost because of machine failure, users have to stop all TiKV servers and use TiKV Control to recover TiKV one by one. Since TiDB v6.1.0, the recovery process is fully automated, does not require to stop TiKV, and does not affect other applications online. The recovery process can be triggered using PD Control and provides a more user-friendly summary information.

    [<a href="/online-unsafe-recovery.md">User document</a>](/online-unsafe-recovery.md), [<a href="https://github.com/tikv/tikv/issues/10483">#10483</a>](https://github.com/tikv/tikv/issues/10483)

-   Support viewing history statistics collection tasks

    You can use the `SHOW ANALYZE STATUS` statement to show cluster-level statistics collection tasks. Before TiDB v6.1.0, the `SHOW ANALYZE STATUS` statement shows instance-level tasks only, and history task records are cleared after a TiDB restart. Therefore, you cannot view history statistics collection time and details. Starting from TiDB v6.1.0, history records of statistics collection tasks are persisted and can be queried after a cluster restart, which provides a reference for troubleshooting query performance issues caused by statistics anomalies.

    [<a href="/sql-statements/sql-statement-show-analyze-status.md">User document</a>](/sql-statements/sql-statement-show-analyze-status.md)

-   Support modifying TiDB, TiKV, and TiFlash configurations dynamically

    In earlier TiDB versions, after modifying a configuration item, you must restart the cluster to make the modification effective. This might interrupt online services. To address this issue, TiDB v6.1.0 introduces the dynamic configuration feature, which allows you to validate a parameter change without restarting the cluster. The specific optimizations are as follows:

    -   Transform some TiDB configuration items to system variables, so that they can be modified dynamically and persisted. Note that the original configuration items are deprecated after transformation. For a detailed list of the transformed configuration items, see [<a href="#configuration-file-parameters">Configuration file parameters</a>](#configuration-file-parameters).
    -   Support configuring some TiKV parameters online. For a detailed list of the parameters, see [<a href="#others">Others</a>](#others).
    -   Transform the TiFlash configuration item `max_threads` to a system variable `tidb_max_tiflash_threads`, so that the configuration can be modified dynamically and persisted. Note that the original configuration item remains after transformation.

    For v6.1.0 clusters upgraded (including online and offline upgrades) from earlier versions, note that:

    -   If the configuration items specified in the configuration file before the upgrade already exist, TiDB will automatically update the values of the configured items to those of the corresponding system variables during the upgrade process. In this way, after the upgrade, the system behavior is not affected by parameter optimization.
    -   The automatic update mentioned above occurs only once during the upgrade. After the upgrade, the deprecated configuration items are no longer effective.

    This feature allows you to modify parameters dynamically, and validate and persist them, instead of restarting the system and interrupting services. This makes your daily maintenance easier.

    [<a href="/dynamic-config.md">User document</a>](/dynamic-config.md)

-   Support killing queries or connections globally

    You can control the Global Kill feature using the `enable-global-kill` configuration (enabled by default).

    Before TiDB v6.1.0, when an operation consumes a lot of resources and causes cluster stability issues, you have to connect to the target TiDB instance and then run the `KILL TIDB ${id};` command to terminate the target connection and operation. In the case of many TiDB instances, this method is not easy to use and prone to wrong operations. Starting from v6.1.0, the `enable-global-kill` configuration is introduced and enabled by default. You can run the kill command in any TiDB instance to terminate a specified connection and operation, without worrying about incorrectly terminating other queries or sessions by mistake when there is a proxy between the client and TiDB. Currently, TiDB does not support using Ctrl+C to terminate queries or sessions.

    [<a href="/tidb-configuration-file.md#enable-global-kill-new-in-v610">User document</a>](/tidb-configuration-file.md#enable-global-kill-new-in-v610), [<a href="https://github.com/pingcap/tidb/issues/8854">#8854</a>](https://github.com/pingcap/tidb/issues/8854)

-   TiKV API V2 (experimental)

    Before v6.1.0, when TiKV is used as Raw Key Value storage, TiKV only provides basic Key Value read and write capability because it only stores the raw data passed in by the client.

    TiKV API V2 provides a new Raw Key Value storage format and access interface, including:

    -   The data is stored in MVCC and the change timestamp of the data is recorded. This feature will lay the foundation for implementing Change Data Capture and incremental backup and restore.
    -   Data is scoped according to different usage and supports co-existence of a single TiDB cluster, Transactional KV, RawKV applications.

    <Warning>
    Due to significant changes in the underlying storage format, after enabling API V2, you cannot roll back a TiKV cluster to a version earlier than v6.1.0. Downgrading TiKV might result in data corruption.
    </Warning>

    [<a href="/tikv-configuration-file.md#api-version-new-in-v610">User document</a>](/tikv-configuration-file.md#api-version-new-in-v610), [<a href="https://github.com/tikv/tikv/issues/11745">#11745</a>](https://github.com/tikv/tikv/issues/11745)

### MySQL compatibility {#mysql-compatibility}

-   Support compatibility with user-level lock management with MySQL

    User-level locks are a user-named lock management system provided by MySQL through built-in functions. The locking functions can provide lock blocking, waiting, and other lock management capabilities. User-level locks are also widely used in ORM frameworks, such as Rails, Elixir, and Ecto. Since v6.1.0, TiDB has supported MySQL-compatible user-level lock management, and supports `GET_LOCK`, `RELEASE_LOCK`, and `RELEASE_ALL_LOCKS` functions.

    [<a href="/functions-and-operators/locking-functions.md">User document</a>](/functions-and-operators/locking-functions.md), [<a href="https://github.com/pingcap/tidb/issues/14994">#14994</a>](https://github.com/pingcap/tidb/issues/14994)

### Data migration {#data-migration}

-   The optimistic mode for merging and migrating sharded tables becomes GA

    DM adds a large number of scenario tests for tasks that merge and migrate data from sharded tables in the optimistic mode, which covers 90% of the daily use scenarios. Compared with the pessimistic mode, the optimistic mode is simpler and more efficient to use. It is recommended to use the optimistic mode preferably after you are familiar with the usage notes.

    [<a href="/dm/feature-shard-merge-optimistic.md#restrictions">User document</a>](/dm/feature-shard-merge-optimistic.md#restrictions)

-   DM WebUI supports starting a task according to the specified parameters

    When starting a migration task, you can specify a start time and a safe mode duration. This is especially useful when you create an incremental migration task with lots of sources, eliminating the need to specify the binlog start position specifically for each source.

    [<a href="/dm/dm-webui-guide.md">User document</a>](/dm/dm-webui-guide.md), [<a href="https://github.com/pingcap/tiflow/issues/5442">#5442</a>](https://github.com/pingcap/tiflow/issues/5442)

### TiDB data share subscription {#tidb-data-share-subscription}

-   TiDB supports data sharing with various third-party data ecosystems

    -   TiCDC supports sending TiDB incremental data to Kafka in the Avro format, allowing data sharing with third-parties, such as KSQL and Snowflake via Confluent.

        [<a href="/ticdc/ticdc-avro-protocol.md">User document</a>](/ticdc/ticdc-avro-protocol.md), [<a href="https://github.com/pingcap/tiflow/issues/5338">#5338</a>](https://github.com/pingcap/tiflow/issues/5338)

    -   TiCDC supports dispatching incremental data from TiDB to different Kafka topics by table, which, combined with the Canal-json format, allows sharing data directly with Flink.

        [<a href="/ticdc/manage-ticdc.md#customize-the-rules-for-topic-and-partition-dispatchers-of-kafka-sink">User document</a>](/ticdc/manage-ticdc.md#customize-the-rules-for-topic-and-partition-dispatchers-of-kafka-sink), [<a href="https://github.com/pingcap/tiflow/issues/4423">#4423</a>](https://github.com/pingcap/tiflow/issues/4423)

    -   TiCDC supports SASL GSSAPI authentication types and adds SASL authentication examples using Kafka.

        [<a href="/ticdc/manage-ticdc.md#ticdc-uses-the-authentication-and-authorization-of-kafka">User document</a>](/ticdc/manage-ticdc.md#ticdc-uses-the-authentication-and-authorization-of-kafka), [<a href="https://github.com/pingcap/tiflow/issues/4423">#4423</a>](https://github.com/pingcap/tiflow/issues/4423)

-   TiCDC supports replicating `charset=GBK` tables.

    [<a href="/character-set-gbk.md#component-compatibility">User document</a>](/character-set-gbk.md#component-compatibility), [<a href="https://github.com/pingcap/tiflow/issues/4806">#4806</a>](https://github.com/pingcap/tiflow/issues/4806)

## Compatibility changes {#compatibility-changes}

### System variables {#system-variables}

| Variable name                                                                                                                                                                                                            | Change type | Description                                                                                                                                                                                                                                                                                           |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [<a href="/system-variables.md#tidb_enable_list_partition-new-in-v50">`tidb_enable_list_partition`</a>](/system-variables.md#tidb_enable_list_partition-new-in-v50)                                                      | Modified    | The default value is changed from `OFF` to `ON`.                                                                                                                                                                                                                                                      |
| [<a href="/system-variables.md#tidb_mem_quota_query">`tidb_mem_quota_query`</a>](/system-variables.md#tidb_mem_quota_query)                                                                                              | Modified    | This variable adds the GLOBAL scope, and the variable value persists to the cluster.                                                                                                                                                                                                                  |
| [<a href="/system-variables.md#tidb_query_log_max_len">`tidb_query_log_max_len`</a>](/system-variables.md#tidb_query_log_max_len)                                                                                        | Modified    | The variable scope is changed from INSTANCE to GLOBAL. The variable value persists to the cluster, and the value range is changed to `[0, 1073741824]`.                                                                                                                                               |
| [<a href="/system-variables.md#require_secure_transport-new-in-v610">`require_secure_transport`</a>](/system-variables.md#require_secure_transport-new-in-v610)                                                          | Newly added | This setting was previously a `tidb.toml` option (`security.require-secure-transport`), but changed to a system variable starting from TiDB v6.1.0.                                                                                                                                                   |
| [<a href="/system-variables.md#tidb_committer_concurrency-new-in-v610">`tidb_committer_concurrency`</a>](/system-variables.md#tidb_committer_concurrency-new-in-v610)                                                    | Newly added | This setting was previously a `tidb.toml` option (`performance.committer-concurrency`), but changed to a system variable starting from TiDB v6.1.0.                                                                                                                                                   |
| [<a href="/system-variables.md#tidb_enable_auto_analyze-new-in-v610">`tidb_enable_auto_analyze`</a>](/system-variables.md#tidb_enable_auto_analyze-new-in-v610)                                                          | Newly added | This setting was previously a `tidb.toml` option (`run-auto-analyze`), but changed to a system variable starting from TiDB v6.1.0.                                                                                                                                                                    |
| [<a href="/system-variables.md#tidb_enable_new_only_full_group_by_check-new-in-v610">`tidb_enable_new_only_full_group_by_check`</a>](/system-variables.md#tidb_enable_new_only_full_group_by_check-new-in-v610)          | Newly added | This variable controls the behavior when TiDB performs the `ONLY_FULL_GROUP_BY` check.                                                                                                                                                                                                                |
| [<a href="/system-variables.md#tidb_enable_outer_join_reorder-new-in-v610">`tidb_enable_outer_join_reorder`</a>](/system-variables.md#tidb_enable_outer_join_reorder-new-in-v610)                                        | Newly added | Since v6.1.0, the Join Reorder algorithm of TiDB supports Outer Join. This variable controls the support behavior, and the default value is `ON`.                                                                                                                                                     |
| [<a href="/system-variables.md#tidb_enable_prepared_plan_cache-new-in-v610">`tidb_enable_prepared_plan_cache`</a>](/system-variables.md#tidb_enable_prepared_plan_cache-new-in-v610)                                     | Newly added | This setting was previously a `tidb.toml` option (`prepared-plan-cache.enabled`), but changed to a system variable starting from TiDB v6.1.0.                                                                                                                                                         |
| [<a href="/system-variables.md#tidb_gc_max_wait_time-new-in-v610">`tidb_gc_max_wait_time`</a>](/system-variables.md#tidb_gc_max_wait_time-new-in-v610)                                                                   | Newly added | This variable is used to set the maximum time of GC safe point blocked by uncommitted transactions.                                                                                                                                                                                                   |
| [<a href="/system-variables.md#tidb_max_auto_analyze_time-new-in-v610">tidb_max_auto_analyze_time</a>](/system-variables.md#tidb_max_auto_analyze_time-new-in-v610)                                                      | Newly added | This variable is used to specify the maximum execution time of auto analyze.                                                                                                                                                                                                                          |
| [<a href="/system-variables.md#tidb_max_tiflash_threads-new-in-v610">`tidb_max_tiflash_threads`</a>](/system-variables.md#tidb_max_tiflash_threads-new-in-v610)                                                          | Newly added | This variable is used to set the maximum concurrency for TiFlash to execute a request.                                                                                                                                                                                                                |
| [<a href="/system-variables.md#tidb_mem_oom_action-new-in-v610">`tidb_mem_oom_action`</a>](/system-variables.md#tidb_mem_oom_action-new-in-v610)                                                                         | Newly added | This setting was previously a `tidb.toml` option (`oom-action`), but changed to a system variable starting from TiDB v6.1.0.                                                                                                                                                                          |
| [<a href="/system-variables.md#tidb_mem_quota_analyze-new-in-v610">`tidb_mem_quota_analyze`</a>](/system-variables.md#tidb_mem_quota_analyze-new-in-v610)                                                                | Newly added | This variable controls the maximum memory usage when TiDB updates statistics, including manually executed [<a href="/sql-statements/sql-statement-analyze-table.md">`ANALYZE TABLE`</a>](/sql-statements/sql-statement-analyze-table.md) by users and automatic analyze tasks in the TiDB background. |
| [<a href="/system-variables.md#tidb_nontransactional_ignore_error-new-in-v610">`tidb_nontransactional_ignore_error`</a>](/system-variables.md#tidb_nontransactional_ignore_error-new-in-v610)                            | Newly added | This variable specifies whether to return error immediately when an error occurs in a non-transactional DML statement.                                                                                                                                                                                |
| [<a href="/system-variables.md#tidb_prepared_plan_cache_memory_guard_ratio-new-in-v610">`tidb_prepared_plan_cache_memory_guard_ratio`</a>](/system-variables.md#tidb_prepared_plan_cache_memory_guard_ratio-new-in-v610) | Newly added | This setting was previously a `tidb.toml` option (`prepared-plan-cache.memory-guard-ratio`), but changed to a system variable starting from TiDB v6.1.0.                                                                                                                                              |
| [<a href="/system-variables.md#tidb_prepared_plan_cache_size-new-in-v610">`tidb_prepared_plan_cache_size`</a>](/system-variables.md#tidb_prepared_plan_cache_size-new-in-v610)                                           | Newly added | This setting was previously a `tidb.toml` option (`prepared-plan-cache.capacity`), but changed to a system variable starting from TiDB v6.1.0.                                                                                                                                                        |
| [<a href="/system-variables.md#tidb_stats_cache_mem_quota-new-in-v610">`tidb_stats_cache_mem_quota`</a>](/system-variables.md#tidb_stats_cache_mem_quota-new-in-v610)                                                    | Newly added | This variable sets the memory quota for the TiDB statistics cache.                                                                                                                                                                                                                                    |

### Configuration file parameters {#configuration-file-parameters}

| Configuration file | Configuration                                                                                                                                                                                                                                                                                                               | Change type | Description                                                                                                                                                                                                                                                                                                        |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| TiDB               | `committer-concurrency`                                                                                                                                                                                                                                                                                                     | Deleted     | Replaced by the system variable `tidb_committer_concurrency`. This configuration item is no longer valid, if you want to modify the value, you need to modify the corresponding system variable.                                                                                                                   |
| TiDB               | `lower-case-table-names`                                                                                                                                                                                                                                                                                                    | Deleted     | Currently TiDB only supports `lower_case_table_name=2`. If another value is set, after the cluster is upgraded to v6.1.0, the value is lost.                                                                                                                                                                       |
| TiDB               | `mem-quota-query`                                                                                                                                                                                                                                                                                                           | Deleted     | Replaced by the system variable `tidb_mem_quota_query`. This configuration item is no longer valid, if you want to modify the value, you need to modify the corresponding system variable.                                                                                                                         |
| TiDB               | `oom-action`                                                                                                                                                                                                                                                                                                                | Deleted     | Replaced by the system variable `tidb_mem_oom_action`. This configuration item is no longer valid, if you want to modify the value, you need to modify the corresponding system variable.                                                                                                                          |
| TiDB               | `prepared-plan-cache.capacity`                                                                                                                                                                                                                                                                                              | Deleted     | Replaced by the system variable `tidb_prepared_plan_cache_size`. This configuration item is no longer valid, if you want to modify the value, you need to modify the corresponding system variable.                                                                                                                |
| TiDB               | `prepared-plan-cache.enabled`                                                                                                                                                                                                                                                                                               | Deleted     | Replaced by the system variable `tidb_enable_prepared_plan_cache`. This configuration item is no longer valid, if you want to modify the value, you need to modify the corresponding system variable.                                                                                                              |
| TiDB               | `query-log-max-len`                                                                                                                                                                                                                                                                                                         | Deleted     | Replaced by the system variable `tidb_query_log_max_len`. This configuration item is no longer valid, if you want to modify the value, you need to modify the corresponding system variable.                                                                                                                       |
| TiDB               | `require-secure-transport`                                                                                                                                                                                                                                                                                                  | Deleted     | Replaced by the system variable `require_secure_transport`. This configuration item is no longer valid, if you want to modify the value, you need to modify the corresponding system variable.                                                                                                                     |
| TiDB               | `run-auto-analyze`                                                                                                                                                                                                                                                                                                          | Deleted     | Replaced by the system variable `tidb_enable_auto_analyze`. This configuration item is no longer valid, if you want to modify the value, you need to modify the corresponding system variable.                                                                                                                     |
| TiDB               | [<a href="/tidb-configuration-file.md#enable-global-kill-new-in-v610">`enable-global-kill`</a>](/tidb-configuration-file.md#enable-global-kill-new-in-v610)                                                                                                                                                                 | Newly added | Controls whether to enable the Global Kill (terminating queries or connections across instances) feature. When the value is `true`, both `KILL` and `KILL TIDB` statements can terminate queries or connections across instances so you do not need to worry about erroneously terminating queries or connections. |
| TiDB               | [<a href="/tidb-configuration-file.md#enable-stats-cache-mem-quota-new-in-v610">`enable-stats-cache-mem-quota`</a>](/tidb-configuration-file.md#enable-stats-cache-mem-quota-new-in-v610)                                                                                                                                   | Newly added | Controls whether to enable the memory quota for the statistics cache.                                                                                                                                                                                                                                              |
| TiKV               | [<a href="/tikv-configuration-file.md#enable-1">`raft-engine.enable`</a>](/tikv-configuration-file.md#enable-1)                                                                                                                                                                                                             | Modified    | The default value is changed from `FALSE` to `TRUE`.                                                                                                                                                                                                                                                               |
| TiKV               | [<a href="/tikv-configuration-file.md#region-max-keys">`region-max-keys`</a>](/tikv-configuration-file.md#region-max-keys)                                                                                                                                                                                                  | Modified    | The default value is changed from 1440000 to `region-split-keys / 2 * 3`.                                                                                                                                                                                                                                          |
| TiKV               | [<a href="/tikv-configuration-file.md#region-max-size">`region-max-size`</a>](/tikv-configuration-file.md#region-max-size)                                                                                                                                                                                                  | Modified    | The default value is changed from 144 MB to `region-split-size / 2 * 3`.                                                                                                                                                                                                                                           |
| TiKV               | [<a href="/tikv-configuration-file.md#enable-region-bucket-new-in-v610">`coprocessor.enable-region-bucket`</a>](/tikv-configuration-file.md#enable-region-bucket-new-in-v610)                                                                                                                                               | Newly added | Determines whether to divide a Region into smaller ranges called buckets.                                                                                                                                                                                                                                          |
| TiKV               | [<a href="/tikv-configuration-file.md#region-bucket-size-new-in-v610">`coprocessor.region-bucket-size`</a>](/tikv-configuration-file.md#region-bucket-size-new-in-v610)                                                                                                                                                     | Newly added | The size of a bucket when `enable-region-bucket` is true.                                                                                                                                                                                                                                                          |
| TiKV               | [<a href="/tikv-configuration-file.md#renew-batch-min-size">`causal-ts.renew-batch-min-size`</a>](/tikv-configuration-file.md#renew-batch-min-size)                                                                                                                                                                         | Newly added | The minimum number of locally cached timestamps.                                                                                                                                                                                                                                                                   |
| TiKV               | [<a href="/tikv-configuration-file.md#renew-interval">`causal-ts.renew-interval`</a>](/tikv-configuration-file.md#renew-interval)                                                                                                                                                                                           | Newly added | The interval at which the locally cached timestamps are refreshed.                                                                                                                                                                                                                                                 |
| TiKV               | [<a href="/tikv-configuration-file.md#max-snapshot-file-raw-size-new-in-v610">`max-snapshot-file-raw-size`</a>](/tikv-configuration-file.md#max-snapshot-file-raw-size-new-in-v610)                                                                                                                                         | Newly added | The snapshot file will split to multiple files when the snapshot file size exceeds this value.                                                                                                                                                                                                                     |
| TiKV               | [<a href="/tikv-configuration-file.md#memory-limit">`raft-engine.memory-limit`</a>](/tikv-configuration-file.md#memory-limit)                                                                                                                                                                                               | Newly added | Specifies the limit on the memory usage of Raft Engine.                                                                                                                                                                                                                                                            |
| TiKV               | [<a href="/tikv-configuration-file.md#background-error-recovery-window-new-in-v610">`storage.background-error-recovery-window`</a>](/tikv-configuration-file.md#background-error-recovery-window-new-in-v610)                                                                                                               | Newly added | The maximum recovery time is allowed after RocksDB detects a recoverable background error.                                                                                                                                                                                                                         |
| TiKV               | [<a href="/tikv-configuration-file.md#api-version-new-in-v610">`storage.api-version`</a>](/tikv-configuration-file.md#api-version-new-in-v610)                                                                                                                                                                              | Newly added | The storage format and interface version used by TiKV when TiKV serves as the raw key-value store.                                                                                                                                                                                                                 |
| PD                 | [<a href="/pd-configuration-file.md#max-store-preparing-time-new-in-v610">`schedule.max-store-preparing-time`</a>](/pd-configuration-file.md#max-store-preparing-time-new-in-v610)                                                                                                                                          | Newly added | Controls the maximum waiting time for the store to go online.                                                                                                                                                                                                                                                      |
| TiCDC              | [<a href="/ticdc/manage-ticdc.md#configure-sink-uri-with-kafka">`enable-tls`</a>](/ticdc/manage-ticdc.md#configure-sink-uri-with-kafka)                                                                                                                                                                                     | Newly added | Whether to use TLS to connect to the downstream Kafka instance.                                                                                                                                                                                                                                                    |
| TiCDC              | `sasl-gssapi-user`<br/>`sasl-gssapi-password`<br/>`sasl-gssapi-auth-type`<br/>`sasl-gssapi-service-name`<br/>`sasl-gssapi-realm`<br/>`sasl-gssapi-key-tab-path`<br/>`sasl-gssapi-kerberos-config-path`                                                                                                                      | Newly added | Used to support SASL/GSSAPI authentication for Kafka. For details, see [<a href="/ticdc/manage-ticdc.md#configure-sink-uri-with-kafka">Configure sink URI with `kafka`</a>](/ticdc/manage-ticdc.md#configure-sink-uri-with-kafka).                                                                                 |
| TiCDC              | [<a href="/ticdc/manage-ticdc.md#configure-sink-uri-with-kafka">`avro-decimal-handling-mode`</a>](/ticdc/manage-ticdc.md#configure-sink-uri-with-kafka)<br/>[<a href="/ticdc/manage-ticdc.md#configure-sink-uri-with-kafka">`avro-bigint-unsigned-handling-mode`</a>](/ticdc/manage-ticdc.md#configure-sink-uri-with-kafka) | Newly added | Determines the output details of Avro format.                                                                                                                                                                                                                                                                      |
| TiCDC              | [<a href="/ticdc/manage-ticdc.md#customize-the-rules-for-topic-and-partition-dispatchers-of-kafka-sink">`dispatchers.topic`</a>](/ticdc/manage-ticdc.md#customize-the-rules-for-topic-and-partition-dispatchers-of-kafka-sink)                                                                                              | Newly added | Controls how TiCDC dispatches incremental data to different Kafka topics.                                                                                                                                                                                                                                          |
| TiCDC              | [<a href="/ticdc/manage-ticdc.md#customize-the-rules-for-topic-and-partition-dispatchers-of-kafka-sink">`dispatchers.partition`</a>](/ticdc/manage-ticdc.md#customize-the-rules-for-topic-and-partition-dispatchers-of-kafka-sink)                                                                                          | Newly added | `dispatchers.partition` is an alias for `dispatchers.dispatcher`. Controls how TiCDC dispatches incremental data to Kafka partitions.                                                                                                                                                                              |
| TiCDC              | [<a href="/ticdc/manage-ticdc.md#integrate-ticdc-with-kafka-connect-confluent-platform">`schema-registry`</a>](/ticdc/manage-ticdc.md#integrate-ticdc-with-kafka-connect-confluent-platform)                                                                                                                                | Newly added | Specifies the schema registry endpoint that stores Avro schema.                                                                                                                                                                                                                                                    |
| DM                 | `worker` in the `dmctl start-relay` command                                                                                                                                                                                                                                                                                 | Deleted     | This parameter is not recommended for use. Will provide a simpler implementation.                                                                                                                                                                                                                                  |
| DM                 | `relay-dir` in the source configuration file                                                                                                                                                                                                                                                                                | Deleted     | Replaced by the same configuration item in the worker configuration file.                                                                                                                                                                                                                                          |
| DM                 | `is-sharding` in the task configuration file                                                                                                                                                                                                                                                                                | Deleted     | Replaced by the `shard-mode` configuration item.                                                                                                                                                                                                                                                                   |
| DM                 | `auto-fix-gtid` in the task configuration file                                                                                                                                                                                                                                                                              | Deleted     | Deprecated in v5.x and officially deleted in v6.1.0.                                                                                                                                                                                                                                                               |
| DM                 | `meta-dir` and `charset` in the source configuration file                                                                                                                                                                                                                                                                   | Deleted     | Deprecated in v5.x and officially deleted in v6.1.0.                                                                                                                                                                                                                                                               |

### Others {#others}

-   Enable Prepared Plan Cache by default

    Prepared Plan Cache is enabled by default in new clusters to cache the execution plans for `Prepare` / `Execute` requests. In the subsequent execution, query plan optimization can be skipped and thus leads to a performance boost. Upgraded clusters inherit the configuration from the configuration file. New clusters use the new default values, which means Prepared Plan Cache is enabled by default and each session can cache 100 plans at most (`capacity=100`). For the memory consumption of this feature, see [<a href="/sql-prepared-plan-cache.md#memory-management-of-prepared-plan-cache">memory management of Prepared Plan Cache</a>](/sql-prepared-plan-cache.md#memory-management-of-prepared-plan-cache).

-   Prior to TiDB v6.1.0, `SHOW ANALYZE STATUS` shows instance-level tasks and the task records are cleared after TiDB restarts. Since TiDB v6.1.0, `SHOW ANALYZE STATUS` shows cluster-level tasks, and the task records persist after the restart. When `tidb_analyze_version = 2`, the `Job_info` column adds the `analyze option` information.

-   Damaged SST files in TiKV might cause the TiKV process to panic. Before TiDB v6.1.0, damaged SST files caused TiKV to panic immediately. Since TiDB v6.1.0, the TiKV process will panic 1 hour after SST files are damaged.

-   The following TiKV configuration items support [<a href="/dynamic-config.md#modify-tikv-configuration-dynamically">modifying values dynamically</a>](/dynamic-config.md#modify-tikv-configuration-dynamically):

    -   `raftstore.raft-entry-max-size`
    -   `quota.foreground-cpu-time`
    -   `quota.foreground-write-bandwidth`
    -   `quota.foreground-read-bandwidth`
    -   `quota.max-delay-duration`
    -   `server.grpc-memory-pool-quota`
    -   `server.max-grpc-send-msg-len`
    -   `server.raft-msg-max-batch-size`

-   In v6.1.0, some configuration file parameters are converted to system variables. For v6.1.0 clusters upgraded (including online and offline upgrades ) from earlier versions, note that:

    -   If the configuration items specified in the configuration file before the upgrade already exist, TiDB will automatically update the values of the configured items to those of the corresponding system variables during the upgrade process. In this way, after the upgrade, the system behavior does not change thanks to parameter optimization.
    -   The automatic update mentioned above occurs only once during the upgrade. After the upgrade, the deprecated configuration items are no longer effective.

-   The Dashboard page is removed from DM WebUI.

-   When `dispatchers.topic` and `dispatchers.partition` are enabled, TiCDC cannot be downgraded to versions earlier than v6.1.0.

-   TiCDC Changefeed using the Avro protocol cannot be downgraded to versions earlier than v6.1.0.

## Improvements {#improvements}

-   TiDB

    -   Improve the performance of the `UnionScanRead` operator [<a href="https://github.com/pingcap/tidb/issues/32433">#32433</a>](https://github.com/pingcap/tidb/issues/32433)
    -   Improve the display of task types in the output of `EXPLAIN` (add the MPP task type) [<a href="https://github.com/pingcap/tidb/issues/33332">#33332</a>](https://github.com/pingcap/tidb/issues/33332)
    -   Support using `rand()` as the default value of a column [<a href="https://github.com/pingcap/tidb/issues/10377">#10377</a>](https://github.com/pingcap/tidb/issues/10377)
    -   Support using `uuid()` as the default value of a column [<a href="https://github.com/pingcap/tidb/issues/33870">#33870</a>](https://github.com/pingcap/tidb/issues/33870)
    -   Support modifying the character set of columns from `latin1` to `utf8`/`utf8mb4` [<a href="https://github.com/pingcap/tidb/issues/34008">#34008</a>](https://github.com/pingcap/tidb/issues/34008)

-   TiKV

    -   Improve the old value hit rate of CDC when using in-memory pessimistic lock [<a href="https://github.com/tikv/tikv/issues/12279">#12279</a>](https://github.com/tikv/tikv/issues/12279)
    -   Improve the health check to detect unavailable Raftstore, so that the TiKV client can update Region Cache in time [<a href="https://github.com/tikv/tikv/issues/12398">#12398</a>](https://github.com/tikv/tikv/issues/12398)
    -   Support setting memory limit on Raft Engine [<a href="https://github.com/tikv/tikv/issues/12255">#12255</a>](https://github.com/tikv/tikv/issues/12255)
    -   TiKV automatically detects and deletes the damaged SST files to improve the product availability [<a href="https://github.com/tikv/tikv/issues/10578">#10578</a>](https://github.com/tikv/tikv/issues/10578)
    -   CDC supports RawKV [<a href="https://github.com/tikv/tikv/issues/11965">#11965</a>](https://github.com/tikv/tikv/issues/11965)
    -   Support splitting a large snapshot file into multiple files [<a href="https://github.com/tikv/tikv/issues/11595">#11595</a>](https://github.com/tikv/tikv/issues/11595)
    -   Move the snapshot garbage collection from Raftstore to background thread to prevent snapshot GC from blocking Raftstore message loops [<a href="https://github.com/tikv/tikv/issues/11966">#11966</a>](https://github.com/tikv/tikv/issues/11966)
    -   Support dynamic setting of the the maximum message length (`max-grpc-send-msg-len`) and the maximum batch size of gPRC messages (`raft-msg-max-batch-size`) [<a href="https://github.com/tikv/tikv/issues/12334">#12334</a>](https://github.com/tikv/tikv/issues/12334)
    -   Support executing online unsafe recovery plan through Raft [<a href="https://github.com/tikv/tikv/issues/10483">#10483</a>](https://github.com/tikv/tikv/issues/10483)

-   PD
    -   Support time-to-live (TTL) for region labels [<a href="https://github.com/tikv/pd/issues/4694">#4694</a>](https://github.com/tikv/pd/issues/4694)
    -   Support Region Buckets [<a href="https://github.com/tikv/pd/issues/4668">#4668</a>](https://github.com/tikv/pd/issues/4668)
    -   Disable compiling swagger server by default [<a href="https://github.com/tikv/pd/issues/4932">#4932</a>](https://github.com/tikv/pd/issues/4932)

-   TiFlash

    -   Optimize memory calculation for an aggregate operator so that a more efficient algorithm is used in the merge phase [<a href="https://github.com/pingcap/tiflash/issues/4451">#4451</a>](https://github.com/pingcap/tiflash/issues/4451)

-   Tools

    -   Backup &#x26; Restore (BR)

        -   Support backing up and restoring empty databases [<a href="https://github.com/pingcap/tidb/issues/33866">#33866</a>](https://github.com/pingcap/tidb/issues/33866)

    -   TiDB Lightning

        -   Optimize Scatter Region to batch mode to improve the stability of the Scatter Region process [<a href="https://github.com/pingcap/tidb/issues/33618">#33618</a>](https://github.com/pingcap/tidb/issues/33618)

    -   TiCDC

        -   TiCDC supports splitting large transactions during replication, which significantly reduces replication latency caused by large transactions [<a href="https://github.com/pingcap/tiflow/issues/5280">#5280</a>](https://github.com/pingcap/tiflow/issues/5280)

## Bug fixes {#bug-fixes}

-   TiDB

    -   Fix the issue of possible panic that might occur when the `in` function processes the `bit` type data [<a href="https://github.com/pingcap/tidb/issues/33070">#33070</a>](https://github.com/pingcap/tidb/issues/33070)
    -   Fix the issue of wrong query result because the `UnionScan` operator cannot maintain the order [<a href="https://github.com/pingcap/tidb/issues/33175">#33175</a>](https://github.com/pingcap/tidb/issues/33175)
    -   Fix the issue that the Merge Join operator gets wrong results in certain cases [<a href="https://github.com/pingcap/tidb/issues/33042">#33042</a>](https://github.com/pingcap/tidb/issues/33042)
    -   Fix the issue that the `index join` result might be wrong in the dynamic pruning mode [<a href="https://github.com/pingcap/tidb/issues/33231">#33231</a>](https://github.com/pingcap/tidb/issues/33231)
    -   Fix the issue that data might not be garbage-collected when some partitions of a partitioned table is dropped [<a href="https://github.com/pingcap/tidb/issues/33620">#33620</a>](https://github.com/pingcap/tidb/issues/33620)
    -   Fix the issue that some DDL statements might be stuck for a period after the PD node of a cluster is replaced [<a href="https://github.com/pingcap/tidb/issues/33908">#33908</a>](https://github.com/pingcap/tidb/issues/33908)
    -   Fix the issue that the TiDB server might run out of memory when the `INFORMATION_SCHEMA.CLUSTER_SLOW_QUERY` table is queried. This issue can be triggered when you check slow queries on the Grafana dashboard [<a href="https://github.com/pingcap/tidb/issues/33893">#33893</a>](https://github.com/pingcap/tidb/issues/33893)
    -   Fix the issue that the system variable `max_allowed_packet` does not take effect [<a href="https://github.com/pingcap/tidb/issues/31422">#31422</a>](https://github.com/pingcap/tidb/issues/31422)
    -   Fix the issue of memory leak in the TopSQL module [<a href="https://github.com/pingcap/tidb/issues/34525">#34525</a>](https://github.com/pingcap/tidb/issues/34525) [<a href="https://github.com/pingcap/tidb/issues/34502">#34502</a>](https://github.com/pingcap/tidb/issues/34502)
    -   Fix the issue that the Plan Cache might be wrong on the PointGet plan [<a href="https://github.com/pingcap/tidb/issues/32371">#32371</a>](https://github.com/pingcap/tidb/issues/32371)
    -   Fix the issue that query result might be wrong when Plan Cache is started in the RC isolation level [<a href="https://github.com/pingcap/tidb/issues/34447">#34447</a>](https://github.com/pingcap/tidb/issues/34447)

-   TiKV

    -   Fix the issue that the Raft log lag is increasing when a TiKV instance is taken offline [<a href="https://github.com/tikv/tikv/issues/12161">#12161</a>](https://github.com/tikv/tikv/issues/12161)
    -   Fix the issue that TiKV panics and destroys peers unexpectedly because the target Region to be merged is invalid [<a href="https://github.com/tikv/tikv/issues/12232">#12232</a>](https://github.com/tikv/tikv/issues/12232)
    -   Fix the issue that TiKV reports the `failed to load_latest_options` error when upgrading from v5.3.1 or v5.4.0 to v6.0.0 or later versions [<a href="https://github.com/tikv/tikv/issues/12269">#12269</a>](https://github.com/tikv/tikv/issues/12269)
    -   Fix the issue of OOM caused by appending Raft logs when the memory resource is insufficient [<a href="https://github.com/tikv/tikv/issues/11379">#11379</a>](https://github.com/tikv/tikv/issues/11379)
    -   Fix the issue of TiKV panic caused by the race between destroying peers and batch splitting Regions [<a href="https://github.com/tikv/tikv/issues/12368">#12368</a>](https://github.com/tikv/tikv/issues/12368)
    -   Fix the issue of TiKV memory usage spike in a short time after `stats_monitor` falls into a dead loop [<a href="https://github.com/tikv/tikv/issues/12416">#12416</a>](https://github.com/tikv/tikv/issues/12416)
    -   Fix the issue that TiKV reports the `invalid store ID 0` error when using Follower Read [<a href="https://github.com/tikv/tikv/issues/12478">#12478</a>](https://github.com/tikv/tikv/issues/12478)

-   PD

    -   Fix the wrong status code of `not leader` [<a href="https://github.com/tikv/pd/issues/4797">#4797</a>](https://github.com/tikv/pd/issues/4797)
    -   Fix a bug of TSO fallback in some corner cases [<a href="https://github.com/tikv/pd/issues/4884">#4884</a>](https://github.com/tikv/pd/issues/4884)
    -   Fix the issue that a removed tombstone store appears again after the PD leader transfer ​​[<a href="https://github.com/tikv/pd/issues/4941">#4941</a>](https://github.com/tikv/pd/issues/4941)
    -   Fix the issue that scheduling cannot start immediately after the PD leader transfer [<a href="https://github.com/tikv/pd/issues/4769">#4769</a>](https://github.com/tikv/pd/issues/4769)

-   TiDB Dashboard

    -   Fix a bug that Top SQL cannot collect the CPU overhead of the SQL statements that were running before the Top SQL feature is enabled [<a href="https://github.com/pingcap/tidb/issues/33859">#33859</a>](https://github.com/pingcap/tidb/issues/33859)

-   TiFlash

    -   Fix potential data inconsistency after a lot of INSERT and DELETE operations [<a href="https://github.com/pingcap/tiflash/issues/4956">#4956</a>](https://github.com/pingcap/tiflash/issues/4956)

-   Tools

    -   TiCDC

        -   Fix excessive memory usage by optimizing the way DDL schemas are buffered [<a href="https://github.com/pingcap/tiflow/issues/1386">#1386</a>](https://github.com/pingcap/tiflow/issues/1386)
        -   Fix data loss that occurs in special incremental scanning scenarios [<a href="https://github.com/pingcap/tiflow/issues/5468">#5468</a>](https://github.com/pingcap/tiflow/issues/5468)

    -   TiDB Data Migration (DM)

        -   Fix the `start-time` time zone issue and change DM behavior from using the downstream time zone to using the upstream time zone [<a href="https://github.com/pingcap/tiflow/issues/5471">#5271</a>](https://github.com/pingcap/tiflow/issues/5471)
        -   Fix the issue that DM occupies more disk space after the task automatically resumes [<a href="https://github.com/pingcap/tiflow/issues/3734">#3734</a>](https://github.com/pingcap/tiflow/issues/3734) [<a href="https://github.com/pingcap/tiflow/issues/5344">#5344</a>](https://github.com/pingcap/tiflow/issues/5344)
        -   Fix the problem that checkpoint flush may cause the data of failed rows to be skipped [<a href="https://github.com/pingcap/tiflow/issues/5279">#5279</a>](https://github.com/pingcap/tiflow/issues/5279)
        -   Fix the issue that in some cases manually executing the filtered DDL in the downstream might cause task resumption failure [<a href="https://github.com/pingcap/tiflow/issues/5272">#5272</a>](https://github.com/pingcap/tiflow/issues/5272)
        -   Fix an issue that the uppercase table cannot be replicated when `case-sensitive: true` is not set [<a href="https://github.com/pingcap/tiflow/issues/5255">#5255</a>](https://github.com/pingcap/tiflow/issues/5255)
        -   Fix the DM worker panic issue that occurs when the primary key is not first in the index returned by the `SHOW CREATE TABLE` statement [<a href="https://github.com/pingcap/tiflow/issues/5159">#5159</a>](https://github.com/pingcap/tiflow/issues/5159)
        -   Fix the issue that CPU usage may increase and a large amount of log is printed when GTID is enabled or when the task is automatically resumed [<a href="https://github.com/pingcap/tiflow/issues/5063">#5063</a>](https://github.com/pingcap/tiflow/issues/5063)
        -   Fix the offline option and other usage issues in DM WebUI [<a href="https://github.com/pingcap/tiflow/issues/4993">#4993</a>](https://github.com/pingcap/tiflow/issues/4993)
        -   Fix the issue that incremental tasks fail to start when GTID is empty in the upstream [<a href="https://github.com/pingcap/tiflow/issues/3731">#3731</a>](https://github.com/pingcap/tiflow/issues/3731)
        -   Fix the issue that empty configurations may cause dm-master to panic [<a href="https://github.com/pingcap/tiflow/issues/3732">#3732</a>](https://github.com/pingcap/tiflow/issues/3732)

    -   TiDB Lightning

        -   Fix the issue that the precheck does not check local disk resources and cluster availability [<a href="https://github.com/pingcap/tidb/issues/34213">#34213</a>](https://github.com/pingcap/tidb/issues/34213)
        -   Fix the issue of incorrect routing for schemas [<a href="https://github.com/pingcap/tidb/issues/33381">#33381</a>](https://github.com/pingcap/tidb/issues/33381)
        -   Fix the issue that the PD configuration is not restored correctly when TiDB Lightning panics [<a href="https://github.com/pingcap/tidb/issues/31733">#31733</a>](https://github.com/pingcap/tidb/issues/31733)
        -   Fix the issue of Local-backend import failure caused by out-of-bounds data in the `auto_increment` column [<a href="https://github.com/pingcap/tidb/issues/27937">#29737</a>](https://github.com/pingcap/tidb/issues/27937)
        -   Fix the issue of local backend import failure when the `auto_random` or `auto_increment` column is null [<a href="https://github.com/pingcap/tidb/issues/34208">#34208</a>](https://github.com/pingcap/tidb/issues/34208)
