---
title: TiDB Dashboard FAQs
summary: Learn about the frequently asked questions (FAQs) and answers about TiDB Dashboard.
---

# TiDB Dashboard FAQs {#tidb-dashboard-faqs}

This document summarizes the frequently asked questions (FAQs) and answers about TiDB Dashboard. If a problem cannot be located or persists after you perform as instructed, [get support](/support.md) from PingCAP or the community.

## Access-related FAQ {#access-related-faq}

### When the firewall or reverse proxy is configured, I am redirected to an internal address other than TiDB Dashboard {#when-the-firewall-or-reverse-proxy-is-configured-i-am-redirected-to-an-internal-address-other-than-tidb-dashboard}

When multiple Placement Driver (PD) instances are deployed in a cluster, only one of the PD instances actually runs the TiDB Dashboard service. If you access other PD instances instead of this one, your browser redirects you to another address. If the firewall or reverse proxy is not properly configured for accessing TiDB Dashboard, when you visit the Dashboard, you might be redirected to an internal address that is protected by the firewall or reverse proxy.

-   See [TiDB Dashboard Multi-PD Instance Deployment](/dashboard/dashboard-ops-deploy.md) to learn the working principle of TiDB Dashboard with multiple PD instances.
-   See [Use TiDB Dashboard through a Reverse Proxy](/dashboard/dashboard-ops-reverse-proxy.md) to learn how to correctly configure a reverse proxy.
-   See [Secure TiDB Dashboard](/dashboard/dashboard-ops-security.md) to learn how to correctly configure the firewall.

### When TiDB Dashboard is deployed with dual network interface cards (NICs), TiDB Dashboard cannot be accessed using another NIC {#when-tidb-dashboard-is-deployed-with-dual-network-interface-cards-nics-tidb-dashboard-cannot-be-accessed-using-another-nic}

For security reasons, TiDB Dashboard on PD only monitors the IP addresses specified during deployment (that is, it only listens on one NIC), not on `0.0.0.0`. Therefore, when multiple NICs are installed on the host, you cannot access TiDB Dashboard using another NIC.

If you have deployed TiDB using the `tiup cluster` or `tiup playground` command, currently this problem cannot be solved. It is recommended that you use a reverse proxy to safely expose TiDB Dashboard to another NIC. For details, see [Use TiDB Dashboard behind a Reverse Proxy](/dashboard/dashboard-ops-reverse-proxy.md).

## UI-related FAQ {#ui-related-faq}

### A <code>prometheus_not_found</code> error is shown in <strong>QPS</strong> and <strong>Latency</strong> sections on the Overview page {#a-code-prometheus-not-found-code-error-is-shown-in-strong-qps-strong-and-strong-latency-strong-sections-on-the-overview-page}

The **QPS** and <strong>Latency</strong> sections on the <strong>Overview</strong> page require a cluster with Prometheus deployed. Otherwise, the error is shown. You can solve this problem by deploying a Prometheus instance in the cluster.

If you still encounter this problem when the Prometheus instance has been deployed, the possible reason is that your deployment tool is out of date (TiUP or TiDB Operator), and your tool does not automatically report metrics addresses, which makes TiDB Dashboard unable to query metrics. You can upgrade you deployment tool to the latest version and try again.

If your deployment tool is TiUP, take the following steps to solve this problem. For other deployment tools, refer to the corresponding documents of those tools.

1.  Upgrade TiUP and TiUP Cluster:

    {{< copyable "" >}}

    ```bash
    tiup update --self
    tiup update cluster --force
    ```

2.  After the upgrade, when a new cluster is deployed with Prometheus instances, the metrics can be displayed normally.

3.  After the upgrade, for an existing cluster, you can restart this cluster to report the metrics addresses. Replace `CLUSTER_NAME` with the actual cluster name:

    {{< copyable "" >}}

    ```bash
    tiup cluster start CLUSTER_NAME
    ```

    Even if the cluster has been started, still execute this command. This command does not affect the normal application in the cluster, but refreshes and reports the metrics addresses, so that the monitoring metrics can be displayed normally in TiDB Dashboard.

### An <code>invalid connection</code> error is shown on the <strong>Slow Queries</strong> page {#an-code-invalid-connection-code-error-is-shown-on-the-strong-slow-queries-strong-page}

