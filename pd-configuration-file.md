---
title: PD Configuration File
summary: Learn the PD configuration file.
---

# PD Configuration File {#pd-configuration-file}

<!-- markdownlint-disable MD001 -->

The PD configuration file supports more options than command-line parameters. You can find the default configuration file [here](https://github.com/pingcap/pd/blob/master/conf/config.toml).

This document only describes parameters that are not included in command-line parameters. Check [here](/command-line-flags-for-pd-configuration.md) for the command line parameters.

### <code>name</code> {#code-name-code}

-   The unique name of a PD node
-   Default value: `"pd"`
-   To start multiply PD nodes, use a unique name for each node.

### <code>data-dir</code> {#code-data-dir-code}

-   The directory in which PD stores data
-   Default value: `default.${name}"`

### <code>client-urls</code> {#code-client-urls-code}

-   The list of client URLs to be listened to by PD
-   Default value: `"http://127.0.0.1:2379"`
-   When you deploy a cluster, you must specify the IP address of the current host as `client-urls` (for example, `"http://192.168.100.113:2379"`). If the cluster runs on Docker, specify the IP address of Docker as `"http://0.0.0.0:2379"`.

### <code>advertise-client-urls</code> {#code-advertise-client-urls-code}

-   The list of advertise URLs for the client to access PD
-   Default value: `"${client-urls}"`
-   In some situations such as in the Docker or NAT network environment, if a client cannot access PD through the default client URLs listened to by PD, you must manually set the advertise client URLs.
-   For example, the internal IP address of Docker is `172.17.0.1`, while the IP address of the host is `192.168.100.113` and the port mapping is set to `-p 2380:2380`. In this case, you can set `advertise-client-urls` to `"http://192.168.100.113:2380"`. The client can find this service through `"http://192.168.100.113:2380"`.

### <code>peer-urls</code> {#code-peer-urls-code}

-   The list of peer URLs to be listened to by a PD node
-   Default value: `"http://127.0.0.1:2380"`
-   When you deploy a cluster, you must specify `peer-urls` as the IP address of the current host, such as `"http://192.168.100.113:2380"`. If the cluster runs on Docker, specify the IP address of Docker as `"http://0.0.0.0:2380"`.

### <code>advertise-peer-urls</code> {#code-advertise-peer-urls-code}

-   The list of advertise URLs for other PD nodes (peers) to access a PD node
-   Default: `"${peer-urls}"`
-   In some situations such as in the Docker or NAT network environment, if the other nodes (peers) cannot access the PD node through the default peer URLs listened to by this PD node, you must manually set the advertise peer URLs.
-   For example, the internal IP address of Docker is `172.17.0.1`, while the IP address of the host is `192.168.100.113` and the port mapping is set to `-p 2380:2380`. In this case, you can set `advertise-peer-urls` to `"http://192.168.100.113:2380"`. The other PD nodes can find this service through `"http://192.168.100.113:2380"`.

### <code>initial-cluster</code> {#code-initial-cluster-code}

-   The initial cluster configuration for bootstrapping
-   Default value: `"{name}=http://{advertise-peer-url}"`
-   For example, if `name` is "pd", and `advertise-peer-urls` is `"http://192.168.100.113:2380"`, the `initial-cluster` is `"pd=http://192.168.100.113:2380"`.
-   If you need to start three PD servers, the `initial-cluster` might be:

    ```
    pd1=http://192.168.100.113:2380, pd2=http://192.168.100.114:2380, pd3=192.168.100.115:2380
    ```

### <code>initial-cluster-state</code> {#code-initial-cluster-state-code}

-   The initial state of the cluster
-   Default value: `"new"`

### <code>initial-cluster-token</code> {#code-initial-cluster-token-code}

-   Identifies different clusters during the bootstrap phase
-   Default value: `"pd-cluster"`
-   If multiple clusters that have nodes with same configurations are deployed successively, you must specify different tokens to isolate different cluster nodes.

### <code>lease</code> {#code-lease-code}

-   The timeout of the PD Leader Key lease. After the timeout, the system re-elects a Leader.
-   Default value: `3`
-   Unit: second

### <code>quota-backend-bytes</code> {#code-quota-backend-bytes-code}

-   The storage size of the meta-information database, which is 8GiB by default
-   Default value: `8589934592`

### <code>auto-compaction-mod</code> {#code-auto-compaction-mod-code}

-   The automatic compaction modes of the meta-information database
-   Available options: `periodic` (by cycle) and `revision` (by version number).
-   Default value: `periodic`

### <code>auto-compaction-retention</code> {#code-auto-compaction-retention-code}

-   The time interval for automatic compaction of the meta-information database when `auto-compaction-retention` is `periodic`. When the compaction mode is set to `revision`, this parameter indicates the version number for the automatic compaction.
-   Default value: 1h

### <code>force-new-cluster</code> {#code-force-new-cluster-code}

-   Determines whether to force PD to start as a new cluster and modify the number of Raft members to `1`
-   Default value: `false`

## pd-server {#pd-server}

Configuration items related to pd-server

### <code>flow-round-by-digit</code> <span class="version-mark">New in TiDB 5.1</span> {#code-flow-round-by-digit-code-span-class-version-mark-new-in-tidb-5-1-span}

-   Default value: 3
-   PD rounds the lowest digits of the flow number, which reduces the update of statistics caused by the changes of the Region flow information. This configuration item is used to specify the number of lowest digits to round for the Region flow information. For example, the flow `100512` will be rounded to `101000` because the default value is `3`. This configuration replaces `trace-region-flow`.

> **Note:**
>
> If you have upgraded your cluster from a TiDB 4.0 version to the current version, the behavior of `flow-round-by-digit` after the upgrading and the behavior of `trace-region-flow` before the upgrading are consistent by default. This means that if the value of `trace-region-flow` is false before the upgrading, the value of `flow-round-by-digit` after the upgrading is 127; if the value of `trace-region-flow` is `true` before the upgrading, the value of `flow-round-by-digit` after the upgrading is `3`.

## security {#security}

Configuration items related to security

### <code>cacert-path</code> {#code-cacert-path-code}

-   The path of the CA file
-   Default value: ""

### <code>cert-path</code> {#code-cert-path-code}

-   The path of the Privacy Enhanced Mail (PEM) file that contains the X509 certificate
-   Default value: ""

### <code>key-path</code> {#code-key-path-code}

-   The path of the PEM file that contains the X509 key
-   Default value: ""

### <code>redact-info-log</code> <span class="version-mark">New in v5.0</span> {#code-redact-info-log-code-span-class-version-mark-new-in-v5-0-span}

-   Controls whether to enable log redaction in the PD log
-   When you set the configuration value to `true`, user data is redacted in the PD log.
-   Default value: `false`

## <code>log</code> {#code-log-code}

Configuration items related to log

### <code>level</code> {#code-level-code}

-   Specifies the level of the output log
-   Optional value: `"debug"`, `"info"`, `"warn"`, `"error"`, `"fatal"`
-   Default value: `"info"`

### <code>format</code> {#code-format-code}

-   The log format
-   Optional value: `"text"`, `"json"`
-   Default value: `"text"`

### <code>disable-timestamp</code> {#code-disable-timestamp-code}

-   Whether to disable the automatically generated timestamp in the log
-   Default value: `false`

## <code>log.file</code> {#code-log-file-code}

Configuration items related to the log file

### <code>max-size</code> {#code-max-size-code}

-   The maximum size of a single log file. When this value is exceeded, the system automatically splits the log into several files.
-   Default value: `300`
-   Unit: MiB
-   Minimum value: `1`

### <code>max-days</code> {#code-max-days-code}

-   The maximum number of days in which a log is kept
-   If the configuration item is not set, or the value of it is set to the default value 0, PD does not clean log files.
-   Default value: `0`

### <code>max-backups</code> {#code-max-backups-code}

-   The maximum number of log files to keep
-   If the configuration item is not set, or the value of it is set to the default value 0, PD keeps all log files.
-   Default value: `0`

## <code>metric</code> {#code-metric-code}

Configuration items related to monitoring

### <code>interval</code> {#code-interval-code}

-   The interval at which monitoring metric data is pushed to Prometheus
-   Default value: `15s`

## <code>schedule</code> {#code-schedule-code}

Configuration items related to scheduling

### <code>max-merge-region-size</code> {#code-max-merge-region-size-code}

-   Controls the size limit of `Region Merge`. When the Region size is greater than the specified value, PD does not merge the Region with the adjacent Regions.
-   Default value: `20`
-   Unit: MiB

### <code>max-merge-region-keys</code> {#code-max-merge-region-keys-code}

-   Specifies the upper limit of the `Region Merge` key. When the Region key is greater than the specified value, the PD does not merge the Region with its adjacent Regions.
-   Default value: `200000`

### <code>patrol-region-interval</code> {#code-patrol-region-interval-code}

-   Controls the running frequency at which `replicaChecker` checks the health state of a Region. The smaller this value is, the faster `replicaChecker` runs. Normally, you do not need to adjust this parameter.
-   Default value: `10ms`

### <code>split-merge-interval</code> {#code-split-merge-interval-code}

-   Controls the time interval between the `split` and `merge` operations on the same Region. That means a newly split Region will not be merged for a while.
-   Default value: `1h`

### <code>max-snapshot-count</code> {#code-max-snapshot-count-code}

-   Controls the maximum number of snapshots that a single store receives or sends at the same time. PD schedulers depend on this configuration to prevent the resources used for normal traffic from being preempted.
-   Default value value: `64`

### <code>max-pending-peer-count</code> {#code-max-pending-peer-count-code}

-   Controls the maximum number of pending peers in a single store. PD schedulers depend on this configuration to prevent too many Regions with outdated logs from being generated on some nodes.
-   Default value: `64`

### <code>max-store-down-time</code> {#code-max-store-down-time-code}

-   The downtime after which PD judges that the disconnected store can not be recovered. When PD fails to receive the heartbeat from a store after the specified period of time, it adds replicas at other nodes.
-   Default value: `30m`

### <code>leader-schedule-limit</code> {#code-leader-schedule-limit-code}

-   The number of Leader scheduling tasks performed at the same time
-   Default value: `4`

### <code>region-schedule-limit</code> {#code-region-schedule-limit-code}

-   The number of Region scheduling tasks performed at the same time
-   Default value: `2048`

### <code>hot-region-schedule-limit</code> {#code-hot-region-schedule-limit-code}

-   Controls the hot Region scheduling tasks that are running at the same time. It is independent of the Region scheduling.
-   Default value: `4`

### <code>hot-region-cache-hits-threshold</code> {#code-hot-region-cache-hits-threshold-code}

-   The threshold used to set the number of minutes required to identify a hot Region. PD can participate in the hotspot scheduling only after the Region is in the hotspot state for more than this number of minutes.
-   Default value: `3`

### <code>replica-schedule-limit</code> {#code-replica-schedule-limit-code}

-   The number of Replica scheduling tasks performed at the same time
-   Default value: `64`

### <code>merge-schedule-limit</code> {#code-merge-schedule-limit-code}

-   The number of the `Region Merge` scheduling tasks performed at the same time. Set this parameter to `0` to disable `Region Merge`.
-   Default value: `8`

### <code>high-space-ratio</code> {#code-high-space-ratio-code}

-   The threshold ratio below which the capacity of the store is sufficient. If the space occupancy ratio of the store is smaller than this threshold value, PD ignores the remaining space of the store when performing scheduling, and balances load mainly based on the Region size. This configuration takes effect only when `region-score-formula-version` is set to `v1`.
-   Default value: `0.7`
-   Minimum value: greater than `0`
-   Maximum value: less than `1`

### <code>low-space-ratio</code> {#code-low-space-ratio-code}

-   The threshold ratio above which the capacity of the store is insufficient. If the space occupancy ratio of a store exceeds this threshold value, PD avoids migrating data to this store as much as possible. Meanwhile, to avoid the disk space of the corresponding store being exhausted, PD performs scheduling mainly based on the remaining space of the store.
-   Default value: `0.8`
-   Minimum value: greater than `0`
-   Maximum value: less than `1`

### <code>tolerant-size-ratio</code> {#code-tolerant-size-ratio-code}

-   Controls the `balance` buffer size
-   Default value: `0` (automatically adjusts the buffer size)
-   Minimum value: `0`

### <code>enable-cross-table-merge</code> {#code-enable-cross-table-merge-code}

-   Determines whether to enable the merging of cross-table Regions
-   Default value: `true`

### <code>region-score-formula-version</code> <span class="version-mark">New in v5.0</span> {#code-region-score-formula-version-code-span-class-version-mark-new-in-v5-0-span}

-   Controls the version of the Region score formula
-   Default value: `v2`
-   Optional values: `v1` and `v2`. Compared to v1, the changes in v2 are smoother, and the scheduling jitter caused by space reclaim is improved.

> **Note:**
>
> If you have upgraded your cluster from a TiDB 4.0 version to the current version, the new formula version is automatically disabled by default to ensure consistent PD behavior before and after the upgrading. If you want to change the formula version, you need to manually switch through the `pd-ctl` setting. For details, refer to [PD Control](/pd-control.md#config-show--set-option-value--placement-rules).

### <code>enable-joint-consensus</code> <span class="version-mark">New in v5.0</span> {#code-enable-joint-consensus-code-span-class-version-mark-new-in-v5-0-span}

-   Controls whether to use Joint Consensus for replica scheduling. If this configuration is disabled, PD schedules one replica at a time.
-   Default value: `true`

### <code>hot-regions-write-interval</code> <span class="version-mark">New in v5.4.0</span> {#code-hot-regions-write-interval-code-span-class-version-mark-new-in-v5-4-0-span}

-   The time interval at which PD stores hot Region information.
-   Default value: `10m`

> **Note:**
>
> The information about hot Regions is updated every three minutes. If the interval is set to less than three minutes, updates during the interval might be meaningless.

### <code>hot-regions-reserved-days</code> <span class="version-mark">New in v5.4.0</span> {#code-hot-regions-reserved-days-code-span-class-version-mark-new-in-v5-4-0-span}

-   Specifies how many days the hot Region information is retained.
-   Default value: `7`

## <code>replication</code> {#code-replication-code}

Configuration items related to replicas

### <code>max-replicas</code> {#code-max-replicas-code}

-   The number of replicas, that is, the sum of the number of leaders and followers. The default value `3` means 1 leader and 2 followers. When this configuration is modified online, PD will schedule Regions in the background so that the number of replicas matches this configuration.
-   Default value: `3`

### <code>location-labels</code> {#code-location-labels-code}

-   The topology information of a TiKV cluster
-   Default value: `[]`
-   [Cluster topology configuration](/schedule-replicas-by-topology-labels.md)

### <code>isolation-level</code> {#code-isolation-level-code}

-   The minimum topological isolation level of a TiKV cluster
-   Default value: `""`
-   [Cluster topology configuration](/schedule-replicas-by-topology-labels.md)

### <code>strictly-match-label</code> {#code-strictly-match-label-code}

-   Enables the strict check for whether the TiKV label matches PD's `location-labels`.
-   Default value: `false`

### <code>enable-placement-rules</code> {#code-enable-placement-rules-code}

-   Enables `placement-rules`.
-   Default value: `true`
-   See [Placement Rules](/configure-placement-rules.md).

## <code>label-property</code> {#code-label-property-code}

Configuration items related to labels

### <code>key</code> {#code-key-code}

-   The label key for the store that rejected the Leader
-   Default value: `""`

### <code>value</code> {#code-value-code}

-   The label value for the store that rejected the Leader
-   Default value: `""`

## <code>dashboard</code> {#code-dashboard-code}

Configuration items related to the [TiDB Dashboard](/dashboard/dashboard-intro.md) built in PD.

### <code>tidb-cacert-path</code> {#code-tidb-cacert-path-code}

-   The path of the root CA certificate file. You can configure this path when you connect to TiDB's SQL services using TLS.
-   Default value: `""`

### <code>tidb-cert-path</code> {#code-tidb-cert-path-code}

-   The path of the SSL certificate file. You can configure this path when you connect to TiDB's SQL services using TLS.
-   Default value: `""`

### <code>tidb-key-path</code> {#code-tidb-key-path-code}

-   The path of the SSL private key file. You can configure this path when you connect to TiDB's SQL services using TLS.
-   Default value: `""`

### <code>public-path-prefix</code> {#code-public-path-prefix-code}

-   When TiDB Dashboard is accessed behind a reverse proxy, this item sets the public URL path prefix for all web resources.
-   Default value: `/dashboard`
-   Do **not** modify this configuration item when TiDB Dashboard is accessed not behind a reverse proxy; otherwise, access issues might occur. See [Use TiDB Dashboard behind a Reverse Proxy](/dashboard/dashboard-ops-reverse-proxy.md) for details.

### <code>enable-telemetry</code> {#code-enable-telemetry-code}

-   Determines whether to enable the telemetry collection feature in TiDB Dashboard.
-   Default value: `true`
-   See [Telemetry](/telemetry.md) for details.

## <code>replication-mode</code> {#code-replication-mode-code}

Configuration items related to the replication mode of all Regions. See [Enable the DR Auto-Sync mode](/two-data-centers-in-one-city-deployment.md#enable-the-dr-auto-sync-mode) for details.
