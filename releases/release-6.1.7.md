---
title: TiDB 6.1.7 Release Notes
summary: Learn about the improvements and bug fixes in TiDB 6.1.7.
---

# TiDB 6.1.7 Release Notes {#tidb-6-1-7-release-notes}

Release date: July 12, 2023

TiDB version: 6.1.7

Quick access: [<a href="https://docs.pingcap.com/tidb/v6.1/quick-start-with-tidb">Quick start</a>](https://docs.pingcap.com/tidb/v6.1/quick-start-with-tidb) | [<a href="https://docs.pingcap.com/tidb/v6.1/production-deployment-using-tiup">Production deployment</a>](https://docs.pingcap.com/tidb/v6.1/production-deployment-using-tiup) | [<a href="https://www.pingcap.com/download/?version=v6.1.7#version-list">Installation packages</a>](https://www.pingcap.com/download/?version=v6.1.7#version-list)

## Improvements {#improvements}

-   TiDB

    -   Use pessimistic transactions in internal transaction retry to avoid retry failure and reduce time consumption [<a href="https://github.com/pingcap/tidb/issues/38136">#38136</a>](https://github.com/pingcap/tidb/issues/38136) @[<a href="https://github.com/jackysp">jackysp</a>](https://github.com/jackysp)

-   Tools

    -   TiCDC

        -   Support batch `UPDATE` DML statements to improve TiCDC replication performance [<a href="https://github.com/pingcap/tiflow/issues/8084">#8084</a>](https://github.com/pingcap/tiflow/issues/8084) @[<a href="https://github.com/amyangfei">amyangfei</a>](https://github.com/amyangfei)

    -   TiDB Lightning

        -   Verify checksum through SQL after the import to improve stability of verification [<a href="https://github.com/pingcap/tidb/issues/41941">#41941</a>](https://github.com/pingcap/tidb/issues/41941) @[<a href="https://github.com/GMHDBJD">GMHDBJD</a>](https://github.com/GMHDBJD)

## Bug fixes {#bug-fixes}

-   TiDB

    -   Fix the panic issue caused by empty `processInfo` [<a href="https://github.com/pingcap/tidb/issues/43829">#43829</a>](https://github.com/pingcap/tidb/issues/43829) @[<a href="https://github.com/zimulala">zimulala</a>](https://github.com/zimulala)
    -   Fix the issue that `resolve lock` might hang when there is a sudden change in PD time [<a href="https://github.com/pingcap/tidb/issues/44822">#44822</a>](https://github.com/pingcap/tidb/issues/44822) @[<a href="https://github.com/zyguan">zyguan</a>](https://github.com/zyguan)
    -   Fix the issue that queries containing Common Table Expressions (CTEs) might cause insufficient disk space [<a href="https://github.com/pingcap/tidb/issues/44477">#44477</a>](https://github.com/pingcap/tidb/issues/44477) @[<a href="https://github.com/guo-shaoge">guo-shaoge</a>](https://github.com/guo-shaoge)
    -   Fix the issue that using CTEs and correlated subqueries simultaneously might result in incorrect query results or panic [<a href="https://github.com/pingcap/tidb/issues/44649">#44649</a>](https://github.com/pingcap/tidb/issues/44649) [<a href="https://github.com/pingcap/tidb/issues/38170">#38170</a>](https://github.com/pingcap/tidb/issues/38170) [<a href="https://github.com/pingcap/tidb/issues/44774">#44774</a>](https://github.com/pingcap/tidb/issues/44774) @[<a href="https://github.com/winoros">winoros</a>](https://github.com/winoros) @[<a href="https://github.com/guo-shaoge">guo-shaoge</a>](https://github.com/guo-shaoge)
    -   Fix the issue that the query result of the `SELECT CAST(n AS CHAR)` statement is incorrect when `n` in the statement is a negative number [<a href="https://github.com/pingcap/tidb/issues/44786">#44786</a>](https://github.com/pingcap/tidb/issues/44786) @[<a href="https://github.com/xhebox">xhebox</a>](https://github.com/xhebox)
    -   Fix the query panic issue of TiDB in certain cases [<a href="https://github.com/pingcap/tidb/issues/40857">#40857</a>](https://github.com/pingcap/tidb/issues/40857) @[<a href="https://github.com/Dousir9">Dousir9</a>](https://github.com/Dousir9)
    -   Fix the issue that SQL compile error logs are not redacted [<a href="https://github.com/pingcap/tidb/issues/41831">#41831</a>](https://github.com/pingcap/tidb/issues/41831) @[<a href="https://github.com/lance6716">lance6716</a>](https://github.com/lance6716)
    -   Fix the issue that the `SELECT` statement returns an error for a partitioned table if the table partition definition uses the `FLOOR()` function to round a partitioned column [<a href="https://github.com/pingcap/tidb/issues/42323">#42323</a>](https://github.com/pingcap/tidb/issues/42323) @[<a href="https://github.com/jiyfhust">jiyfhust</a>](https://github.com/jiyfhust)
    -   Fix the issue that querying partitioned tables might cause errors during Region split [<a href="https://github.com/pingcap/tidb/issues/43144">#43144</a>](https://github.com/pingcap/tidb/issues/43144) @[<a href="https://github.com/lcwangchao">lcwangchao</a>](https://github.com/lcwangchao)
    -   Fix the issue of unnecessary memory usage during reading statistical information [<a href="https://github.com/pingcap/tidb/issues/42052">#42052</a>](https://github.com/pingcap/tidb/issues/42052) @[<a href="https://github.com/xuyifangreeneyes">xuyifangreeneyes</a>](https://github.com/xuyifangreeneyes)
    -   Fix the issue of excessive memory usage after creating a large number of empty partitioned tables [<a href="https://github.com/pingcap/tidb/issues/44308">#44308</a>](https://github.com/pingcap/tidb/issues/44308) @[<a href="https://github.com/hawkingrei">hawkingrei</a>](https://github.com/hawkingrei)
    -   Fix the issue that queries might return incorrect results when `tidb_opt_agg_push_down` is enabled [<a href="https://github.com/pingcap/tidb/issues/44795">#44795</a>](https://github.com/pingcap/tidb/issues/44795) @[<a href="https://github.com/AilinKid">AilinKid</a>](https://github.com/AilinKid)
    -   Fix the issue that the join result of common table expressions might be wrong [<a href="https://github.com/pingcap/tidb/issues/38170">#38170</a>](https://github.com/pingcap/tidb/issues/38170) @[<a href="https://github.com/wjhuang2016">wjhuang2016</a>](https://github.com/wjhuang2016)
    -   Fix the issue that in some rare cases, residual pessimistic locks of pessimistic transactions might affect data correctness when GC resolves locks [<a href="https://github.com/pingcap/tidb/issues/43243">#43243</a>](https://github.com/pingcap/tidb/issues/43243) @[<a href="https://github.com/MyonKeminta">MyonKeminta</a>](https://github.com/MyonKeminta)
    -   Fix the issue that after a new column is added in the cache table, the value is `NULL` instead of the default value of the column [<a href="https://github.com/pingcap/tidb/issues/42928">#42928</a>](https://github.com/pingcap/tidb/issues/42928) @[<a href="https://github.com/lqs">lqs</a>](https://github.com/lqs)
    -   Fix the issue that TiDB returns an error when the corresponding rows in partitioned tables cannot be found in the probe phase of index join [<a href="https://github.com/pingcap/tidb/issues/43686">#43686</a>](https://github.com/pingcap/tidb/issues/43686) @[<a href="https://github.com/AilinKid">AilinKid</a>](https://github.com/AilinKid) @[<a href="https://github.com/mjonss">mjonss</a>](https://github.com/mjonss)
    -   Fix the issue that dropping a database causes slow GC progress [<a href="https://github.com/pingcap/tidb/issues/33069">#33069</a>](https://github.com/pingcap/tidb/issues/33069) @[<a href="https://github.com/tiancaiamao">tiancaiamao</a>](https://github.com/tiancaiamao)
    -   Fix the issue that data and indexes are inconsistent when the `ON UPDATE` statement does not correctly update the primary key [<a href="https://github.com/pingcap/tidb/issues/44565">#44565</a>](https://github.com/pingcap/tidb/issues/44565) @[<a href="https://github.com/zyguan">zyguan</a>](https://github.com/zyguan)
    -   Fix the issue that TiCDC might lose some row changes during table renaming [<a href="https://github.com/pingcap/tidb/issues/43338">#43338</a>](https://github.com/pingcap/tidb/issues/43338) @[<a href="https://github.com/tangenta">tangenta</a>](https://github.com/tangenta)
    -   Fix the behavior issue of Placement Rules in partitioned tables, so that the Placement Rules in deleted partitions can be correctly set and recycled [<a href="https://github.com/pingcap/tidb/issues/44116">#44116</a>](https://github.com/pingcap/tidb/issues/44116) @[<a href="https://github.com/lcwangchao">lcwangchao</a>](https://github.com/lcwangchao)
    -   Fix the issue that when `tidb_scatter_region` is enabled, Region does not automatically split after a partition is truncated [<a href="https://github.com/pingcap/tidb/issues/43174">#43174</a>](https://github.com/pingcap/tidb/issues/43174) [<a href="https://github.com/pingcap/tidb/issues/43028">#43028</a>](https://github.com/pingcap/tidb/issues/43028)
    -   Fix the issue of DDL retry caused by write conflict when executing `TRUNCATE TABLE` for partitioned tables with many partitions and TiFlash replicas [<a href="https://github.com/pingcap/tidb/issues/42940">#42940</a>](https://github.com/pingcap/tidb/issues/42940) @[<a href="https://github.com/mjonss">mjonss</a>](https://github.com/mjonss)
    -   Fix the issue of incorrect execution plans when pushing down window functions to TiFlash [<a href="https://github.com/pingcap/tidb/issues/43922">#43922</a>](https://github.com/pingcap/tidb/issues/43922) @[<a href="https://github.com/gengliqi">gengliqi</a>](https://github.com/gengliqi)
    -   Fix the issue that incorrect results might be returned when using a common table expression (CTE) in statements with non-correlated subqueries [<a href="https://github.com/pingcap/tidb/issues/44051">#44051</a>](https://github.com/pingcap/tidb/issues/44051) @[<a href="https://github.com/winoros">winoros</a>](https://github.com/winoros)
    -   Fix the issue that using `memTracker` with cursor fetch causes memory leaks [<a href="https://github.com/pingcap/tidb/issues/44254">#44254</a>](https://github.com/pingcap/tidb/issues/44254) @[<a href="https://github.com/YangKeao">YangKeao</a>](https://github.com/YangKeao)
    -   Fix the issue that the data length in the `QUERY` column of the `INFORMATION_SCHEMA.DDL_JOBS` table might exceed the column definition [<a href="https://github.com/pingcap/tidb/issues/42440">#42440</a>](https://github.com/pingcap/tidb/issues/42440) @[<a href="https://github.com/tiancaiamao">tiancaiamao</a>](https://github.com/tiancaiamao)
    -   Fix the issue that the `min, max` query result is incorrect [<a href="https://github.com/pingcap/tidb/issues/43805">#43805</a>](https://github.com/pingcap/tidb/issues/43805) @[<a href="https://github.com/wshwsh12">wshwsh12</a>](https://github.com/wshwsh12)
    -   Fix the issue that TiDB reports syntax errors when analyzing tables [<a href="https://github.com/pingcap/tidb/issues/43392">#43392</a>](https://github.com/pingcap/tidb/issues/43392) @[<a href="https://github.com/guo-shaoge">guo-shaoge</a>](https://github.com/guo-shaoge)
    -   Fix the issue that the `SHOW PROCESSLIST` statement cannot display the TxnStart of the transaction of the statement with a long subquery time [<a href="https://github.com/pingcap/tidb/issues/40851">#40851</a>](https://github.com/pingcap/tidb/issues/40851) @[<a href="https://github.com/crazycs520">crazycs520</a>](https://github.com/crazycs520)
    -   Fix the issue of missing table names in the `ADMIN SHOW DDL JOBS` result when a `DROP TABLE` operation is being executed [<a href="https://github.com/pingcap/tidb/issues/42268">#42268</a>](https://github.com/pingcap/tidb/issues/42268) @[<a href="https://github.com/tiancaiamao">tiancaiamao</a>](https://github.com/tiancaiamao)
    -   Fix the issue of displaying the incorrect TiDB address in IPv6 environment [<a href="https://github.com/pingcap/tidb/issues/43260">#43260</a>](https://github.com/pingcap/tidb/issues/43260) @[<a href="https://github.com/nexustar">nexustar</a>](https://github.com/nexustar)
    -   Fix the issue that the SQL statement reports the `runtime error: index out of range` error when using the `AES_DECRYPT` expression [<a href="https://github.com/pingcap/tidb/issues/43063">#43063</a>](https://github.com/pingcap/tidb/issues/43063) @[<a href="https://github.com/lcwangchao">lcwangchao</a>](https://github.com/lcwangchao)
    -   Fix the issue that there is no warning when using `SUBPARTITION` to create partitioned tables [<a href="https://github.com/pingcap/tidb/issues/41198">#41198</a>](https://github.com/pingcap/tidb/issues/41198) [<a href="https://github.com/pingcap/tidb/issues/41200">#41200</a>](https://github.com/pingcap/tidb/issues/41200) @[<a href="https://github.com/mjonss">mjonss</a>](https://github.com/mjonss)
    -   Fix the issue that the query with CTE causes TiDB to hang [<a href="https://github.com/pingcap/tidb/issues/43749">#43749</a>](https://github.com/pingcap/tidb/issues/43749) [<a href="https://github.com/pingcap/tidb/issues/36896">#36896</a>](https://github.com/pingcap/tidb/issues/36896) @[<a href="https://github.com/guo-shaoge">guo-shaoge</a>](https://github.com/guo-shaoge)
    -   Fix the issue that truncating a partition of a partitioned table might cause the Placement Rule of the partition to become invalid [<a href="https://github.com/pingcap/tidb/issues/44031">#44031</a>](https://github.com/pingcap/tidb/issues/44031) @[<a href="https://github.com/lcwangchao">lcwangchao</a>](https://github.com/lcwangchao)
    -   Fix the issue that CTE results are incorrect when pushing down predicates [<a href="https://github.com/pingcap/tidb/issues/43645">#43645</a>](https://github.com/pingcap/tidb/issues/43645) @[<a href="https://github.com/winoros">winoros</a>](https://github.com/winoros)
    -   Fix the issue that `auto-commit` change affects transaction commit behaviours [<a href="https://github.com/pingcap/tidb/issues/36581">#36581</a>](https://github.com/pingcap/tidb/issues/36581) @[<a href="https://github.com/cfzjywxk">cfzjywxk</a>](https://github.com/cfzjywxk)

-   TiKV

    -   Fix the issue that TiDB Lightning might cause SST file leakage [<a href="https://github.com/tikv/tikv/issues/14745">#14745</a>](https://github.com/tikv/tikv/issues/14745) @[<a href="https://github.com/YuJuncen">YuJuncen</a>](https://github.com/YuJuncen)
    -   Fix the issue that encryption key ID conflict might cause the deletion of the old keys [<a href="https://github.com/tikv/tikv/issues/14585">#14585</a>](https://github.com/tikv/tikv/issues/14585) @[<a href="https://github.com/tabokie">tabokie</a>](https://github.com/tabokie)
    -   Fix the issue of file handle leakage in Continuous Profiling [<a href="https://github.com/tikv/tikv/issues/14224">#14224</a>](https://github.com/tikv/tikv/issues/14224) @[<a href="https://github.com/tabokie">tabokie</a>](https://github.com/tabokie)

-   PD

    -   Fix the issue that gRPC returns errors with unexpected formats [<a href="https://github.com/tikv/pd/issues/5161">#5161</a>](https://github.com/tikv/pd/issues/5161) @[<a href="https://github.com/HuSharp">HuSharp</a>](https://github.com/HuSharp)

-   Tools

    -   Backup &#x26; Restore (BR)

        -   Fix the issue that `resolved lock timeout` is falsely reported in some cases [<a href="https://github.com/pingcap/tidb/issues/43236">#43236</a>](https://github.com/pingcap/tidb/issues/43236) @[<a href="https://github.com/YuJuncen">YuJuncen</a>](https://github.com/YuJuncen)
        -   Fix the issue of backup slowdown when a TiKV node crashes in a cluster [<a href="https://github.com/pingcap/tidb/issues/42973">#42973</a>](https://github.com/pingcap/tidb/issues/42973) @[<a href="https://github.com/YuJuncen">YuJuncen</a>](https://github.com/YuJuncen)

    -   TiCDC

        -   Fix the issue that TiCDC cannot create a changefeed with a downstream Kafka-on-Pulsar [<a href="https://github.com/pingcap/tiflow/issues/8892">#8892</a>](https://github.com/pingcap/tiflow/issues/8892) @[<a href="https://github.com/hi-rustin">hi-rustin</a>](https://github.com/hi-rustin)
        -   Fix the issue that TiCDC cannot automatically recover when PD address or leader fails [<a href="https://github.com/pingcap/tiflow/issues/8812">#8812</a>](https://github.com/pingcap/tiflow/issues/8812) [<a href="https://github.com/pingcap/tiflow/issues/8877">#8877</a>](https://github.com/pingcap/tiflow/issues/8877) @[<a href="https://github.com/asddongmen">asddongmen</a>](https://github.com/asddongmen)
        -   Fix the issue that when the downstream is Kafka, TiCDC queries the downstream metadata too frequently and causes excessive workload in the downstream [<a href="https://github.com/pingcap/tiflow/issues/8957">#8957</a>](https://github.com/pingcap/tiflow/issues/8957) [<a href="https://github.com/pingcap/tiflow/issues/8959">#8959</a>](https://github.com/pingcap/tiflow/issues/8959) @[<a href="https://github.com/hi-rustin">hi-rustin</a>](https://github.com/hi-rustin)
        -   Fix the issue that TiCDC gets stuck when PD fails such as network isolation or PD Owner node reboot [<a href="https://github.com/pingcap/tiflow/issues/8808">#8808</a>](https://github.com/pingcap/tiflow/issues/8808) [<a href="https://github.com/pingcap/tiflow/issues/8812">#8812</a>](https://github.com/pingcap/tiflow/issues/8812) [<a href="https://github.com/pingcap/tiflow/issues/8877">#8877</a>](https://github.com/pingcap/tiflow/issues/8877) @[<a href="https://github.com/asddongmen">asddongmen</a>](https://github.com/asddongmen)

    -   TiDB Lightning

        -   Fix the issue that in Logical Import Mode, deleting tables downstream during import might cause TiDB Lightning metadata not to be updated in time [<a href="https://github.com/pingcap/tidb/issues/44614">#44614</a>](https://github.com/pingcap/tidb/issues/44614) @[<a href="https://github.com/dsdashun">dsdashun</a>](https://github.com/dsdashun)
        -   Fix the issue that disk quota might be inaccurate due to competing conditions [<a href="https://github.com/pingcap/tidb/issues/44867">#44867</a>](https://github.com/pingcap/tidb/issues/44867) @[<a href="https://github.com/D3Hunter">D3Hunter</a>](https://github.com/D3Hunter)
        -   Fix the issue of `write to tikv with no leader returned` when importing a large amount of data [<a href="https://github.com/pingcap/tidb/issues/43055">#43055</a>](https://github.com/pingcap/tidb/issues/43055) @[<a href="https://github.com/lance6716">lance6716</a>](https://github.com/lance6716)
        -   Fix a possible OOM problem when there is an unclosed delimiter in the data file [<a href="https://github.com/pingcap/tidb/issues/40400">#40400</a>](https://github.com/pingcap/tidb/issues/40400) @[<a href="https://github.com/buchuitoudegou">buchuitoudegou</a>](https://github.com/buchuitoudegou)
        -   Fix the issue that OOM might occur when importing a wide table [<a href="https://github.com/pingcap/tidb/issues/43728">#43728</a>](https://github.com/pingcap/tidb/issues/43728) @[<a href="https://github.com/D3Hunter">D3Hunter</a>](https://github.com/D3Hunter)

    -   TiDB Binlog

        -   Fix the issue that the etcd client does not automatically synchronize the latest node information during initialization [<a href="https://github.com/pingcap/tidb-binlog/issues/1236">#1236</a>](https://github.com/pingcap/tidb-binlog/issues/1236) @[<a href="https://github.com/lichunzhu">lichunzhu</a>](https://github.com/lichunzhu)
        -   Fix the panic issue of Drainer due to an old TiKV client version by upgrading the TiKV client [<a href="https://github.com/pingcap/tidb-binlog/issues/1170">#1170</a>](https://github.com/pingcap/tidb-binlog/issues/1170) @[<a href="https://github.com/lichunzhu">lichunzhu</a>](https://github.com/lichunzhu)
        -   Fix the issue that unfiltered failed DDL statements cause task errors [<a href="https://github.com/pingcap/tidb-binlog/issues/1228">#1228</a>](https://github.com/pingcap/tidb-binlog/issues/1228) @[<a href="https://github.com/lichunzhu">lichunzhu</a>](https://github.com/lichunzhu)