The possible reason is that you have enabled the Prepared Plan Cache feature of TiDB. As an experimental feature, when enabled, Prepared Plan Cache might not function properly in specific TiDB versions, which could cause this problem in TiDB Dashboard (and other applications). You can disable Prepared Plan Cache by setting the system variable [`tidb_enable_prepared_plan_cache = OFF`](/system-variables.md#tidb_enable_prepared_plan_cache-new-in-v610).

### A <code>required component NgMonitoring is not started</code> error is shown {#a-code-required-component-ngmonitoring-is-not-started-code-error-is-shown}

NgMonitoring is an advanced monitoring component built in TiDB clusters of v5.4.0 and later versions to support TiDB Dashboard features such as **Continuous Profiling** and <strong>Top SQL</strong>. NgMonitoring is automatically deployed when you deploy or upgrade a cluster with a newer version of TiUP. For clusters deployed using TiDB Operator, you can deploy NgMonitoring manually by referring to [Enable Continuous Profiling](https://docs.pingcap.com/tidb-in-kubernetes/dev/access-dashboard/#enable-continuous-profiling).

If the web page shows `required component NgMonitoring is not started`, you can troubleshoot the deployment issue as follows:

<details>
  <summary>Clusters Deployed using TiUP</summary>

Step 1. Check versions

1.  Check the TiUP cluster version. NgMonitoring is deployed only when TiUP is v1.9.0 or later.

    {{< copyable "" >}}

    ```shell
    tiup cluster --version
    ```

    The command output shows the TiUP version. For example:

    ```
    tiup version 1.9.0 tiup
    Go Version: go1.17.2
    Git Ref: v1.9.0
    ```

2.  If the TiUP cluster version is earlier than v1.9.0, upgrade TiUP and TiUP cluster to the latest version:

    {{< copyable "" >}}

    ```shell
    tiup update --all
    ```

Step 2. Add the ng_port configuration item on the control machine by using TiUP. Then reload Prometheus.

1.  Open the cluster configuration file in editing mode:

    {{< copyable "" >}}

    ```shell
    tiup cluster edit-config ${cluster-name}
    ```

2.  Under `monitoring_servers`, add the `ng_port:12020` parameter:

    ```
    monitoring_servers:
    - host: 172.16.6.6
      ng_port: 12020
    ```

3.  Reload Prometheus:

    {{< copyable "" >}}

    ```shell
    tiup cluster reload ${cluster-name} --role prometheus
    ```

If the error message is still prompted after performing steps above, [get support](/support.md) from PingCAP or the community.

</details>

<details>
  <summary>Clusters Deployed using TiDB Operator</summary>

Deploy the NgMonitoring component by following instructions in the [Enable Continuous Profiling](https://docs.pingcap.com/tidb-in-kubernetes/dev/access-dashboard/#enable-continuous-profiling) section in TiDB Operator documentation.

</details>

<details>
  <summary>Clusters Started using TiUP Playground</summary>

When starting the cluster, TiUP Playground (>= v1.8.0) automatically starts the NgMonitoring component. To update TiUP Playground to the latest version, run the following command:

{{< copyable "" >}}

```shell
tiup update --self
tiup update playground
```

</details>

### An <code>unknown field</code> error is shown on the <strong>Slow Queries</strong> page {#an-code-unknown-field-code-error-is-shown-on-the-strong-slow-queries-strong-page}

If the `unknown field` error appears on the **Slow Queries** page after the cluster upgrade, the error is related to a compatibility issue caused by the difference between TiDB Dashboard server fields (which might be updated) and user preferences fields (which are in the browser cache). This issue has been fixed. If your cluster is earlier than v5.0.3 or v4.0.14, perform the following steps to clear your browser cache:

1.  Open TiDB Dashboard page.

2.  Open Developer Tools. Different browsers have different ways of opening Developer Tools. After clicking the **Menu Bar**:

    -   Firefox: **Menu** > <strong>Web Developer</strong> > <strong>Toggle Tools</strong>, or <strong>Tools</strong> > <strong>Web Developer</strong> > <strong>Toggle Tools</strong>.
    -   Chrome: **More tools** > <strong>Developer tools</strong>.
    -   Safari: **Develop** > <strong>Show Web Inspector</strong>. If you can't see the <strong>Develop</strong> menu, go to <strong>Safari</strong> > <strong>Preferences</strong> > <strong>Advanced</strong>, and check the <strong>Show Develop</strong> menu in menu bar checkbox.

    In the following example, Chrome is used.

    ![Opening DevTools from Chrome's main menu](/media/dashboard/dashboard-faq-devtools.png)

3.  Select the **Application** panel, expand the <strong>Local Storage</strong> menu and select the <strong>TiDB Dashboard page domain</strong>. Click the <strong>Clear All</strong> button.

    ![Clear the Local Storage](/media/dashboard/dashboard-faq-devtools-application.png)
