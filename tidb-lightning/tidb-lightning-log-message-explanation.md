---
title: TiDB Lightning Log Message Explanation
summary: Provide detailed explanation of log messages generated during the importing process using TiDB Lightning.
---

# TiDB Lightning Log Message Explanation {#tidb-lightning-log-message-explanation}

This document provides an explanation of log messages for **TiDB Lightning v5.4** in **Local Backend mode**, based on a successful test data import. It dives deep into the origin and meaning of each log message. You can refer to this document to better understand TiDB Lightning logs.

To fully comprehend this document, you need to be already familiar with TiDB Lightning and have prior knowledge of its high-level workflow described in [<a href="/tidb-lightning/tidb-lightning-overview.md">TiDB Lightning Overview</a>](/tidb-lightning/tidb-lightning-overview.md). If you come across unfamiliar terminology, refer to the [<a href="/tidb-lightning/tidb-lightning-glossary.md">glossary</a>](/tidb-lightning/tidb-lightning-glossary.md).

You can use this document to quickly navigate through TiDB Lightning source code and gain insight into its internal workings and understand the significance behind each log message.

Note that only important logs are included in this document. Less important logs have been omitted.

## Log message explanation {#log-message-explanation}

```
[INFO] [info.go:49] ["Welcome to TiDB-Lightning"] [release-version=v5.4.0] [git-hash=55f3b24c1c9f506bd652ef1d162283541e428872] [git-branch=HEAD] [go-version=go1.16.6] [utc-build-time="2022-04-21 02:07:55"] [race-enabled=false]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/version/build/info.go#L49">info.go:49</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/version/build/info.go#L49): Print TiDB Lightning version information.

```
[INFO] [lightning.go:233] [cfg] [cfg="{\"id\":1650510440481957437,\"lightning\":{\"table-concurrency\":6,\"index-concurrency\":2,\"region-concurrency\":8,\"io-concurrency\":5,\"check-requirements\":true,\"meta-schema-name\":\"lightning_metadata\", ...
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/lightning.go#L233">lightning.go:233</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/lightning.go#L233): Print TiDB Lightning config information.

```
[INFO] [lightning.go:312] ["load data source start"]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/lightning.go#L312">lightning.go:312</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/lightning.go#L312): Start to [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/loader.go#L205">scan data source dir or external storage</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/loader.go#L205) defined in TiDB Lightning [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/config/config.go#L447">mydumper `data-source-dir` config field</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/config/config.go#L447), and load all data source file meta info into [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/loader.go#L82">internal data structure</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/loader.go#L82) for future usage.

```
[INFO] [loader.go:289] ["[loader] file is filtered by file router"] [path=metadata]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/loader.go#L289">loader.go:289</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/loader.go#L289): Print data source files skipped based on [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/loader.go#L139">file router rules</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/loader.go#L139) defined in TiDB Lightning [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/config/config.go#L452">mydumper files config field</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/config/config.go#L452) or internal [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/router.go#L105">default file router rules</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/router.go#L105) if [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/config/config.go#L847">file rules are not defined</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/config/config.go#L847).

```
[INFO] [lightning.go:315] ["load data source completed"] [takeTime=273.964µs] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/lightning.go#L315">lightning.go:315</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/lightning.go#L315): Completed loading data source file info into [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/loader.go#L73">Mydumper File Loader</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/loader.go#L73) for later import.

```
[INFO] [checkpoints.go:977] ["open checkpoint file failed, going to create a new one"] [path=/tmp/tidb_lightning_checkpoint.pb] [error="open /tmp/tidb_lightning_checkpoint.pb: no such file or directory"]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/checkpoints/checkpoints.go#L977">checkpoints.go:977</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/checkpoints/checkpoints.go#L977): If TiDB Lightning uses files to store checkpoints, and cannot find any local checkpoint file, TiDB Lightning will create a new checkpoint.

```
[INFO] [restore.go:444] ["the whole procedure start"]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L444">restore.go:444</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L444): Start the import procedure.

```
[INFO] [restore.go:748] ["restore all schema start"]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L748">restore.go:748</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L748): Based on data source schema info, start to create databases and tables.

```
[INFO] [restore.go:767] ["restore all schema completed"] [takeTime=189.766729ms]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L767">restore.go:767</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L767): Completed to create databases and tables.

```
[INFO] [check_info.go:680] ["datafile to check"] [db=sysbench] [table=sbtest1] [path=sysbench.sbtest1.000000000.sql]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/check_info.go#L680">check_info.go:680</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/check_info.go#L680): As part of the precheck, TiDB Lightning uses the first data file of each table to check if the source data file and the target cluster table schema are matched.

```
[INFO] [version.go:360] ["detect server version"] [type=TiDB] [version=5.4.0]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/version/version.go#L360">version.go:360</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/version/version.go#L360): Detect and print the current TiDB server version. To import data in local backend mode, TiDB later than v4.0 is required. The same version check is also implemented for [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/version/version.go#L224">detecting data conflicts</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/version/version.go#L224).

```
[INFO] [check_info.go:995] ["sample file start"] [table=sbtest1]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/check_info.go#L995">check_info.go:995</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/check_info.go#L995): As part of the precheck, TiDB Lightning estimates the source data size to determine the following:

-   [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/check_info.go#L462">Whether the local disk has enough space if TiDB Lightning is in local backend mode</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/check_info.go#L462).
-   [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/check_info.go#L102">Whether the target cluster has enough space to store transformed KV pairs</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/check_info.go#L102).

TiDB Lightning estimates the size of transformed KV pairs by sampling the first source data file of each table and calculating the ratio between its size and KV pairs size. Then it uses the ratio, multiplied by the source data file size, to estimate the size of transformed KV pairs.

```
[INFO] [check_info.go:1080] ["Sample source data"] [table=sbtest1] [IndexRatio=1.3037832180660969] [IsSourceOrder=true]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/check_info.go#L1080">check_info.go:1080</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/check_info.go#L1080): The ratio between the table source file size and the KV pairs size has been calculated.

```
[INFO] [pd.go:415] ["pause scheduler successful at beginning"] [name="[balance-region-scheduler,balance-leader-scheduler,balance-hot-region-scheduler]"]
[INFO] [pd.go:423] ["pause configs successful at beginning"] [cfg="{\"enable-location-replacement\":\"false\",\"leader-schedule-limit\":4,\"max-merge-region-keys\":0,\"max-merge-region-size\":0,\"max-pending-peer-count\":2147483647,\"max-snapshot-count\":40,\"region-schedule-limit\":40}"]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#L415">pd.go:415</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#L415), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#L423">pd.go:423</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#L423): In local backend mode, some [<a href="https://docs.pingcap.com/tidb/stable/tidb-scheduling">PD schedulers</a>](https://docs.pingcap.com/tidb/stable/tidb-scheduling) are disabled and some [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#L417">configuration items are changed</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#L417) to split and scatter TiKV regions and ingest SSTs.

```
[INFO] [restore.go:1683] ["switch to import mode"]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1683">restore.go:1683</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1683): In local backend mode, TiDB Lightning turns each TiKV node into import mode to speed up the import process, but sacrifices its storage space. If it uses tidb backend mode, it does not need to switch TiKV to [<a href="https://docs.pingcap.com/tidb/stable/tidb-lightning-glossary#import-mode">import mode</a>](https://docs.pingcap.com/tidb/stable/tidb-lightning-glossary#import-mode).

```
[INFO] [restore.go:1462] ["restore table start"] [table=`sysbench`.`sbtest1`]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1462">restore.go:1462</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1462): Start to restore table `sysbench`.`sbtest1`. TiDB Lightning concurrently restores multiple tables based on the [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1459">`index-concurrency`</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1459) configuration. For each table, it concurrently restores data files in the table based on the [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/region.go#L157">`region-concurrency`</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/region.go#L157) configuration.

```
[INFO] [table_restore.go:91] ["load engines and files start"] [table=`sysbench`.`sbtest1`]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L91">table_restore.go:91</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L91): Start to logically [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/region.go#L145">split each table source data files</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/region.go#L145) into multiple [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/region.go#L283">chunks/table regions</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/region.go#L283). Each table source data file will be [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/region.go#L246">assigned to an engine</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/region.go#L246) so source data files can be processed in parallel across different engines.

```
[INFO] [region.go:241] [makeTableRegions] [filesCount=8] [MaxRegionSize=268435456] [RegionsCount=8] [BatchSize=107374182400] [cost=53.207µs]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/region.go#L241">region.go:241</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/mydump/region.go#L241): Prints the amount of table data files (`filesCount`) that have been processed, and the largest chunk size (`MaxRegionSize`) of the CSV file, the number of generated table regions/chunks (`RegionsCount`), and the `batchSize` that is used to assign different engines to process data files.

```
[INFO] [table_restore.go:129] ["load engines and files completed"] [table=`sysbench`.`sbtest1`] [enginesCnt=2] [ime=75.563µs] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L129">table_restore.go:129</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L129): Completed to logically split table data files.

```
[INFO] [backend.go:346] ["open engine"] [engineTag=`sysbench`.`sbtest1`:-1] [engineUUID=3942bab1-bd60-52e2-bf53-e17aebf962c6]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L346">backend.go:346</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L346): The engine with id `-1` is the index engine. It will open the index engine at the beginning of the [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L199">restore engines process</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L199) for storing transformed index KV pairs.

```
[INFO] [table_restore.go:270] ["import whole table start"] [table=`sysbench`.`sbtest1`]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L270">table_restore.go:270</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L270): Start to concurrently [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L318">restore different data engines of a given table</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L318).

```
[INFO] [table_restore.go:317] ["restore engine start"] [table=`sysbench`.`sbtest1`] [engineNumber=0]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L317">table_restore.go:317</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L317): Start to restore engine `0`. A non `-1` engine id indicates a data engine. Note that "restore engine" and "import engine" (which appears later in the logs) refer to different processes. "restore engine" indicates the process of sending KV pairs to the allocated engine and sorting them, while "import engine" represents the process of ingesting sorted KV pairs in the engine file into the TiKV nodes.

```
[INFO] [table_restore.go:422] ["encode kv data and write start"] [table=`sysbench`.`sbtest1`] [engineNumber=0]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L422">table_restore.go:422</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L422): Start to [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L386">restore table data by chunks</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L386).

```
[INFO] [backend.go:346] ["open engine"] [engineTag=`sysbench`.`sbtest1`:0] [engineUUID=d173bb2e-b753-5da9-b72e-13a49a46f5d7]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L346">backend.go:346</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L346): Open data engine with id = 0 for storing transformed data KV pairs.

```
[INFO] [restore.go:2482] ["restore file start"] [table=`sysbench`.`sbtest1`] [engineNumber=0] [fileIndex=0] [path=sysbench.sbtest1.000000000.sql:0]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L2482">restore.go:2482</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L2482): This log may appear multiple times based on the data size of the imported table. Each log in this form indicates the start of restoring a chunk/table region. It concurrently [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L386">restores chunks</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L386) based on internal [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L532">region workers</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L532) defined by [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L402">region concurrency</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L402). For each chunk, the restoring process is as follows:

1.  [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L2389">Encodes SQL into KV pairs</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L2389).
2.  [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L2179">Writes KV pairs into data engine and index engine</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L2179).

```
[INFO] [engine.go:777] ["write data to local DB"] [size=134256327] [kvs=621576] [files=1] [sstFileSize=108984502] [file=/home/centos/tidb-lightning-temp-data/sorted-kv-dir/d173bb2e-b753-5da9-b72e-13a49a46f5d7.sst/11e65bc1-04d0-4a39-9666-cae49cd013a9.sst] [firstKey=74800000000000003F5F728000000000144577] [lastKey=74800000000000003F5F7280000000001DC17E]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/engine.go#L777">engine.go:777</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/engine.go#L777): Start to ingest generated SST files into the embedded engine. It [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/local.go#L624">concurrently ingests SST files</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/local.go#L624).

```
[INFO] [restore.go:2492] ["restore file completed"] [table=`sysbench`.`sbtest1`] [engineNumber=0] [fileIndex=1] [path=sysbench.sbtest1.000000001.sql:0] [readDur=3.123667511s] [encodeDur=5.627497136s] [deliverDur=6.653498837s] [checksum="{cksum=6610977918434119862,size=336040251,kvs=2646056}"] [takeTime=15.474211783s] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L2492">restore.go:2492</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L2492): A chunk (a data source file defined by `fileIndex=1`) of a given table has been encoded and stored in the engine.

```
[INFO] [table_restore.go:584] ["encode kv data and write completed"] [table=`sysbench`.`sbtest1`] [engineNumber=0] [read=16] [written=2539933993] [takeTime=23.598662501s] []
[source code]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L584">table_restore.go:584</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L584): All chunks/table regions that belong to engine `engineNumber=0` has been encoded and stored in engine `engineNumber=0`.

```
[INFO] [backend.go:438] ["engine close start"] [engineTag=`sysbench`.`sbtest1`:0] [engineUUID=d173bb2e-b753-5da9-b72e-13a49a46f5d7]
[INFO] [backend.go:440] ["engine close completed"] [engineTag=`sysbench`.`sbtest1`:0] [engineUUID=d173bb2e-b753-5da9-b72e-13a49a46f5d7] [takeTime=2.879906ms] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L438">backend.go:438</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L438): As [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L628">the final stage of engine restore</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L628), the data engine is closed and prepared for importing to TiKV nodes.

```
[INFO] [table_restore.go:319] ["restore engine completed"] [table=`sysbench`.`sbtest1`] [engineNumber=0] [takeTime=27.031916498s] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L319">table_restore.go:319</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L319): Completed to encode and write KV pairs to data engine 0.

```
[INFO] [table_restore.go:927] ["import and cleanup engine start"] [engineTag=`sysbench`.`sbtest1`:0] [engineUUID=d173bb2e-b753-5da9-b72e-13a49a46f5d7]
[INFO] [backend.go:452] ["import start"] [engineTag=`sysbench`.`sbtest1`:0] [engineUUID=d173bb2e-b753-5da9-b72e-13a49a46f5d7] [retryCnt=0]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L927">table_restore.go:927</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L927), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L452">backend.go:452</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L452): Start to [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/local.go#L1311">import</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/local.go#L1311) KV pairs stored in the engine into the target TiKV node.

```
[INFO] [local.go:1023] ["split engine key ranges"] [engine=d173bb2e-b753-5da9-b72e-13a49a46f5d7] [totalSize=2159933993] [totalCount=10000000] [firstKey=74800000000000003F5F728000000000000001] [lastKey=74800000000000003F5F728000000000989680] [ranges=22]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/local.go#L1023">local.go:1023</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/local.go#L1023): Before the [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/local.go#L1331">import engine</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/local.go#L1331) procedure, TiDB Lightning logically splits engine data into smaller ranges based on the [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L927">`RegionSplitSize`</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L927) configuration.

```
[INFO] [local.go:1336] ["start import engine"] [uuid=d173bb2e-b753-5da9-b72e-13a49a46f5d7] [ranges=22] [count=10000000] [size=2159933993]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/local.go#L1336">local.go:1336</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/local.go#L1336): Start to import KV pairs into the engine by splitting ranges.

```
[INFO] [localhelper.go:89] ["split and scatter region"] [minKey=7480000000000000FF3F5F728000000000FF0000010000000000FA] [maxKey=7480000000000000FF3F5F728000000000FF9896810000000000FA] [retry=0]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/localhelper.go#L89">localhelper.go:89</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/localhelper.go#L89): Start to [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/localhelper.go#L65">split and scatter</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/localhelper.go#L65) TiKV regions based on engine ranges `minKey` and `maxKey`.

```
[INFO] [localhelper.go:108] ["paginate scan regions"] [count=1] [start=7480000000000000FF3F5F728000000000FF0000010000000000FA] [end=7480000000000000FF3F5F728000000000FF9896810000000000FA]
[INFO] [localhelper.go:116] ["paginate scan region finished"] [minKey=7480000000000000FF3F5F728000000000FF0000010000000000FA] [maxKey=7480000000000000FF3F5F728000000000FF9896810000000000FA] [regions=1]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/localhelper.go#L108">localhelper.go:108</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/localhelper.go#L108), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/localhelper.go#L108">localhelper.go:116</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/localhelper.go#L108): Paginate the [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/restore/split.go#L413">scans of a batch of regions info</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/restore/split.go#L413) on PD.

```
[INFO] [split_client.go:460] ["checking whether need to scatter"] [store=1] [max-replica=3]
[INFO] [split_client.go:113] ["skipping scatter because the replica number isn't less than store count."]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/restore/split_client.go#L460">split_client.go:460</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/restore/split_client.go#L460), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/restore/split_client.go#L113">split_client.go:113</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/restore/split_client.go#L113): Skips the scatter regions phase because `max-replica` >= the number of TiKV stores. Scattering regions is the process that PD schedulers distribute regions and their replicas to different TiKV stores.

```
[INFO] [localhelper.go:240] ["batch split region"] [region_id=2] [keys=23] [firstKey="dIAAAAAAAAA/X3KAAAAAAAAAAQ=="] [end="dIAAAAAAAAA/X3KAAAAAAJiWgQ=="]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/localhelper.go#L240">localhelper.go:240</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/localhelper.go#L240): Completed to batch split TiKV regions.

```
[INFO] [localhelper.go:319] ["waiting for scattering regions done"] [skipped_keys=0] [regions=23] [take=6.505195ms]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/localhelper.go#L319">localhelper.go:319</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/localhelper.go#L319): Completed to scatter TiKV regions.

```
[INFO] [local.go:1371] ["import engine success"] [uuid=d173bb2e-b753-5da9-b72e-13a49a46f5d7] [size=2159933993] [kvs=10000000] [importedSize=2159933993] [importedCount=10000000]
[INFO] [backend.go:455] ["import completed"] [engineTag=`sysbench`.`sbtest1`:0] [engineUUID=d173bb2e-b753-5da9-b72e-13a49a46f5d7] [retryCnt=0] [takeTime=20.179184481s] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/local.go#L1371">local.go:1371</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/local.go#L1371), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L455">backend.go:455</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L455): Completed to import KV pairs in the specific engine into TiKV stores.

```
[INFO] [backend.go:467] ["cleanup start"] [engineTag=`sysbench`.`sbtest1`:0] [engineUUID=d173bb2e-b753-5da9-b72e-13a49a46f5d7]
[INFO] [backend.go:469] ["cleanup completed"] [engineTag=`sysbench`.`sbtest1`:0] [engineUUID=d173bb2e-b753-5da9-b72e-13a49a46f5d7] [takeTime=209.800004ms] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L467">backend.go:467</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L467), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L469">backend.go:469</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/backend.go#L469): Clean up intermediate data during import phase. TiDB Lightning will [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/engine.go#L158">clean up</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/backend/local/engine.go#L158) the engine related meta info and db files.

```
[INFO] [table_restore.go:946] ["import and cleanup engine completed"] [engineTag=`sysbench`.`sbtest1`:0] [engineUUID=d173bb2e-b753-5da9-b72e-13a49a46f5d7] [takeTime=20.389269402s] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L946">table_restore.go:946</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L946): Completed to import and cleanup.

```
[INFO] [table_restore.go:345] ["import whole table completed"] [table=`sysbench`.`sbtest1`] [takeTime=47.421324969s] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L345">table_restore.go:345</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L345): Completed to import table data. TiDB Lightning converted all table data into KV pairs and ingested them into the TiKV clusters.

```
[INFO] [tidb.go:401] ["alter table auto_increment start"] [table=`sysbench`.`sbtest1`] [auto_increment=10000002]
[INFO] [tidb.go:403] ["alter table auto_increment completed"] [table=`sysbench`.`sbtest1`] [auto_increment=10000002] [takeTime=82.225557ms] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/tidb.go#L401">tidb.go:401</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/tidb.go#L401), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/tidb.go#L403">tidb.go:403</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/tidb.go#L403): During the [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L680">post process</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L680) phase, it will [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L703">adjust the table's auto increment ID</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L703) to avoid introducing conflicts from newly added data.

```
[INFO] [restore.go:1466] ["restore table completed"] [table=`sysbench`.`sbtest1`] [takeTime=53.280464651s] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1466">restore.go:1466</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1466): Completed to restore table.

```
[INFO] [restore.go:1396] ["add back PD leader&region schedulers"]
[INFO] [pd.go:462] ["resume scheduler"] [schedulers="[balance-region-scheduler,balance-leader-scheduler,balance-hot-region-scheduler]"]
[INFO] [pd.go:448] ["exit pause scheduler and configs successful"]
[INFO] [pd.go:482] ["resume scheduler successful"] [scheduler=balance-region-scheduler]
[INFO] [pd.go:573] ["restoring config"] [config="{\"enable-location-replacement\":\"true\",\"leader-schedule-limit\":4,\"max-merge-region-keys\":200000,\"max-merge-region-size\":20,\"max-pending-peer-count\":64,\"max-snapshot-count\":64,\"region-schedule-limit\":2048}"]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1396">restore.go:1396</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1396), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#L462">pd.go:462</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#L462), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#L448">pd.go:448</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#L448), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#482">pd.go:482</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#482), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#573">pd.go:573</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/pdutil/pd.go#573): Resume the paused PD schedulers before import, and reset PD configuration.

```
[INFO] [restore.go:1244] ["cancel periodic actions"] [do=true]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1244">restore.go:1244</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1244): Start to cancel [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1087">periodic actions</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1087), which periodically prints the importing progress, and check whether TiKV is still in import mode.

```
[INFO] [restore.go:1688] ["switch to normal mode"]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1688">restore.go:1688</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1688): Switch TiKV from import mode to normal mode.

```
[INFO] [table_restore.go:736] ["local checksum"] [table=`sysbench`.`sbtest1`] [checksum="{cksum=9970490404295648092,size=2539933993,kvs=20000000}"]
[INFO] [checksum.go:172] ["remote checksum start"] [table=sbtest1]
[INFO] [checksum.go:175] ["remote checksum completed"] [table=sbtest1] [takeTime=2.817086758s] []
[INFO] [table_restore.go:971] ["checksum pass"] [table=`sysbench`.`sbtest1`] [local="{cksum=9970490404295648092,size=2539933993,kvs=20000000}"]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L736">table_restore.go:736</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L736), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/checksum.go#L172">checksum.go:172</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/checksum.go#L172), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/checksum.go#L175">checksum.go:175</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/checksum.go#L175), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L971">table_restore.go:971</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L971): [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L736">Compare the local and remote checksum</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L736) to validate the imported data.

```
[INFO] [table_restore.go:976] ["analyze start"] [table=`sysbench`.`sbtest1`]
[INFO] [table_restore.go:978] ["analyze completed"] [table=`sysbench`.`sbtest1`] [takeTime=26.410378251s] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L976">table_restore.go:976</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L976), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L978">table_restore.go:978</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/table_restore.go#L978): TiDB analyzes table to update the statistics that TiDB builds on tables and indexes. It is recommended to run `ANALYZE` after performing a large batch update or import of records, or when you notice that query execution plans are sub-optimal.

```
[INFO] [restore.go:1440] ["cleanup task metas"]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1440">restore.go:1440</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1440): Clean up import task metas, table metas and schema db if needed.

```
[INFO] [restore.go:1842] ["clean checkpoints start"] [keepAfterSuccess=remove] [taskID=1650516927467320997]
[INFO] [restore.go:1850] ["clean checkpoints completed"] [keepAfterSuccess=remove] [taskID=1650516927467320997] [takeTime=18.543µs] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1842">restore.go:1842</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1842), [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1850">restore.go:1850</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1850): Clean up checkpoints.

```
[INFO] [restore.go:473] ["the whole procedure completed"] [takeTime=1m22.804337152s] []
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L473">restore.go:473</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L473): Completed the whole import procedure.

```
[INFO] [restore.go:1143] ["everything imported, stopping periodic actions"]
```

[<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1143">restore.go:1143</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1143): Stop all [<a href="https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1087">periodic actions</a>](https://github.com/pingcap/tidb/blob/v5.4.0/br/pkg/lightning/restore/restore.go#L1087) after the import is completed.
