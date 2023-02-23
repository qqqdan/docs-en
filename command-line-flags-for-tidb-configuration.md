---
title: Configuration Options
summary: Learn the configuration options in TiDB.
---

# Configuration Options {#configuration-options}

When you start the TiDB cluster, you can use command-line options or environment variables to configure it. This document introduces TiDB's command options. The default TiDB ports are `4000` for client requests and `10080` for status report.

## <code>--advertise-address</code> {#code-advertise-address-code}

-   The IP address through which to log into the TiDB server
-   Default: `""`
-   This address must be accessible by the rest of the TiDB cluster and the user.

## <code>--config</code> {#code-config-code}

-   The configuration file
-   Default: `""`
-   If you have specified the configuration file, TiDB reads the configuration file. If the corresponding configuration also exists in the command line options, TiDB uses the configuration in the command line options to overwrite that in the configuration file. For detailed configuration information, see [TiDB Configuration File Description](/tidb-configuration-file.md).

## <code>--config-check</code> {#code-config-check-code}

-   Checks the validity of the configuration file and exits
-   Default: `false`

## <code>--config-strict</code> {#code-config-strict-code}

-   Enforces the validity of the configuration file
-   Default: `false`

## <code>--cors</code> {#code-cors-code}

-   Specifies the `Access-Control-Allow-Origin` value for Cross-Origin Request Sharing (CORS) request of the TiDB HTTP status service
-   Default: `""`

## <code>--enable-binlog</code> {#code-enable-binlog-code}

-   Enables or disables TiDB binlog generation
-   Default: `false`

## <code>--host</code> {#code-host-code}

-   The host address that the TiDB server monitors
-   Default: `"0.0.0.0"`
-   The TiDB server monitors this address.
-   The `"0.0.0.0"` address monitors all network cards by default. If you have multiple network cards, specify the network card that provides service, such as `192.168.100.113`.

## <code>--initialize-insecure</code> {#code-initialize-insecure-code}

-   Bootstraps tidb-server in insecure mode
-   Default: `true`

## <code>--initialize-secure</code> {#code-initialize-secure-code}

-   Bootstraps tidb-server in secure mode
-   Default: `false`

## <code>-L</code> {#code-l-code}

-   The log level
-   Default: `"info"`
-   Optional values: `"debug"`, `"info"`, `"warn"`, `"error"`, `"fatal"`

## <code>--lease</code> {#code-lease-code}

-   The duration of the schema lease. It is **dangerous** to change the value unless you know what you do.
-   Default: `45s`

## <code>--log-file</code> {#code-log-file-code}

-   The log file
-   Default: `""`
-   If this option is not set, logs are output to "stderr". If this option is set, logs are output to the corresponding file.

## <code>--log-slow-query</code> {#code-log-slow-query-code}

-   The directory for the slow query log
-   Default: `""`
-   If this option is not set, logs are output to the file specified by `--log-file` by default.

## <code>--metrics-addr</code> {#code-metrics-addr-code}

-   The Prometheus Pushgateway address
-   Default: `""`
-   Leaving it empty stops the Prometheus client from pushing.
-   The format is `--metrics-addr=192.168.100.115:9091`.

## <code>--metrics-interval</code> {#code-metrics-interval-code}

-   The Prometheus client push interval in seconds
-   Default: `15s`
-   Setting the value to 0 stops the Prometheus client from pushing.

## <code>-P</code> {#code-p-code}

-   The monitoring port of TiDB services
-   Default: `"4000"`
-   The TiDB server accepts MySQL client requests from this port.

## <code>--path</code> {#code-path-code}

-   The path to the data directory for local storage engine like "unistore"
-   For `--store = tikv`, you must specify the path; for `--store = unistore`, the default value is used if you do not specify the path.
-   For the distributed storage engine like TiKV, `--path` specifies the actual PD address. Assuming that you deploy the PD server on 192.168.100.113:2379, 192.168.100.114:2379 and 192.168.100.115:2379, the value of `--path` is "192.168.100.113:2379, 192.168.100.114:2379, 192.168.100.115:2379".
-   Default: `"/tmp/tidb"`
-   You can use `tidb-server --store=unistore --path=""` to enable a pure in-memory TiDB.

## <code>--proxy-protocol-networks</code> {#code-proxy-protocol-networks-code}

