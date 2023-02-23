---
title: TiKV Configuration File
summary: Learn the TiKV configuration file.
---

# TiKV Configuration File {#tikv-configuration-file}

<!-- markdownlint-disable MD001 -->

The TiKV configuration file supports more options than command-line parameters. You can find the default configuration file in [etc/config-template.toml](https://github.com/tikv/tikv/blob/master/etc/config-template.toml) and rename it to `config.toml`.

This document only describes the parameters that are not included in command-line parameters. For more details, see [command-line parameter](/command-line-flags-for-tikv-configuration.md).

## Global configuration {#global-configuration}

### <code>abort-on-panic</code> {#code-abort-on-panic-code}

-   Sets whether to call `abort()` to exit the process when TiKV panics. This option affects whether TiKV allows the system to generate core dump files.

    -   If the value of this configuration item is `false`, when TiKV panics, it calls `exit()` to exit the process.
    -   If the value of this configuration item is `true`, when TiKV panics, TiKV calls `abort()` to exit the process. At this time, TiKV allows the system to generate core dump files when exiting. To generate the core dump file, you also need to perform the system configuration related to core dump (for example, setting the size limit of the core dump file via `ulimit -c` command, and configure the core dump path. Different operating systems have different related configurations). To avoid the core dump files occupying too much disk space and causing insufficient TiKV disk space, it is recommended to set the core dump generation path to a disk partition different to that of TiKV data.

-   Default value: `false`

### <code>slow-log-file</code> {#code-slow-log-file-code}

-   The file that stores slow logs
-   If this configuration item is not set, but `log.file.filename` is set, slow logs are output to the log file specified by `log.file.filename`.
-   If neither `slow-log-file` nor `log.file.filename` are set, all logs are output to "stderr" by default.
-   If both configuration items are set, ordinary logs are output to the log file specified by `log.file.filename`, and slow logs are output to the log file set by `slow-log-file`.
-   Default value: `""`

### <code>slow-log-threshold</code> {#code-slow-log-threshold-code}

-   The threshold for outputing slow logs. If the processing time is longer than this threshold, slow logs are output.
-   Default value: `"1s"`

## log <span class="version-mark">New in v5.4.0</span> {#log-span-class-version-mark-new-in-v5-4-0-span}

-   Configuration items related to the log.

-   From v5.4.0, to make the log configuration items of TiKV and TiDB consistent, TiKV deprecates the former configuration item `log-rotation-timespan` and changes `log-level`, `log-format`, `log-file`, `log-rotation-size` to the following ones. If you only set the old configuration items, and their values are set to non-default values, the old items remain compatible with the new items. If both old and new configuration items are set, the new items take effect.

### <code>level</code> <span class="version-mark">New in v5.4.0</span> {#code-level-code-span-class-version-mark-new-in-v5-4-0-span}

-   The log level
-   Optional values: `"debug"`, `"info"`, `"warn"`, `"error"`, `"fatal"`
-   Default value: `"info"`

### <code>format</code> <span class="version-mark">New in v5.4.0</span> {#code-format-code-span-class-version-mark-new-in-v5-4-0-span}

-   The log format
-   Optional values: `"json"`, `"text"`
-   Default value: `"text"`

### <code>enable-timestamp</code> <span class="version-mark">New in v5.4.0</span> {#code-enable-timestamp-code-span-class-version-mark-new-in-v5-4-0-span}

-   Determines whether to enable or disable the timestamp in the log
-   Optional values: `true`, `false`
-   Default value: `true`

## log.file <span class="version-mark">New in v5.4.0</span> {#log-file-span-class-version-mark-new-in-v5-4-0-span}

-   Configuration items related to the log file.

### <code>filename</code> <span class="version-mark">New in v5.4.0</span> {#code-filename-code-span-class-version-mark-new-in-v5-4-0-span}

-   The log file. If this configuration item is not set, logs are output to "stderr" by default. If this configuration item is set, logs are output to the corresponding file.
-   Default value: `""`

### <code>max-size</code> <span class="version-mark">New in v5.4.0</span> {#code-max-size-code-span-class-version-mark-new-in-v5-4-0-span}

-   The maximum size of a single log file. When the file size is larger than the value set by this configuration item, the system automatically splits the single file into multiple files.
-   Default value: `300`
-   Maximum value: `4096`
-   Unit: MiB

### <code>max-days</code> <span class="version-mark">New in v5.4.0</span> {#code-max-days-code-span-class-version-mark-new-in-v5-4-0-span}

-   The maximum number of days that TiKV keeps log files.
    -   If the configuration item is not set, or the value of it is set to the default value `0`, TiKV does not clean log files.
    -   If the parameter is set to a value other than `0`, TiKV cleans up the expired log files after `max-days`.
-   Default value: `0`

### <code>max-backups</code> <span class="version-mark">New in v5.4.0</span> {#code-max-backups-code-span-class-version-mark-new-in-v5-4-0-span}

-   The maximum number of log files that TiKV keeps.
    -   If the configuration item is not set, or the value of it is set to the default value `0`, TiKV keeps all log files.
    -   If the configuration item is set to a value other than `0`, TiKV keeps at most the number of old log files specified by `max-backups`. For example, if the value is set to `7`, TiKV keeps up to 7 old log files.
-   Default value: `0`

### <code>pd.enable-forwarding</code> <span class="version-mark">New in v5.0.0</span> {#code-pd-enable-forwarding-code-span-class-version-mark-new-in-v5-0-0-span}

-   Controls whether the PD client in TiKV forwards requests to the leader via the followers in the case of possible network isolation.
-   Default value: `false`
-   If the environment might have isolated network, enabling this parameter can reduce the window of service unavailability.
-   If you cannot accurately determine whether isolation, network interruption, or downtime has occurred, using this mechanism has the risk of misjudgment and causes reduced availability and performance. If network failure has never occurred, it is not recommended to enable this parameter.

## server {#server}

-   Configuration items related to the server.

### <code>status-thread-pool-size</code> {#code-status-thread-pool-size-code}

-   The number of worker threads for the `HTTP` API service
-   Default value: `1`
-   Minimum value: `1`

### <code>grpc-compression-type</code> {#code-grpc-compression-type-code}

-   The compression algorithm for gRPC messages
-   Optional values: `"none"`, `"deflate"`, `"gzip"`
-   Default value: `"none"`
-   Note: When the value is `gzip`, TiDB Dashboard will have a display error because it might not complete the corresponding compression algorithm in some cases. If you adjust the value back to the default `none`, TiDB Dashboard will display normally.

### <code>grpc-concurrency</code> {#code-grpc-concurrency-code}

-   The number of gRPC worker threads. When you modify the size of the gRPC thread pool, refer to [Performance tuning for TiKV thread pools](/tune-tikv-thread-performance.md#performance-tuning-for-tikv-thread-pools).
-   Default value: `5`
-   Minimum value: `1`

### <code>grpc-concurrent-stream</code> {#code-grpc-concurrent-stream-code}

-   The maximum number of concurrent requests allowed in a gRPC stream
-   Default value: `1024`
-   Minimum value: `1`

### <code>grpc-memory-pool-quota</code> {#code-grpc-memory-pool-quota-code}

-   Limits the memory size that can be used by gRPC
-   Default value: No limit
-   Limit the memory in case OOM is observed. Note that limit the usage can lead to potential stall

### <code>grpc-raft-conn-num</code> {#code-grpc-raft-conn-num-code}

-   The maximum number of links among TiKV nodes for Raft communication
-   Default value: `1`
-   Minimum value: `1`

### <code>max-grpc-send-msg-len</code> {#code-max-grpc-send-msg-len-code}

-   Sets the maximum length of a gRPC message that can be sent
-   Default value: `10485760`
-   Unit: Bytes
-   Maximum value: `2147483647`

### <code>grpc-stream-initial-window-size</code> {#code-grpc-stream-initial-window-size-code}

-   The window size of the gRPC stream
-   Default value: `2MB`
-   Unit: KB|MB|GB
-   Minimum value: `"1KB"`

### <code>grpc-keepalive-time</code> {#code-grpc-keepalive-time-code}

-   The time interval at which that gRPC sends `keepalive` Ping messages
-   Default value: `"10s"`
-   Minimum value: `"1s"`

### <code>grpc-keepalive-timeout</code> {#code-grpc-keepalive-timeout-code}

-   Disables the timeout for gRPC streams
-   Default value: `"3s"`
-   Minimum value: `"1s"`

### <code>concurrent-send-snap-limit</code> {#code-concurrent-send-snap-limit-code}

-   The maximum number of snapshots sent at the same time
-   Default value: `32`
-   Minimum value: `1`

### <code>concurrent-recv-snap-limit</code> {#code-concurrent-recv-snap-limit-code}

-   The maximum number of snapshots received at the same time
-   Default value: `32`
-   Minimum value: `1`

### <code>end-point-recursion-limit</code> {#code-end-point-recursion-limit-code}

-   The maximum number of recursive levels allowed when TiKV decodes the Coprocessor DAG expression
-   Default value: `1000`
-   Minimum value: `1`

### <code>end-point-request-max-handle-duration</code> {#code-end-point-request-max-handle-duration-code}

-   The longest duration allowed for a TiDB's push down request to TiKV for processing tasks
-   Default value: `"60s"`
-   Minimum value: `"1s"`

### <code>snap-max-write-bytes-per-sec</code> {#code-snap-max-write-bytes-per-sec-code}

-   The maximum allowable disk bandwidth when processing snapshots
-   Default value: `"100MB"`
-   Unit: KB|MB|GB
-   Minimum value: `"1KB"`

### <code>end-point-slow-log-threshold</code> {#code-end-point-slow-log-threshold-code}

-   The time threshold for a TiDB's push-down request to output slow log. If the processing time is longer than this threshold, the slow logs are output.
-   Default value: `"1s"`
-   Minimum value: `0`

### <code>raft-client-queue-size</code> {#code-raft-client-queue-size-code}

-   Specifies the queue size of the Raft messages in TiKV. If too many messages not sent in time result in a full buffer, or messages discarded, you can specify a greater value to improve system stability.
-   Default value: `8192`

### <code>forward-max-connections-per-address</code> <span class="version-mark">New in v5.0.0</span> {#code-forward-max-connections-per-address-code-span-class-version-mark-new-in-v5-0-0-span}

-   Sets the size of the connection pool for service and forwarding requests to the server. Setting it to too small a value affects the request latency and load balancing.
-   Default value: `4`

## readpool.unified {#readpool-unified}

Configuration items related to the single thread pool serving read requests. This thread pool supersedes the original storage thread pool and coprocessor thread pool since the 4.0 version.

### <code>min-thread-count</code> {#code-min-thread-count-code}

-   The minimal working thread count of the unified read pool
-   Default value: `1`

### <code>max-thread-count</code> {#code-max-thread-count-code}

-   The maximum working thread count of the unified read pool or the UnifyReadPool thread pool. When you modify the size of this thread pool, refer to [Performance tuning for TiKV thread pools](/tune-tikv-thread-performance.md#performance-tuning-for-tikv-thread-pools).
-   Default value: `MAX(4, CPU * 0.8)`

### <code>stack-size</code> {#code-stack-size-code}

-   The stack size of the threads in the unified thread pool
-   Type: Integer + Unit
-   Default value: `"10MB"`
-   Unit: KB|MB|GB
-   Minimum value: `"2MB"`
-   Maximum value: The number of Kbytes output in the result of the `ulimit -sH` command executed in the system.

### <code>max-tasks-per-worker</code> {#code-max-tasks-per-worker-code}

-   The maximum number of tasks allowed for a single thread in the unified read pool. `Server Is Busy` is returned when the value is exceeded.
-   Default value: `2000`
-   Minimum value: `2`

## readpool.storage {#readpool-storage}

Configuration items related to storage thread pool.

### <code>use-unified-pool</code> {#code-use-unified-pool-code}

-   Determines whether to use the unified thread pool (configured in [`readpool.unified`](#readpoolunified)) for storage requests. If the value of this parameter is `false`, a separate thread pool is used, which is configured through the rest parameters in this section (`readpool.storage`).
-   Default value: If this section (`readpool.storage`) has no other configurations, the default value is `true`. Otherwise, for the backward compatibility, the default value is `false`. Change the configuration in [`readpool.unified`](#readpoolunified) as needed before enabling this option.

### <code>high-concurrency</code> {#code-high-concurrency-code}

-   The allowable number of concurrent threads that handle high-priority `read` requests
-   When `8` ≤ `cpu num` ≤ `16`, the default value is `cpu_num * 0.5`; when `cpu num` is smaller than `8`, the default value is `4`; when `cpu num` is greater than `16`, the default value is `8`.
-   Minimum value: `1`

### <code>normal-concurrency</code> {#code-normal-concurrency-code}

-   The allowable number of concurrent threads that handle normal-priority `read` requests
-   When `8` ≤ `cpu num` ≤ `16`, the default value is `cpu_num * 0.5`; when `cpu num` is smaller than `8`, the default value is `4`; when `cpu num` is greater than `16`, the default value is `8`.
-   Minimum value: `1`

### <code>low-concurrency</code> {#code-low-concurrency-code}

-   The allowable number of concurrent threads that handle low-priority `read` requests
-   When `8` ≤ `cpu num` ≤ `16`, the default value is `cpu_num * 0.5`; when `cpu num` is smaller than `8`, the default value is `4`; when `cpu num` is greater than `16`, the default value is `8`.
-   Minimum value: `1`

### <code>max-tasks-per-worker-high</code> {#code-max-tasks-per-worker-high-code}

-   The maximum number of tasks allowed for a single thread in a high-priority thread pool. `Server Is Busy` is returned when the value is exceeded.
-   Default value: `2000`
-   Minimum value: `2`

### <code>max-tasks-per-worker-normal</code> {#code-max-tasks-per-worker-normal-code}

-   The maximum number of tasks allowed for a single thread in a normal-priority thread pool. `Server Is Busy` is returned when the value is exceeded.
-   Default value: `2000`
-   Minimum value: `2`

### <code>max-tasks-per-worker-low</code> {#code-max-tasks-per-worker-low-code}

-   The maximum number of tasks allowed for a single thread in a low-priority thread pool. `Server Is Busy` is returned when the value is exceeded.
-   Default value: `2000`
-   Minimum value: `2`

### <code>stack-size</code> {#code-stack-size-code}

-   The stack size of threads in the Storage read thread pool
-   Type: Integer + Unit
-   Default value: `"10MB"`
-   Unit: KB|MB|GB
-   Minimum value: `"2MB"`
-   Maximum value: The number of Kbytes output in the result of the `ulimit -sH` command executed in the system.

## <code>readpool.coprocessor</code> {#code-readpool-coprocessor-code}

Configuration items related to the Coprocessor thread pool.

### <code>use-unified-pool</code> {#code-use-unified-pool-code}

-   Determines whether to use the unified thread pool (configured in [`readpool.unified`](#readpoolunified)) for coprocessor requests. If the value of this parameter is `false`, a separate thread pool is used, which is configured through the rest parameters in this section (`readpool.coprocessor`).
-   Default value: If none of the parameters in this section (`readpool.coprocessor`) are set, the default value is `true`. Otherwise, the default value is `false` for the backward compatibility. Adjust the configuration items in [`readpool.unified`](#readpoolunified) before enabling this parameter.

### <code>high-concurrency</code> {#code-high-concurrency-code}

-   The allowable number of concurrent threads that handle high-priority Coprocessor requests, such as checkpoints
-   Default value: `CPU * 0.8`
-   Minimum value: `1`

### <code>normal-concurrency</code> {#code-normal-concurrency-code}

-   The allowable number of concurrent threads that handle normal-priority Coprocessor requests
-   Default value: `CPU * 0.8`
-   Minimum value: `1`

### <code>low-concurrency</code> {#code-low-concurrency-code}

-   The allowable number of concurrent threads that handle low-priority Coprocessor requests, such as table scan
-   Default value: `CPU * 0.8`
-   Minimum value: `1`

### <code>max-tasks-per-worker-high</code> {#code-max-tasks-per-worker-high-code}

-   The number of tasks allowed for a single thread in a high-priority thread pool. When this number is exceeded, `Server Is Busy` is returned.
-   Default value: `2000`
-   Minimum value: `2`

### <code>max-tasks-per-worker-normal</code> {#code-max-tasks-per-worker-normal-code}

-   The number of tasks allowed for a single thread in a normal-priority thread pool. When this number is exceeded, `Server Is Busy` is returned.
-   Default value: `2000`
-   Minimum value: `2`

### <code>max-tasks-per-worker-low</code> {#code-max-tasks-per-worker-low-code}

-   The number of tasks allowed for a single thread in a low-priority thread pool. When this number is exceeded, `Server Is Busy` is returned.
-   Default value: `2000`
-   Minimum value: `2`

### <code>stack-size</code> {#code-stack-size-code}

-   The stack size of the thread in the Coprocessor thread pool
-   Type: Integer + Unit
-   Default value: `"10MB"`
-   Unit: KB|MB|GB
-   Minimum value: `"2MB"`
-   Maximum value: The number of Kbytes output in the result of the `ulimit -sH` command executed in the system.

## storage {#storage}

Configuration items related to storage.

### <code>scheduler-concurrency</code> {#code-scheduler-concurrency-code}

-   A built-in memory lock mechanism to prevent simultaneous operations on a key. Each key has a hash in a different slot.
-   Default value: `524288`
-   Minimum value: `1`

### <code>scheduler-worker-pool-size</code> {#code-scheduler-worker-pool-size-code}

-   The number of `scheduler` threads, mainly used for checking transaction consistency before data writing. If the number of CPU cores is greater than or equal to `16`, the default value is `8`; otherwise, the default value is `4`. When you modify the size of the Scheduler thread pool, refer to [Performance tuning for TiKV thread pools](/tune-tikv-thread-performance.md#performance-tuning-for-tikv-thread-pools).
-   Default value: `4`
-   Minimum value: `1`

### <code>scheduler-pending-write-threshold</code> {#code-scheduler-pending-write-threshold-code}

-   The maximum size of the write queue. A `Server Is Busy` error is returned for a new write to TiKV when this value is exceeded.
-   Default value: `"100MB"`
-   Unit: MB|GB

### <code>reserve-space</code> {#code-reserve-space-code}

-   When TiKV is started, some space is reserved on the disk as disk protection. When the remaining disk space is less than the reserved space, TiKV restricts some write operations. The reserved space is divided into two parts: 80% of the reserved space is used as the extra disk space required for operations when the disk space is insufficient, and the other 20% is used to store the temporary file. In the process of reclaiming space, if the storage is exhausted by using too much extra disk space, this temporary file serves as the last protection for restoring services.
-   The name of the temporary file is `space_placeholder_file`, located in the `storage.data-dir` directory. When TiKV goes offline because its disk space ran out, if you restart TiKV, the temporary file is automatically deleted and TiKV tries to reclaim the space.
-   When the remaining space is insufficient, TiKV does not create the temporary file. The effectiveness of the protection is related to the size of the reserved space. The size of the reserved space is the larger value between 5% of the disk capacity and this configuration value. When the value of this configuration item is `"0MB"`, TiKV disables this disk protection feature.
-   Default value: `"5GB"`
-   Unit: MB|GB

### <code>enable-ttl</code> {#code-enable-ttl-code}

> **Warning:**
>
> -   Set `enable-ttl` to `true` or `false` **ONLY WHEN** deploying a new TiKV cluster. <strong>DO NOT</strong> modify the value of this configuration item in an existing TiKV cluster. TiKV clusters with different `enable-ttl` values use different data formats. Therefore, if you modify the value of this item in an existing TiKV cluster, the cluster will store data in different formats, which causes the "can't enable TTL on a non-ttl" error when you restart the TiKV cluster.
> -   Use `enable-ttl` **ONLY IN** a TiKV cluster. <strong>DO NOT</strong> use this configuration item in a cluster that has TiDB nodes (which means setting `enable-ttl` to `true` in such clusters). Otherwise, critical issues such as data corruption and the upgrade failure of TiDB clusters will occur.

-   TTL is short for "Time to live". If this item is enabled, TiKV automatically deletes data that reaches its TTL. To set the value of TTL, you need to specify it in the requests when writing data via the client. If the TTL is not specified, it means that TiKV does not automatically delete the corresponding data.
-   Default value: `false`

### <code>ttl-check-poll-interval</code> {#code-ttl-check-poll-interval-code}

-   The interval of checking data to reclaim physical spaces. If data reaches its TTL, TiKV forcibly reclaims its physical space during the check.
-   Default value: `"12h"`
-   Minimum value: `"0s"`

## storage.block-cache {#storage-block-cache}

Configuration items related to the sharing of block cache among multiple RocksDB Column Families (CF). When these configuration items are enabled, block cache separately configured for each column family is disabled.

### <code>shared</code> {#code-shared-code}

-   Enables or disables the sharing of block cache.
-   Default value: `true`

### <code>capacity</code> {#code-capacity-code}

-   The size of the shared block cache.
-   Default value: 45% of the size of total system memory
-   Unit: KB|MB|GB

## storage.flow-control {#storage-flow-control}

Configuration items related to the flow control mechanism in TiKV. This mechanism replaces the write stall mechanism in RocksDB and controls flow at the scheduler layer, which avoids secondary disasters caused by the stuck Raftstore or Apply threads.

### <code>enable</code> {#code-enable-code}

-   Determines whether to enable the flow control mechanism. After it is enabled, TiKV automatically disables the write stall mechanism of KvDB and the write stall mechanism of RaftDB (excluding memtable).
-   Default value: `true`

### <code>memtables-threshold</code> {#code-memtables-threshold-code}

-   When the number of kvDB memtables reaches this threshold, the flow control mechanism starts to work. When `enable` is set to `true`, this configuration item overrides `rocksdb.(defaultcf|writecf|lockcf).max-write-buffer-number`.
-   Default value: `5`

### <code>l0-files-threshold</code> {#code-l0-files-threshold-code}

-   When the number of kvDB L0 files reaches this threshold, the flow control mechanism starts to work. When `enable` is set to `true`, this configuration item overrides `rocksdb.(defaultcf|writecf|lockcf).level0-slowdown-writes-trigger`.
-   Default value: `20`

### <code>soft-pending-compaction-bytes-limit</code> {#code-soft-pending-compaction-bytes-limit-code}

-   When the pending compaction bytes in KvDB reach this threshold, the flow control mechanism starts to reject some write requests and reports the `ServerIsBusy` error. When `enable` is set to `true`, this configuration item overrides `rocksdb.(defaultcf|writecf|lockcf).soft-pending-compaction-bytes-limit`.
-   Default value: `"192GB"`

### <code>hard-pending-compaction-bytes-limit</code> {#code-hard-pending-compaction-bytes-limit-code}

-   When the pending compaction bytes in KvDB reach this threshold, the flow control mechanism rejects all write requests and reports the `ServerIsBusy` error. When `enable` is set to `true`, this configuration item overrides `rocksdb.(defaultcf|writecf|lockcf).hard-pending-compaction-bytes-limit`.
-   Default value: `"1024GB"`

## storage.io-rate-limit {#storage-io-rate-limit}

Configuration items related to the I/O rate limiter.

### <code>max-bytes-per-sec</code> {#code-max-bytes-per-sec-code}

-   Limits the maximum I/O bytes that a server can write to or read from the disk (determined by the `mode` configuration item below) in one second. When this limit is reached, TiKV prefers throttling background operations over foreground ones. The value of this configuration item should be set to the disk's optimal I/O bandwidth, for example, the maximum I/O bandwidth specified by your cloud disk vendor. When this configuration value is set to zero, disk I/O operations are not limited.
-   Default value: `"0MB"`

### <code>mode</code> {#code-mode-code}

-   Determines which types of I/O operations are counted and restrained below the `max-bytes-per-sec` threshold. Currently, only the write-only mode is supported.
-   Optional value: `"write-only"`
-   Default value: `"write-only"`

## raftstore {#raftstore}

Configuration items related to Raftstore.

### <code>prevote</code> {#code-prevote-code}

-   Enables or disables `prevote`. Enabling this feature helps reduce jitter on the system after recovery from network partition.
-   Default value: `true`

### <code>capacity</code> {#code-capacity-code}

-   The storage capacity, which is the maximum size allowed to store data. If `capacity` is left unspecified, the capacity of the current disk prevails. To deploy multiple TiKV instances on the same physical disk, add this parameter to the TiKV configuration. For details, see [Key parameters of the hybrid deployment](/hybrid-deployment-topology.md#key-parameters).
-   Default value: `0`

### <code>raftdb-path</code> {#code-raftdb-path-code}

-   The path to the Raft library, which is `storage.data-dir/raft` by default
-   Default value: `""`

### <code>raft-base-tick-interval</code> {#code-raft-base-tick-interval-code}

> **Note:**
>
> This configuration item cannot be queried via SQL statements but can be configured in the configuration file.

-   The time interval at which the Raft state machine ticks
-   Default value: `"1s"`
-   Minimum value: greater than `0`

### <code>raft-heartbeat-ticks</code> {#code-raft-heartbeat-ticks-code}

> **Note:**
>
> This configuration item cannot be queried via SQL statements but can be configured in the configuration file.

-   The number of passed ticks when the heartbeat is sent. This means that a heartbeat is sent at the time interval of `raft-base-tick-interval` * `raft-heartbeat-ticks`.
-   Default value: `2`
-   Minimum value: greater than `0`

### <code>raft-election-timeout-ticks</code> {#code-raft-election-timeout-ticks-code}

> **Note:**
>
> This configuration item cannot be queried via SQL statements but can be configured in the configuration file.

-   The number of passed ticks when Raft election is initiated. This means that if Raft group is missing the leader, a leader election is initiated approximately after the time interval of `raft-base-tick-interval` * `raft-election-timeout-ticks`.
-   Default value: `10`
-   Minimum value: `raft-heartbeat-ticks`

### <code>raft-min-election-timeout-ticks</code> {#code-raft-min-election-timeout-ticks-code}

> **Note:**
>
> This configuration item cannot be queried via SQL statements but can be configured in the configuration file.

-   The minimum number of ticks during which the Raft election is initiated. If the number is `0`, the value of `raft-election-timeout-ticks` is used. The value of this parameter must be greater than or equal to `raft-election-timeout-ticks`.
-   Default value: `0`
-   Minimum value: `0`

### <code>raft-max-election-timeout-ticks</code> {#code-raft-max-election-timeout-ticks-code}

> **Note:**
>
> This configuration item cannot be queried via SQL statements but can be configured in the configuration file.

-   The maximum number of ticks during which the Raft election is initiated. If the number is `0`, the value of `raft-election-timeout-ticks` * `2` is used.
-   Default value: `0`
-   Minimum value: `0`

### <code>raft-max-size-per-msg</code> {#code-raft-max-size-per-msg-code}

> **Note:**
>
> This configuration item cannot be queried via SQL statements but can be configured in the configuration file.

-   The soft limit on the size of a single message packet
-   Default value: `"1MB"`
-   Minimum value: `0`
-   Unit: MB

### <code>raft-max-inflight-msgs</code> {#code-raft-max-inflight-msgs-code}

> **Note:**
>
> This configuration item cannot be queried via SQL statements but can be configured in the configuration file.

-   The number of Raft logs to be confirmed. If this number is exceeded, log sending slows down.
-   Default value: `256`
-   Minimum value: greater than `0`

### <code>raft-entry-max-size</code> {#code-raft-entry-max-size-code}

-   The hard limit on the maximum size of a single log
-   Default value: `"8MB"`
-   Minimum value: `0`
-   Unit: MB|GB

### <code>raft-log-compact-sync-interval</code> <span class="version-mark">New in v5.3</span> {#code-raft-log-compact-sync-interval-code-span-class-version-mark-new-in-v5-3-span}

-   The time interval to compact unnecessary Raft logs
-   Default value: `"2s"`
-   Minimum value: `"0s"`

### <code>raft-log-gc-tick-interval</code> {#code-raft-log-gc-tick-interval-code}

-   The time interval at which the polling task of deleting Raft logs is scheduled. `0` means that this feature is disabled.
-   Default value: `"3s"`
-   Minimum value: `"0s"`

### <code>raft-log-gc-threshold</code> {#code-raft-log-gc-threshold-code}

-   The soft limit on the maximum allowable count of residual Raft logs
-   Default value: `50`
-   Minimum value: `1`

### <code>raft-log-gc-count-limit</code> {#code-raft-log-gc-count-limit-code}

-   The hard limit on the allowable number of residual Raft logs
-   Default value: the log number that can be accommodated in the 3/4 Region size (calculated as 1MB for each log)
-   Minimum value: `0`

### <code>raft-log-gc-size-limit</code> {#code-raft-log-gc-size-limit-code}

-   The hard limit on the allowable size of residual Raft logs
-   Default value: 3/4 of the Region size
-   Minimum value: greater than `0`

### <code>raft-log-reserve-max-ticks</code> <span class="version-mark">New in v5.3</span> {#code-raft-log-reserve-max-ticks-code-span-class-version-mark-new-in-v5-3-span}

-   After the number of ticks set by this configuration item passes, even if the number of residual Raft logs does not reach the value set by `raft-log-gc-threshold`, TiKV still performs garbage collection (GC) to these logs.
-   Default value: `6`
-   Minimum value: greater than `0`

### <code>raft-entry-cache-life-time</code> {#code-raft-entry-cache-life-time-code}

-   The maximum remaining time allowed for the log cache in memory.
-   Default value: `"30s"`
-   Minimum value: `0`

### <code>hibernate-regions</code> {#code-hibernate-regions-code}

-   Enables or disables Hibernate Region. When this option is enabled, a Region idle for a long time is automatically set as hibernated. This reduces the extra overhead caused by heartbeat messages between the Raft leader and the followers for idle Regions. You can use `peer-stale-state-check-interval` to modify the heartbeat interval between the leader and the followers of hibernated Regions.
-   Default value: `true` in v5.0.2 and later versions; `false` in versions before v5.0.2

### <code>split-region-check-tick-interval</code> {#code-split-region-check-tick-interval-code}

-   Specifies the interval at which to check whether the Region split is needed. `0` means that this feature is disabled.
-   Default value: `"10s"`
-   Minimum value: `0`

### <code>region-split-check-diff</code> {#code-region-split-check-diff-code}

-   The maximum value by which the Region data is allowed to exceed before Region split
-   Default value: 1/16 of the Region size.
-   Minimum value: `0`

### <code>region-compact-check-interval</code> {#code-region-compact-check-interval-code}

-   The time interval at which to check whether it is necessary to manually trigger RocksDB compaction. `0` means that this feature is disabled.
-   Default value: `"5m"`
-   Minimum value: `0`

### <code>region-compact-check-step</code> {#code-region-compact-check-step-code}

-   The number of Regions checked at one time for each round of manual compaction
-   Default value: `100`
-   Minimum value: `0`

### <code>region-compact-min-tombstones</code> {#code-region-compact-min-tombstones-code}

-   The number of tombstones required to trigger RocksDB compaction
-   Default value: `10000`
-   Minimum value: `0`

### <code>region-compact-tombstones-percent</code> {#code-region-compact-tombstones-percent-code}

-   The proportion of tombstone required to trigger RocksDB compaction
-   Default value: `30`
-   Minimum value: `1`
-   Maximum value: `100`

### <code>pd-heartbeat-tick-interval</code> {#code-pd-heartbeat-tick-interval-code}

-   The time interval at which a Region's heartbeat to PD is triggered. `0` means that this feature is disabled.
-   Default value: `"1m"`
-   Minimum value: `0`

### <code>pd-store-heartbeat-tick-interval</code> {#code-pd-store-heartbeat-tick-interval-code}

-   The time interval at which a store's heartbeat to PD is triggered. `0` means that this feature is disabled.
-   Default value: `"10s"`
-   Minimum value: `0`

### <code>snap-mgr-gc-tick-interval</code> {#code-snap-mgr-gc-tick-interval-code}

-   The time interval at which the recycle of expired snapshot files is triggered. `0` means that this feature is disabled.
-   Default value: `"1m"`
-   Minimum value: `0`

### <code>snap-gc-timeout</code> {#code-snap-gc-timeout-code}

-   The longest time for which a snapshot file is saved
-   Default value: `"4h"`
-   Minimum value: `0`

### <code>snap-generator-pool-size</code> <span class="version-mark">New in v5.4.0</span> {#code-snap-generator-pool-size-code-span-class-version-mark-new-in-v5-4-0-span}

-   Configures the size of the `snap-generator` thread pool.
-   To make Regions generate snapshot faster in TiKV in recovery scenarios, you need to increase the count of the `snap-generator` threads of the corresponding worker. You can use this configuration item to increase the size of the `snap-generator` thread pool.
-   Default value: `2`
-   Minimum value: `1`

### <code>lock-cf-compact-interval</code> {#code-lock-cf-compact-interval-code}

-   The time interval at which TiKV triggers a manual compaction for the Lock Column Family
-   Default value: `"10m"`
-   Minimum value: `0`

### <code>lock-cf-compact-bytes-threshold</code> {#code-lock-cf-compact-bytes-threshold-code}

-   The size out of which TiKV triggers a manual compaction for the Lock Column Family
-   Default value: `"256MB"`
-   Minimum value: `0`
-   Unit: MB

### <code>notify-capacity</code> {#code-notify-capacity-code}

-   The longest length of the Region message queue.
-   Default value: `40960`
-   Minimum value: `0`

### <code>messages-per-tick</code> {#code-messages-per-tick-code}

-   The maximum number of messages processed per batch
-   Default value: `4096`
-   Minimum value: `0`

### <code>max-peer-down-duration</code> {#code-max-peer-down-duration-code}

-   The longest inactive duration allowed for a peer. A peer with timeout is marked as `down`, and PD tries to delete it later.
-   Default value: `"10m"`
-   Minimum value: When Hibernate Region is enabled, the minimum value is `peer-stale-state-check-interval * 2`; when Hibernate Region is disabled, the minimum value is `0`.

### <code>max-leader-missing-duration</code> {#code-max-leader-missing-duration-code}

-   The longest duration allowed for a peer to be in the state where a Raft group is missing the leader. If this value is exceeded, the peer verifies with PD whether the peer has been deleted.
-   Default value: `"2h"`
-   Minimum value: greater than `abnormal-leader-missing-duration`

### <code>abnormal-leader-missing-duration</code> {#code-abnormal-leader-missing-duration-code}

-   The longest duration allowed for a peer to be in the state where a Raft group is missing the leader. If this value is exceeded, the peer is seen as abnormal and marked in metrics and logs.
-   Default value: `"10m"`
-   Minimum value: greater than `peer-stale-state-check-interval`

### <code>peer-stale-state-check-interval</code> {#code-peer-stale-state-check-interval-code}

-   The time interval to trigger the check for whether a peer is in the state where a Raft group is missing the leader.
-   Default value: `"5m"`
-   Minimum value: greater than `2 * election-timeout`

### <code>leader-transfer-max-log-lag</code> {#code-leader-transfer-max-log-lag-code}

-   The maximum number of missing logs allowed for the transferee during a Raft leader transfer
-   Default value: `128`
-   Minimum value: `10`

### <code>snap-apply-batch-size</code> {#code-snap-apply-batch-size-code}

-   The memory cache size required when the imported snapshot file is written into the disk
-   Default value: `"10MB"`
-   Minimum value: `0`
-   Unit: MB

### <code>consistency-check-interval</code> {#code-consistency-check-interval-code}

> **Warning:**
>
> It is **NOT** recommended to enable the consistency check in production environments, because it affects cluster performance and is incompatible with the garbage collection in TiDB.

-   The time interval at which the consistency check is triggered. `0` means that this feature is disabled.
-   Default value: `"0s"`
-   Minimum value: `0`

### <code>raft-store-max-leader-lease</code> {#code-raft-store-max-leader-lease-code}

-   The longest trusted period of a Raft leader
-   Default value: `"9s"`
-   Minimum value: `0`

### <code>merge-max-log-gap</code> {#code-merge-max-log-gap-code}

-   The maximum number of missing logs allowed when `merge` is performed
-   Default value: `10`
-   Minimum value: greater than `raft-log-gc-count-limit`

### <code>merge-check-tick-interval</code> {#code-merge-check-tick-interval-code}

-   The time interval at which TiKV checks whether a Region needs merge
-   Default value: `"2s"`
-   Minimum value: greater than `0`

### <code>use-delete-range</code> {#code-use-delete-range-code}

-   Determines whether to delete data from the `rocksdb delete_range` interface
-   Default value: `false`

### <code>cleanup-import-sst-interval</code> {#code-cleanup-import-sst-interval-code}

-   The time interval at which the expired SST file is checked. `0` means that this feature is disabled.
-   Default value: `"10m"`
-   Minimum value: `0`

### <code>local-read-batch-size</code> {#code-local-read-batch-size-code}

-   The maximum number of read requests processed in one batch
-   Default value: `1024`
-   Minimum value: greater than `0`

### <code>apply-max-batch-size</code> {#code-apply-max-batch-size-code}

-   The maximum number of requests for data flushing in one batch
-   Default value: `256`
-   Minimum value: greater than `0`

### <code>apply-pool-size</code> {#code-apply-pool-size-code}

-   The allowable number of threads in the pool that flushes data to storage. When you modify the size of this thread pool, refer to [Performance tuning for TiKV thread pools](/tune-tikv-thread-performance.md#performance-tuning-for-tikv-thread-pools).
-   Default value: `2`
-   Minimum value: greater than `0`

### <code>store-max-batch-size</code> {#code-store-max-batch-size-code}

-   The maximum number of requests processed in one batch
-   If `hibernate-regions` is enabled, the default value is `256`. If `hibernate-regions` is disabled, the default value is `1024`.
-   Minimum value: greater than `0`

### <code>store-pool-size</code> {#code-store-pool-size-code}

-   The allowable number of threads that process Raft, which is the size of the Raftstore thread pool. When you modify the size of this thread pool, refer to [Performance tuning for TiKV thread pools](/tune-tikv-thread-performance.md#performance-tuning-for-tikv-thread-pools).
-   Default value: `2`
-   Minimum value: greater than `0`

### <code>store-io-pool-size</code> <span class="version-mark">New in v5.3.0</span> {#code-store-io-pool-size-code-span-class-version-mark-new-in-v5-3-0-span}

-   The allowable number of threads that process Raft I/O tasks, which is the size of the StoreWriter thread pool. When you modify the size of this thread pool, refer to [Performance tuning for TiKV thread pools](/tune-tikv-thread-performance.md#performance-tuning-for-tikv-thread-pools).
-   Default value: `0`
-   Minimum value: `0`

### <code>future-poll-size</code> {#code-future-poll-size-code}

-   The allowable number of threads that drive `future`
-   Default value: `1`
-   Minimum value: greater than `0`

### <code>cmd-batch</code> {#code-cmd-batch-code}

-   Controls whether to enable batch processing of the requests. When it is enabled, the write performance is significantly improved.
-   Default value: `true`

### <code>inspect-interval</code> {#code-inspect-interval-code}

-   At a certain interval, TiKV inspects the latency of the Raftstore component. This parameter specifies the interval of the inspection. If the latency exceeds this value, this inspection is marked as timeout.
-   Judges whether the TiKV node is slow based on the ratio of timeout inspection.
-   Default value: `"500ms"`
-   Minimum value: `"1ms"`

### <code>raft-write-size-limit</code> <span class="version-mark">New in v5.3.0</span> {#code-raft-write-size-limit-code-span-class-version-mark-new-in-v5-3-0-span}

-   Determines the threshold at which Raft data is written into the disk. If the data size is larger than the value of this configuration item, the data is written to the disk. When the value of `store-io-pool-size` is `0`, this configuration item does not take effect.
-   Default value: `1MB`
-   Minimum value: `0`

## Coprocessor {#coprocessor}

Configuration items related to Coprocessor.

### <code>split-region-on-table</code> {#code-split-region-on-table-code}

-   Determines whether to split Region by table. It is recommended for you to use the feature only in TiDB mode.
-   Default value: `false`

### <code>batch-split-limit</code> {#code-batch-split-limit-code}

-   The threshold of Region split in batches. Increasing this value speeds up Region split.
-   Default value: `10`
-   Minimum value: `1`

### <code>region-max-size</code> {#code-region-max-size-code}

-   The maximum size of a Region. When the value is exceeded, the Region splits into many.
-   Default value: `"144MB"`
-   Unit: KB|MB|GB

### <code>region-split-size</code> {#code-region-split-size-code}

-   The size of the newly split Region. This value is an estimate.
-   Default value: `"96MB"`
-   Unit: KB|MB|GB

### <code>region-max-keys</code> {#code-region-max-keys-code}

-   The maximum allowable number of keys in a Region. When this value is exceeded, the Region splits into many.
-   Default value: `1440000`

### <code>region-split-keys</code> {#code-region-split-keys-code}

-   The number of keys in the newly split Region. This value is an estimate.
-   Default value: `960000`

## RocksDB {#rocksdb}

Configuration items related to RocksDB

### <code>max-background-jobs</code> {#code-max-background-jobs-code}

-   The number of background threads in RocksDB. When you modify the size of the RocksDB thread pool, refer to [Performance tuning for TiKV thread pools](/tune-tikv-thread-performance.md#performance-tuning-for-tikv-thread-pools).
-   Default value: `8`
-   Minimum value: `2`

### <code>max-background-flushes</code> {#code-max-background-flushes-code}

-   The maximum number of concurrent background memtable flush jobs
-   Default value: `2`
-   Minimum value: `1`

### <code>max-sub-compactions</code> {#code-max-sub-compactions-code}

-   The number of sub-compaction operations performed concurrently in RocksDB
-   Default value: `3`
-   Minimum value: `1`

### <code>max-open-files</code> {#code-max-open-files-code}

-   The total number of files that RocksDB can open
-   Default value: `40960`
-   Minimum value: `-1`

### <code>max-manifest-file-size</code> {#code-max-manifest-file-size-code}

-   The maximum size of a RocksDB Manifest file
-   Default value: `"128MB"`
-   Minimum value: `0`
-   Unit: B|KB|MB|GB

### <code>create-if-missing</code> {#code-create-if-missing-code}

-   Determines whether to automatically create a DB switch
-   Default value: `true`

### <code>wal-recovery-mode</code> {#code-wal-recovery-mode-code}

-   WAL recovery mode
-   Optional values:
    -   `"tolerate-corrupted-tail-records"`: tolerates and discards the records that have incomplete trailing data on all logs
    -   `"absolute-consistency"`: abandons recovery when corrupted logs are found
    -   `"point-in-time"`: recovers logs sequentially until the first corrupted log is encountered
    -   `"skip-any-corrupted-records"`: post-disaster recovery. The data is recovered as much as possible, and corrupted records are skipped.
-   Default value: `"point-in-time"`

### <code>wal-dir</code> {#code-wal-dir-code}

-   The directory in which WAL files are stored
-   Default value: `"/tmp/tikv/store"`

### <code>wal-ttl-seconds</code> {#code-wal-ttl-seconds-code}

-   The living time of the archived WAL files. When the value is exceeded, the system deletes these files.
-   Default value: `0`
-   Minimum value: `0`
-   unit: second

### <code>wal-size-limit</code> {#code-wal-size-limit-code}

-   The size limit of the archived WAL files. When the value is exceeded, the system deletes these files.
-   Default value: `0`
-   Minimum value: `0`
-   Unit: B|KB|MB|GB

### <code>enable-statistics</code> {#code-enable-statistics-code}

-   Determines whether to enable the statistics of RocksDB
-   Default value: `true`

### <code>stats-dump-period</code> {#code-stats-dump-period-code}

-   The interval at which statistics are output to the log.
-   Default value: `10m`

### <code>compaction-readahead-size</code> {#code-compaction-readahead-size-code}

-   Enables the readahead feature during RocksDB compaction and specifies the size of readahead data. If you are using mechanical disks, it is recommended to set the value to 2MB at least.
-   Default value: `0`
-   Minimum value: `0`
-   Unit: B|KB|MB|GB

### <code>writable-file-max-buffer-size</code> {#code-writable-file-max-buffer-size-code}

-   The maximum buffer size used in WritableFileWrite
-   Default value: `"1MB"`
-   Minimum value: `0`
-   Unit: B|KB|MB|GB

### <code>use-direct-io-for-flush-and-compaction</code> {#code-use-direct-io-for-flush-and-compaction-code}

-   Determines whether to use `O_DIRECT` for both reads and writes in the background flush and compactions. The performance impact of this option: enabling `O_DIRECT` bypasses and prevents contamination of the OS buffer cache, but the subsequent file reads require re-reading the contents to the buffer cache.
-   Default value: `false`

### <code>rate-bytes-per-sec</code> {#code-rate-bytes-per-sec-code}

-   The maximum rate permitted by RocksDB's compaction rate limiter
-   Default value: `10GB`
-   Minimum value: `0`
-   Unit: B|KB|MB|GB

### <code>rate-limiter-mode</code> {#code-rate-limiter-mode-code}

-   RocksDB's compaction rate limiter mode
-   Optional values: `"read-only"`, `"write-only"`, `"all-io"`
-   Default value: `"write-only"`

### <code>rate-limiter-auto-tuned</code> <span class="version-mark">New in v5.0</span> {#code-rate-limiter-auto-tuned-code-span-class-version-mark-new-in-v5-0-span}

-   Determines whether to automatically optimize the configuration of the RocksDB's compaction rate limiter based on recent workload. When this configuration is enabled, compaction pending bytes will be slightly higher than usual.
-   Default value: `true`

### <code>enable-pipelined-write</code> {#code-enable-pipelined-write-code}

-   Enables or disables Pipelined Write
-   Default value: `true`

### <code>bytes-per-sync</code> {#code-bytes-per-sync-code}

-   The rate at which OS incrementally synchronizes files to disk while these files are being written asynchronously
-   Default value: `"1MB"`
-   Minimum value: `0`
-   Unit: B|KB|MB|GB

### <code>wal-bytes-per-sync</code> {#code-wal-bytes-per-sync-code}

-   The rate at which OS incrementally synchronizes WAL files to disk while the WAL files are being written
-   Default value: `"512KB"`
-   Minimum value: `0`
-   Unit: B|KB|MB|GB

### <code>info-log-max-size</code> {#code-info-log-max-size-code}

-   The maximum size of Info log
-   Default value: `"1GB"`
-   Minimum value: `0`
-   Unit: B|KB|MB|GB

### <code>info-log-roll-time</code> {#code-info-log-roll-time-code}

-   The time interval at which Info logs are truncated. If the value is `0s`, logs are not truncated.
-   Default value: `"0s"`

### <code>info-log-keep-log-file-num</code> {#code-info-log-keep-log-file-num-code}

-   The maximum number of kept log files
-   Default value: `10`
-   Minimum value: `0`

### <code>info-log-dir</code> {#code-info-log-dir-code}

-   The directory in which logs are stored
-   Default value: `""`

## rocksdb.titan {#rocksdb-titan}

Configuration items related to Titan.

### <code>enabled</code> {#code-enabled-code}

-   Enables or disables Titan
-   Default value: `false`

### <code>dirname</code> {#code-dirname-code}

-   The directory in which the Titan Blob file is stored
-   Default value: `"titandb"`

### <code>disable-gc</code> {#code-disable-gc-code}

-   Determines whether to disable Garbage Collection (GC) that Titan performs to Blob files
-   Default value: `false`

### <code>max-background-gc</code> {#code-max-background-gc-code}

-   The maximum number of GC threads in Titan
-   Default value: `4`
-   Minimum value: `1`

## rocksdb.defaultcf | rocksdb.writecf | rocksdb.lockcf {#rocksdb-defaultcf-rocksdb-writecf-rocksdb-lockcf}

Configuration items related to `rocksdb.defaultcf`, `rocksdb.writecf`, and `rocksdb.lockcf`.

### <code>block-size</code> {#code-block-size-code}

-   The default size of a RocksDB block
-   Default value for `defaultcf` and `writecf`: `"64KB"`
-   Default value for `lockcf`: `"16KB"`
-   Minimum value: `"1KB"`
-   Unit: KB|MB|GB

### <code>block-cache-size</code> {#code-block-cache-size-code}

-   The cache size of a RocksDB block
-   Default value for `defaultcf`: `Total machine memory * 25%`
-   Default value for `writecf`: `Total machine memory * 15%`
-   Default value for `lockcf`: `Total machine memory * 2%`
-   Minimum value: `0`
-   Unit: KB|MB|GB

### <code>disable-block-cache</code> {#code-disable-block-cache-code}

-   Enables or disables block cache
-   Default value: `false`

### <code>cache-index-and-filter-blocks</code> {#code-cache-index-and-filter-blocks-code}

-   Enables or disables caching index and filter
-   Default value: `true`

### <code>pin-l0-filter-and-index-blocks</code> {#code-pin-l0-filter-and-index-blocks-code}

-   Determines whether to pin the index and filter blocks of the level 0 SST files in memory.
-   Default value: `true`

### <code>use-bloom-filter</code> {#code-use-bloom-filter-code}

-   Enables or disables bloom filter
-   Default value: `true`

### <code>optimize-filters-for-hits</code> {#code-optimize-filters-for-hits-code}

-   Determines whether to optimize the hit ratio of filters
-   Default value for `defaultcf`: `true`
-   Default value for `writecf` and `lockcf`: `false`

### <code>whole-key-filtering</code> {#code-whole-key-filtering-code}

-   Determines whether to put the entire key to bloom filter
-   Default value for `defaultcf` and `lockcf`: `true`
-   Default value for `writecf`: `false`

### <code>bloom-filter-bits-per-key</code> {#code-bloom-filter-bits-per-key-code}

-   The length that bloom filter reserves for each key
-   Default value: `10`
-   Unit: byte

### <code>block-based-bloom-filter</code> {#code-block-based-bloom-filter-code}

-   Determines whether each block creates a bloom filter
-   Default value: `false`

### <code>read-amp-bytes-per-bit</code> {#code-read-amp-bytes-per-bit-code}

-   Enables or disables statistics of read amplification.
-   Optional values: `0` (disabled), > `0` (enabled).
-   Default value: `0`
-   Minimum value: `0`

### <code>compression-per-level</code> {#code-compression-per-level-code}

-   The default compression algorithm for each level
-   Default value for `defaultcf`: ["no", "no", "lz4", "lz4", "lz4", "zstd", "zstd"]
-   Default value for `writecf`: ["no", "no", "lz4", "lz4", "lz4", "zstd", "zstd"]
-   Default value for `lockcf`: ["no", "no", "no", "no", "no", "no", "no"]

### <code>bottommost-level-compression</code> {#code-bottommost-level-compression-code}

-   Sets the compression algorithm of the bottommost layer. This configuration item overrides the `compression-per-level` setting.
-   Ever since data is written to LSM-tree, RocksDB does not directly adopt the last compression algorithm specified in the `compression-per-level` array for the bottommost layer. `bottommost-level-compression` enables the bottommost layer to use the compression algorithm of the best compression effect from the beginning.
-   If you do not want to set the compression algorithm for the bottommost layer, set the value of this configuration item to `disable`.
-   Default value: `"zstd"`

### <code>write-buffer-size</code> {#code-write-buffer-size-code}

-   Memtable size
-   Default value for `defaultcf` and `writecf`: `"128MB"`
-   Default value for `lockcf`: `"32MB"`
-   Minimum value: `0`
-   Unit: KB|MB|GB

### <code>max-write-buffer-number</code> {#code-max-write-buffer-number-code}

-   The maximum number of memtables. When `storage.flow-control.enable` is set to `true`, `storage.flow-control.memtables-threshold` overrides this configuration item.
-   Default value: `5`
-   Minimum value: `0`

### <code>min-write-buffer-number-to-merge</code> {#code-min-write-buffer-number-to-merge-code}

-   The minimum number of memtables required to trigger flush
-   Default value: `1`
-   Minimum value: `0`

### <code>max-bytes-for-level-base</code> {#code-max-bytes-for-level-base-code}

-   The maximum number of bytes at base level (L1). Generally, it is set to 4 times the size of a memtable.
-   Default value for `defaultcf` and `writecf`: `"512MB"`
-   Default value for `lockcf`: `"128MB"`
-   Minimum value: `0`
-   Unit: KB|MB|GB

### <code>target-file-size-base</code> {#code-target-file-size-base-code}

-   The size of the target file at base level. This value is overridden by `compaction-guard-max-output-file-size` when the `enable-compaction-guard` value is `true`.
-   Default value: `"8MB"`
-   Minimum value: `0`
-   Unit: KB|MB|GB

### <code>level0-file-num-compaction-trigger</code> {#code-level0-file-num-compaction-trigger-code}

-   The maximum number of files at L0 that trigger compaction
-   Default value for `defaultcf` and `writecf`: `4`
-   Default value for `lockcf`: `1`
-   Minimum value: `0`

### <code>level0-slowdown-writes-trigger</code> {#code-level0-slowdown-writes-trigger-code}

-   The maximum number of files at L0 that trigger write stall. When `storage.flow-control.enable` is set to `true`, `storage.flow-control.l0-files-threshold` overrides this configuration item.
-   Default value: `20`
-   Minimum value: `0`

### <code>level0-stop-writes-trigger</code> {#code-level0-stop-writes-trigger-code}

-   The maximum number of files at L0 required to completely block write
-   Default value: `36`
-   Minimum value: `0`

### <code>max-compaction-bytes</code> {#code-max-compaction-bytes-code}

-   The maximum number of bytes written into disk per compaction
-   Default value: `"2GB"`
-   Minimum value: `0`
-   Unit: KB|MB|GB

### <code>compaction-pri</code> {#code-compaction-pri-code}

-   The priority type of compaction
-   Optional values: `"by-compensated-size"`, `"oldest-largest-seq-first"`, `"oldest-smallest-seq-first"`, `"min-overlapping-ratio"`
-   Default value for `defaultcf` and `writecf`: `"min-overlapping-ratio"`
-   Default value for `lockcf`: `"by-compensated-size"`

### <code>dynamic-level-bytes</code> {#code-dynamic-level-bytes-code}

-   Determines whether to optimize dynamic level bytes
-   Default value: `true`

### <code>num-levels</code> {#code-num-levels-code}

-   The maximum number of levels in a RocksDB file
-   Default value: `7`

### <code>max-bytes-for-level-multiplier</code> {#code-max-bytes-for-level-multiplier-code}

-   The default amplification multiple for each layer
-   Default value: `10`

### <code>compaction-style</code> {#code-compaction-style-code}

-   Compaction method
-   Optional values: `"level"`, `"universal"`, `"fifo"`
-   Default value: `"level"`

### <code>disable-auto-compactions</code> {#code-disable-auto-compactions-code}

-   Determines whether to disable auto compaction.
-   Default value: `false`

### <code>soft-pending-compaction-bytes-limit</code> {#code-soft-pending-compaction-bytes-limit-code}

-   The soft limit on the pending compaction bytes. When `storage.flow-control.enable` is set to `true`, `storage.flow-control.soft-pending-compaction-bytes-limit` overrides this configuration item.
-   Default value: `"192GB"`
-   Unit: KB|MB|GB

### <code>hard-pending-compaction-bytes-limit</code> {#code-hard-pending-compaction-bytes-limit-code}

-   The hard limit on the pending compaction bytes. When `storage.flow-control.enable` is set to `true`, `storage.flow-control.hard-pending-compaction-bytes-limit` overrides this configuration item.
-   Default value: `"256GB"`
-   Unit: KB|MB|GB

### <code>enable-compaction-guard</code> {#code-enable-compaction-guard-code}

-   Enables or disables the compaction guard, which is an optimization to split SST files at TiKV Region boundaries. This optimization can help reduce compaction I/O and allows TiKV to use larger SST file size (thus less SST files overall) and at the time efficiently clean up stale data when migrating Regions.
-   Default value for `defaultcf` and `writecf`: `true`
-   Default value for `lockcf`: `false`

### <code>compaction-guard-min-output-file-size</code> {#code-compaction-guard-min-output-file-size-code}

-   The minimum SST file size when the compaction guard is enabled. This configuration prevents SST files from being too small when the compaction guard is enabled.
-   Default value: `"8MB"`
-   Unit: KB|MB|GB

### <code>compaction-guard-max-output-file-size</code> {#code-compaction-guard-max-output-file-size-code}

-   The maximum SST file size when the compaction guard is enabled. The configuration prevents SST files from being too large when the compaction guard is enabled. This configuration overrides `target-file-size-base` for the same column family.
-   Default value: `"128MB"`
-   Unit: KB|MB|GB

## rocksdb.defaultcf.titan {#rocksdb-defaultcf-titan}

Configuration items related to `rocksdb.defaultcf.titan`.

### <code>min-blob-size</code> {#code-min-blob-size-code}

-   The smallest value stored in a Blob file. Values smaller than the specified size are stored in the LSM-Tree.
-   Default value: `"1KB"`
-   Minimum value: `0`
-   Unit: KB|MB|GB

### <code>blob-file-compression</code> {#code-blob-file-compression-code}

-   The compression algorithm used in a Blob file
-   Optional values: `"no"`, `"snappy"`, `"zlib"`, `"bzip2"`, `"lz4"`, `"lz4hc"`, `"zstd"`
-   Default value: `"lz4"`

### <code>blob-cache-size</code> {#code-blob-cache-size-code}

-   The cache size of a Blob file
-   Default value: `"0GB"`
-   Minimum value: `0`
-   Unit: KB|MB|GB

### <code>min-gc-batch-size</code> {#code-min-gc-batch-size-code}

-   The minimum total size of Blob files required to perform GC for one time
-   Default value: `"16MB"`
-   Minimum value: `0`
-   Unit: KB|MB|GB

### <code>max-gc-batch-size</code> {#code-max-gc-batch-size-code}

-   The maximum total size of Blob files allowed to perform GC for one time
-   Default value: `"64MB"`
-   Minimum value: `0`
-   Unit: KB|MB|GB

### <code>discardable-ratio</code> {#code-discardable-ratio-code}

-   The ratio at which GC is triggered for Blob files. The Blob file can be selected for GC only if the proportion of the invalid values in a Blob file exceeds this ratio.
-   Default value: `0.5`
-   Minimum value: `0`
-   Maximum value: `1`

### <code>sample-ratio</code> {#code-sample-ratio-code}

-   The ratio of (data read from a Blob file/the entire Blob file) when sampling the file during GC
-   Default value: `0.1`
-   Minimum value: `0`
-   Maximum value: `1`

### <code>merge-small-file-threshold</code> {#code-merge-small-file-threshold-code}

-   When the size of a Blob file is smaller than this value, the Blob file might still be selected for GC. In this situation, `discardable-ratio` is ignored.
-   Default value: `"8MB"`
-   Minimum value: `0`
-   Unit: KB|MB|GB

### <code>blob-run-mode</code> {#code-blob-run-mode-code}

-   Specifies the running mode of Titan.
-   Optional values:
    -   `normal`: Writes data to the blob file when the value size exceeds `min-blob-size`.
    -   `read_only`: Refuses to write new data to the blob file, but still reads the original data from the blob file.
    -   `fallback`: Writes data in the blob file back to LSM.
-   Default value: `normal`

### <code>level-merge</code> {#code-level-merge-code}

-   Determines whether to optimize the read performance. When `level-merge` is enabled, there is more write amplification.
-   Default value: `false`

### <code>gc-merge-rewrite</code> {#code-gc-merge-rewrite-code}

-   Determines whether to use the merge operator to write back blob indexes for Titan GC. When `gc-merge-rewrite` is enabled, it reduces the effect of Titan GC on the writes in the foreground.
-   Default value: `false`

## raftdb {#raftdb}

Configuration items related to `raftdb`

### <code>max-background-jobs</code> {#code-max-background-jobs-code}

-   The number of background threads in RocksDB. When you modify the size of the RocksDB thread pool, refer to [Performance tuning for TiKV thread pools](/tune-tikv-thread-performance.md#performance-tuning-for-tikv-thread-pools).
-   Default value: `4`
-   Minimum value: `2`

### <code>max-sub-compactions</code> {#code-max-sub-compactions-code}

-   The number of concurrent sub-compaction operations performed in RocksDB
-   Default value: `2`
-   Minimum value: `1`

### <code>wal-dir</code> {#code-wal-dir-code}

-   The directory in which WAL files are stored
-   Default value: `"/tmp/tikv/store"`

## raft-engine {#raft-engine}

Configuration items related to Raft Engine.

> **Warning:**
>
> Raft Engine is an experimental feature. It is not recommended to use it in the production environment.

### <code>enable</code> {#code-enable-code}

-   Determines whether to use Raft Engine to store Raft logs. When it is enabled, configurations of `raftdb` are ignored.
-   Default value: `false`

### <code>dir</code> {#code-dir-code}

-   The directory at which raft log files are stored. If the directory does not exist, it will be created when TiKV is started.
-   When this configuration is not set, `{data-dir}/raft-engine` is used.
-   If there are multiple disks on your machine, it is recommended to store the data of Raft Engine on a different disk to improve TiKV performance.
-   Default value: `""`

### <code>batch-compression-threshold</code> {#code-batch-compression-threshold-code}

-   Specifies the threshold size of a log batch. A log batch larger than this configuration is compressed. If you set this configuration item to `0`, compression is disabled.
-   Default value: `"8KB"`

### <code>bytes-per-sync</code> {#code-bytes-per-sync-code}

-   Specifies the maximum accumulative size of buffered writes. When this configuration value is exceeded, buffered writes are flushed to the disk.
-   If you set this configuration item to `0`, incremental sync is disabled.
-   Default value: `"4MB"`

### <code>target-file-size</code> {#code-target-file-size-code}

-   Specifies the maximum size of log files. When a log file is larger than this value, it is rotated.
-   Default value: `"128MB"`

### <code>purge-threshold</code> {#code-purge-threshold-code}

-   Specifies the threshold size of the main log queue. When this configuration value is exceeded, the main log queue is purged.
-   This configuration can be used to adjust the disk space usage of Raft Engine.
-   Default value: `"10GB"`

### <code>recovery-mode</code> {#code-recovery-mode-code}

-   Determines how to deal with file corruption during recovery.
-   Value options: `"absolute-consistency"`, `"tolerate-tail-corruption"`, `"tolerate-any-corruption"`
-   Default value: `"tolerate-tail-corruption"`

### <code>recovery-read-block-size</code> {#code-recovery-read-block-size-code}

-   The minimum I/O size for reading log files during recovery.
-   Default value: `"16KB"`
-   Minimum value: `"512B"`

### <code>recovery-threads</code> {#code-recovery-threads-code}

-   The number of threads used to scan and recover log files.
-   Default value: `4`
-   Minimum value: `1`

## security {#security}

Configuration items related to security.

### <code>ca-path</code> {#code-ca-path-code}

-   The path of the CA file
-   Default value: `""`

### <code>cert-path</code> {#code-cert-path-code}

-   The path of the Privacy Enhanced Mail (PEM) file that contains the X.509 certificate
-   Default value: `""`

### <code>key-path</code> {#code-key-path-code}

-   The path of the PEM file that contains the X.509 key
-   Default value: `""`

### <code>cert-allowed-cn</code> {#code-cert-allowed-cn-code}

-   A list of acceptable X.509 Common Names in certificates presented by clients. Requests are permitted only when the presented Common Name is an exact match with one of the entries in the list.
-   Default value: `[]`. This means that the client certificate CN check is disabled by default.

### <code>redact-info-log</code> <span class="version-mark">New in v4.0.8</span> {#code-redact-info-log-code-span-class-version-mark-new-in-v4-0-8-span}

-   This configuration item enables or disables log redaction. If the configuration value is set to `true`, all user data in the log will be replaced by `?`.
-   Default value: `false`

## security.encryption {#security-encryption}

Configuration items related to [encryption at rest](/encryption-at-rest.md) (TDE).

### <code>data-encryption-method</code> {#code-data-encryption-method-code}

-   The encryption method for data files
-   Value options: "plaintext", "aes128-ctr", "aes192-ctr", and "aes256-ctr"
-   A value other than "plaintext" means that encryption is enabled, in which case the master key must be specified.
-   Default value: `"plaintext"`

### <code>data-key-rotation-period</code> {#code-data-key-rotation-period-code}

-   Specifies how often TiKV rotates the data encryption key.
-   Default value: `7d`

### enable-file-dictionary-log {#enable-file-dictionary-log}

-   Enables the optimization to reduce I/O and mutex contention when TiKV manages the encryption metadata.
-   To avoid possible compatibility issues when this configuration parameter is enabled (by default), see [Encryption at Rest - Compatibility between TiKV versions](/encryption-at-rest.md#compatibility-between-tikv-versions) for details.
-   Default value: `true`

### master-key {#master-key}

-   Specifies the master key if encryption is enabled. To learn how to configure a master key, see [Encryption at Rest - Configure encryption](/encryption-at-rest.md#configure-encryption).

### previous-master-key {#previous-master-key}

-   Specifies the old master key when rotating the new master key. The configuration format is the same as that of `master-key`. To learn how to configure a master key, see [Encryption at Rest - Configure encryption](/encryption-at-rest.md#configure-encryption).

## <code>import</code> {#code-import-code}

Configuration items related to TiDB Lightning import and BR restore.

### <code>num-threads</code> {#code-num-threads-code}

-   The number of threads to process RPC requests
-   Default value: `8`
-   Minimum value: `1`

## gc {#gc}

### <code>enable-compaction-filter</code> <span class="version-mark">New in v5.0</span> {#code-enable-compaction-filter-code-span-class-version-mark-new-in-v5-0-span}

-   Controls whether to enable the GC in Compaction Filter feature
-   Default value: `true`

## backup {#backup}

Configuration items related to BR backup.

### <code>num-threads</code> {#code-num-threads-code}

-   The number of worker threads to process backup
-   Default value: `MIN(CPU * 0.5, 8)`.
-   Minimum value: `1`

### <code>enable-auto-tune</code> <span class="version-mark">New in v5.4.0</span> {#code-enable-auto-tune-code-span-class-version-mark-new-in-v5-4-0-span}

-   Controls whether to limit the resources used by backup tasks to reduce the impact on the cluster when the cluster resource utilization is high. For more information, refer to [BR Auto-Tune](/br/br-auto-tune.md).
-   Default value: `true`

### <code>s3-multi-part-size</code> <span class="version-mark">New in v5.3.2</span> {#code-s3-multi-part-size-code-span-class-version-mark-new-in-v5-3-2-span}

> **Note:**
>
> This configuration is introduced to address backup failures caused by S3 rate limiting. This problem has been fixed by [refining the backup data storage structure](https://docs.pingcap.com/tidb/stable/backup-and-restore-design#backup-file-structure). Therefore, this configuration is deprecated from v6.1.1 and is no longer recommended.

-   The part size used when you perform multipart upload to S3 during backup. You can adjust the value of this configuration to control the number of requests sent to S3.
-   If data is backed up to S3 and the backup file is larger than the value of this configuration item, [multipart upload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPart.html) is automatically enabled. Based on the compression ratio, the backup file generated by a 96-MiB Region is approximately 10 MiB to 30 MiB.
-   Default value: 5MiB

## cdc {#cdc}

Configuration items related to TiCDC.

### <code>min-ts-interval</code> {#code-min-ts-interval-code}

-   The interval at which Resolved TS is calculated and forwarded.
-   Default value: `"1s"`

### <code>old-value-cache-memory-quota</code> {#code-old-value-cache-memory-quota-code}

-   The upper limit of memory usage by TiCDC old values.
-   Default value: `512MB`

### <code>sink-memory-quota</code> {#code-sink-memory-quota-code}

-   The upper limit of memory usage by TiCDC data change events.
-   Default value: `512MB`

### <code>incremental-scan-speed-limit</code> {#code-incremental-scan-speed-limit-code}

-   The maximum speed at which historical data is incrementally scanned.
-   Default value: `"128MB"`, which means 128 MB per second.

### <code>incremental-scan-threads</code> {#code-incremental-scan-threads-code}

-   The number of threads for the task of incrementally scanning historical data.
-   Default value: `4`, which means 4 threads.

### <code>incremental-scan-concurrency</code> {#code-incremental-scan-concurrency-code}

-   The maximum number of concurrent executions for the tasks of incrementally scanning historical data.
-   Default value: `6`, which means 6 tasks can be concurrent executed at most.
-   Note: The value of `incremental-scan-concurrency` must be greater than or equal to that of `incremental-scan-threads`; otherwise, TiKV will report an error at startup.

## resolved-ts {#resolved-ts}

Configuration items related to maintaining the Resolved TS to serve Stale Read requests.

### <code>enable</code> {#code-enable-code}

-   Determines whether to maintain the Resolved TS for all Regions.
-   Default value: `true`

### <code>advance-ts-interval</code> {#code-advance-ts-interval-code}

-   The interval at which Resolved TS is calculated and forwarded.
-   Default value: `"1s"`

### <code>scan-lock-pool-size</code> {#code-scan-lock-pool-size-code}

-   The number of threads that TiKV uses to scan the MVCC (multi-version concurrency control) lock data when initializing the Resolved TS.
-   Default value: `2`, which means 2 threads.

## pessimistic-txn {#pessimistic-txn}

For pessimistic transaction usage, refer to [TiDB Pessimistic Transaction Mode](/pessimistic-transaction.md).

### <code>wait-for-lock-timeout</code> {#code-wait-for-lock-timeout-code}

-   The longest time that a pessimistic transaction in TiKV waits for other transactions to release the lock. If the time is out, an error is returned to TiDB, and TiDB retries to add a lock. The lock wait timeout is set by `innodb_lock_wait_timeout`.
-   Default value: `"1s"`
-   Minimum value: `"1ms"`

### <code>wake-up-delay-duration</code> {#code-wake-up-delay-duration-code}

-   When pessimistic transactions release the lock, among all the transactions waiting for lock, only the transaction with the smallest `start_ts` is woken up. Other transactions will be woken up after `wake-up-delay-duration`.
-   Default value: `"20ms"`

### <code>pipelined</code> {#code-pipelined-code}

-   This configuration item enables the pipelined process of adding the pessimistic lock. With this feature enabled, after detecting that data can be locked, TiKV immediately notifies TiDB to execute the subsequent requests and write the pessimistic lock asynchronously, which reduces most of the latency and significantly improves the performance of pessimistic transactions. But there is a still low probability that the asynchronous write of the pessimistic lock fails, which might cause the failure of pessimistic transaction commits.
-   Default value: `true`
