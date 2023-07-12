---
title: Download TiDB Tools
summary: Download the most officially maintained versions of TiDB tools.
---

# Download TiDB Tools {#download-tidb-tools}

This document describes how to download the TiDB Toolkit.

TiDB Toolkit contains frequently used TiDB tools, such as data export tool Dumpling, data import tool TiDB Lightning, and backup and restore tool BR.

> **Tip:**
>
> -   If your deployment environment has internet access, you can deploy a TiDB tool using a single [<a href="/tiup/tiup-component-management.md">TiUP command</a>](/tiup/tiup-component-management.md), so there is no need to download the TiDB Toolkit separately.
> -   If you need to deploy and maintain TiDB on Kubernetes, instead of downloading the TiDB Toolkit, follow the steps in [<a href="https://docs.pingcap.com/tidb-in-kubernetes/stable/deploy-tidb-operator#offline-installation">TiDB Operator offline installation</a>](https://docs.pingcap.com/tidb-in-kubernetes/stable/deploy-tidb-operator#offline-installation).

## Environment requirements {#environment-requirements}

-   Operating system: Linux
-   Architecture: amd64

## Download link {#download-link}

You can download TiDB Toolkit from the following link:

```
https://download.pingcap.org/tidb-community-toolkit-{version}-linux-amd64.tar.gz
```

`{version}` in the link indicates the version number of TiDB. For example, the download link for `v6.1.7` is `https://download.pingcap.org/tidb-community-toolkit-v6.1.7-linux-amd64.tar.gz`.

> **Note:**
>
> If you need to download the [<a href="/pd-control.md">PD Control</a>](/pd-control.md) tool `pd-ctl`, download the TiDB installation package separately from `https://download.pingcap.org/tidb-community-server-{version}-linux-amd64.tar.gz`.

## TiDB Toolkit description {#tidb-toolkit-description}

Depending on which tools you want to use, you can install the corresponding offline packages as follows:

| Tool                                                                                                                                           | Offline package name                                                                                                                                            |
| :--------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [<a href="/tiup/tiup-overview.md">TiUP</a>](/tiup/tiup-overview.md)                                                                            | `tiup-linux-amd64.tar.gz` <br/>`tiup-{tiup-version}-linux-amd64.tar.gz` <br/>`dm-{tiup-version}-linux-amd64.tar.gz` <br/> `server-{version}-linux-amd64.tar.gz` |
| [<a href="/dumpling-overview.md">Dumpling</a>](/dumpling-overview.md)                                                                          | `dumpling-{version}-linux-amd64.tar.gz`                                                                                                                         |
| [<a href="/tidb-lightning/tidb-lightning-overview.md">TiDB Lightning</a>](/tidb-lightning/tidb-lightning-overview.md)                          | `tidb-lightning-ctl` <br/>`tidb-lightning-{version}-linux-amd64.tar.gz`                                                                                         |
| [<a href="/dm/dm-overview.md">TiDB Data Migration (DM)</a>](/dm/dm-overview.md)                                                                | `dm-worker-{version}-linux-amd64.tar.gz` <br/>`dm-master-{version}-linux-amd64.tar.gz` <br/>`dmctl-{version}-linux-amd64.tar.gz`                                |
| [<a href="/ticdc/ticdc-overview.md">TiCDC</a>](/ticdc/ticdc-overview.md)                                                                       | `cdc-{version}-linux-amd64.tar.gz`                                                                                                                              |
| [<a href="/tidb-binlog/tidb-binlog-overview.md">TiDB Binlog</a>](/tidb-binlog/tidb-binlog-overview.md)                                         | `pump-{version}-linux-amd64.tar.gz` <br/>`drainer-{version}-linux-amd64.tar.gz` <br/>`binlogctl` <br/>`reparo`                                                  |
| [<a href="/br/backup-and-restore-overview.md">Backup &#x26; Restore (BR)</a>](/br/backup-and-restore-overview.md)                              | `br-{version}-linux-amd64.tar.gz`                                                                                                                               |
| [<a href="/sync-diff-inspector/sync-diff-inspector-overview.md">sync-diff-inspector</a>](/sync-diff-inspector/sync-diff-inspector-overview.md) | `sync_diff_inspector`                                                                                                                                           |
| [<a href="/tispark-overview.md">TiSpark</a>](/tispark-overview.md)                                                                             | `tispark-{tispark-version}-any-any.tar.gz` <br/>`spark-{spark-version}-any-any.tar.gz`                                                                          |
| [<a href="/pd-recover.md">PD Recover</a>](/pd-recover.md)                                                                                      | `pd-recover-{version}-linux-amd64.tar`                                                                                                                          |