-   The list of proxy server's IP addresses allowed to connect to TiDB using the [PROXY protocol](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt).
-   Default: `""`
-   In general cases, when you access TiDB behind a reverse proxy, TiDB takes the IP address of the reverse proxy server as the IP address of the client. By enabling the PROXY protocol, reverse proxies that support this protocol such as HAProxy can pass the real client IP address to TiDB.
-   After configuring this flag, TiDB allows the configured source IP address to connect to TiDB using the PROXY protocol; if a protocol other than PROXY is used, this connection will be denied. If this flag is left empty, no IP address can connect to TiDB using the PROXY protocol. The value can be the IP address (192.168.1.50) or CIDR (192.168.1.0/24) with `,` as the separator. `*` means any IP addresses.

> **Warning:**
>
> Use `*` with caution because it might introduce security risks by allowing a client of any IP address to report its IP address. In addition, using `*` might also cause the internal component that directly connects to TiDB (such as TiDB Dashboard) to be unavailable.

## <code>--proxy-protocol-header-timeout</code> {#code-proxy-protocol-header-timeout-code}

-   Timeout for the PROXY protocol header read
-   Default: `5` (seconds)

    > **Note:**
    >
    > Do not set the value to `0`. Use the default value except for special situations.

## <code>--report-status</code> {#code-report-status-code}

-   Enables (`true`) or disables (`false`) the status report and pprof tool
-   Default: `true`
-   When set to `true`, this parameter enables metrics and pprof. When set to `false`, this parameter disables metrics and pprof.

## <code>--run-ddl</code> {#code-run-ddl-code}

-   To see whether the `tidb-server` runs DDL statements, and set when the number of `tidb-server` is over two in the cluster
-   Default: `true`
-   The value can be (true) or (false). (true) indicates the `tidb-server` runs DDL itself. (false) indicates the `tidb-server` does not run DDL itself.

## <code>--socket string</code> {#code-socket-string-code}

-   The TiDB services use the unix socket file for external connections.
-   Default: `""`
-   Use `/tmp/tidb.sock` to open the unix socket file.

## <code>--status</code> {#code-status-code}

-   The status report port for TiDB server
-   Default: `"10080"`
-   This port is used to get server internal data. The data includes [Prometheus metrics](https://prometheus.io/) and [pprof](https://golang.org/pkg/net/http/pprof/).
-   Prometheus metrics can be accessed by `"http://host:status_port/metrics"`.
-   pprof data can be accessed by `"http://host:status_port/debug/pprof"`.

## <code>--status-host</code> {#code-status-host-code}

-   The `HOST` used to monitor the status of TiDB service
-   Default: `0.0.0.0`

## <code>--store</code> {#code-store-code}

-   Specifies the storage engine used by TiDB in the bottom layer
-   Default: `"unistore"`
-   You can choose "unistore" or "tikv". ("unistore" is the local storage engine; "tikv" is a distributed storage engine)

## <code>--temp-dir</code> {#code-temp-dir-code}

-   The temporary directory of TiDB
-   Default: `"/tmp/tidb"`

## <code>--token-limit</code> {#code-token-limit-code}

-   The number of sessions allowed to run concurrently in TiDB. It is used for traffic control.
-   Default: `1000`
-   If the number of the concurrent sessions is larger than `token-limit`, the request is blocked and waiting for the operations which have been finished to release tokens.

## <code>-V</code> {#code-v-code}

-   Outputs the version of TiDB
-   Default: `""`

## <code>--plugin-dir</code> {#code-plugin-dir-code}

-   The storage directory for plugins.
-   Default: `"/data/deploy/plugin"`

## <code>--plugin-load</code> {#code-plugin-load-code}

-   The names of the plugins to be loaded, each separated by a comma.
-   Default: `""`

## <code>--affinity-cpus</code> {#code-affinity-cpus-code}

-   Sets the CPU affinity of TiDB servers, which is separated by commas. For example, "1,2,3".
-   Default: `""`

## <code>--repair-mode</code> {#code-repair-mode-code}

-   Determines whether to enable the repair mode, which is only used in the data repair scenario.
-   Default: `false`

## <code>--repair-list</code> {#code-repair-list-code}

-   The names of the tables to be repaired in the repair mode.
-   Default: `""`
