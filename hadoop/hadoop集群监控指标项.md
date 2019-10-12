# hadoop集群监控指标项



## jmx

说明文档：[https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-common/Metrics.html](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-common/Metrics.html)

访问地址：http://ip:port/jmx

## 访问方式

get

qry

callback

## jvm指标项

| 名称                         | Description               |
| -------------------------- | ------------------------- |
| MemNonHeapUsedM            | JVM 当前已经使用的非堆内存 的大小（M）    |
| MemNonHeapCommittedM       | 当前非堆内存已提交大小（M）            |
| MemNonHeapMaxM             | JVM配置的最大非堆内存大小（M）         |
| MemHeapUsedM               | JVM当前已使用的堆内存大小（M）         |
| MemHeapCommittedM          | 堆内存已提交大小（M）               |
| MemHeapMaxM                | JVM配置的最大堆内存（M）            |
| MemMaxM                    | JVM配置的最大内存大小（M）           |
| ThreadsNew                 | 处于 NEW 状态下的线程数量           |
| ThreadsRunnable            | 处于 RUNNABLE 状态下的线程数量      |
| ThreadsBlocked             | 处于 BLOCKED 状态下的线程数量       |
| ThreadsWaiting             | 处于 WAITING 状态下的线程数量       |
| ThreadsTimedWaiting        | 处于 TIMED_WAITING 状态下的线程数量 |
| ThreadsTerminated          | 处于 TERMINATED 状态下的线程数量    |
| GcInfo                     | 按照GC类型分组的GC次数和耗时          |
| GcCount                    | GC次数                      |
| GcTimeMillis               | GC耗时（ms）                  |
| LogFatal                   | FATAL日志的次数                |
| LogError                   | ERROR日志的次数                |
| LogWarn                    | WARN日志的次数                 |
| LogInfo                    | INFO日志的次数                 |
| GcNumWarnThresholdExceeded | 超过GCWarn阈值的次数             |
| GcNumInfoThresholdExceeded | 超过GCInfo阈值的次数             |
| GcTotalExtraSleepTime      | GC额外的休眠时间                 |



## rpc指标项

| Name                                     | Description                              |
| ---------------------------------------- | ---------------------------------------- |
| ReceivedBytes                            | 接收总字节数                                   |
| SentBytes                                | 发送总字节数                                   |
| RpcQueueTimeNumOps                       | Rpc被调用次数                                 |
| RpcQueueTimeAvgTime                      | Rpc队列平均耗时                                |
| RpcProcessingTimeNumOps                  | Rpc被调用次数 (与RpcQueueTimeNumOps相同)         |
| RpcProcessingAvgTime                     | Rpc平均处理耗时                                |
| RpcAuthenticationFailures                | Rpc验证失败次数                                |
| RpcAuthenticationSuccesses               | Rpc验证成功次数                                |
| RpcAuthorizationFailures                 | Rpc授权失败次数                                |
| RpcAuthorizationSuccesses                | Rpc授权成功次数                                |
| NumOpenConnections                       | 当前打开的连接数目                                |
| CallQueueLength                          | RPC Call队列的长度                            |
| rpcQueueTime*num*sNumOps                 | Shows total number of RPC calls (*num* seconds granularity) if rpc.metrics.quantile.enable is set to true. *num* is specified by rpc.metrics.percentiles.intervals. |
| rpcQueueTime*num*s50thPercentileLatency  | Shows the 50th percentile of RPC queue time in milliseconds (*num* seconds granularity) if rpc.metrics.quantile.enable is set to true. *num* is specified by rpc.metrics.percentiles.intervals. |
| rpcQueueTime*num*s75thPercentileLatency  | Shows the 75th percentile of RPC queue time in milliseconds (*num* seconds granularity) if rpc.metrics.quantile.enable is set to true. *num* is specified by rpc.metrics.percentiles.intervals. |
| rpcQueueTime*num*s90thPercentileLatency  | Shows the 90th percentile of RPC queue time in milliseconds (*num* seconds granularity) if rpc.metrics.quantile.enable is set to true. *num* is specified by rpc.metrics.percentiles.intervals. |
| rpcQueueTime*num*s95thPercentileLatency  | Shows the 95th percentile of RPC queue time in milliseconds (*num* seconds granularity) if rpc.metrics.quantile.enable is set to true. *num* is specified by rpc.metrics.percentiles.intervals. |
| rpcQueueTime*num*s99thPercentileLatency  | Shows the 99th percentile of RPC queue time in milliseconds (*num* seconds granularity) if rpc.metrics.quantile.enable is set to true. *num* is specified by rpc.metrics.percentiles.intervals. |
| rpcProcessingTime*num*sNumOps            | Shows total number of RPC calls (*num* seconds granularity) if rpc.metrics.quantile.enable is set to true. *num* is specified by rpc.metrics.percentiles.intervals. |
| rpcProcessingTime*num*s50thPercentileLatency | Shows the 50th percentile of RPC processing time in milliseconds (*num* seconds granularity) if rpc.metrics.quantile.enable is set to true. *num* is specified by rpc.metrics.percentiles.intervals. |
| rpcProcessingTime*num*s75thPercentileLatency | Shows the 75th percentile of RPC processing time in milliseconds (*num* seconds granularity) if rpc.metrics.quantile.enable is set to true. *num* is specified by rpc.metrics.percentiles.intervals. |
| rpcProcessingTime*num*s90thPercentileLatency | Shows the 90th percentile of RPC processing time in milliseconds (*num* seconds granularity) if rpc.metrics.quantile.enable is set to true. *num* is specified by rpc.metrics.percentiles.intervals. |
| rpcProcessingTime*num*s95thPercentileLatency | Shows the 95th percentile of RPC processing time in milliseconds (*num* seconds granularity) if rpc.metrics.quantile.enable is set to true. *num* is specified by rpc.metrics.percentiles.intervals. |
| rpcProcessingTime*num*s99thPercentileLatency | Shows the 99th percentile of RPC processing time in milliseconds (*num* seconds granularity) if rpc.metrics.quantile.enable is set to true. *num* is specified by rpc.metrics.percentiles.intervals. |

## RetryCache/NameNodeRetryCache

| Name         | Description                        |
| ------------ | ---------------------------------- |
| CacheHit     | Total number of RetryCache hit     |
| CacheCleared | Total number of RetryCache cleared |
| CacheUpdated | Total number of RetryCache updated |

## rpcdetailed

| Name                | Description |
| ------------------- | ----------- |
| *methodname*NumOps  | 方法调用次数      |
| *methodname*AvgTime | 平均调用时间      |



## dfs 

| Name                      | Description                              |
| ------------------------- | ---------------------------------------- |
| CreateFileOps             | 创建文件执行次数                                 |
| FilesCreated              | 所有文件和目录的创建次数                             |
| FilesAppended             | 文件追加次数                                   |
| GetBlockLocations         | 获取块位置执行次数                                |
| FilesRenamed              | 重命名操作次数(不是重命名文件或重命名目录的次数)                |
| GetListingOps             | 目录列表操作次数                                 |
| DeleteFileOps             | 删除文件操作次数                                 |
| FilesDeleted              | 所有文件和目录被删除和重命名次数                         |
| FileInfoOps               | getFileInfo 和getLinkFileInfo操作执行次数       |
| AddBlockOps               | addBlock（增加块）成功次数                        |
| GetAdditionalDatanodeOps  | getAdditionalDatanode次数                  |
| CreateSymlinkOps          | createSymlink次数                          |
| GetLinkTargetOps          | getLinkTarget次数                          |
| FilesInGetListingOps      | Total number of files and directories listed by directory listing operations |
| AllowSnapshotOps          | allowSnapshot 次数                         |
| DisallowSnapshotOps       | isallowSnapshot次数                        |
| CreateSnapshotOps         | createSnapshot 执行次数                      |
| DeleteSnapshotOps         | deleteSnapshot执行次数                       |
| RenameSnapshotOps         | renameSnapshot执行次数                       |
| ListSnapshottableDirOps   | snapshottableDirectoryStatus执行次数         |
| SnapshotDiffReportOps     | getSnapshotDiffReport执行次数                |
| TransactionsNumOps        | Total number of Journal transactions     |
| TransactionsAvgTime       | Average time of Journal transactions in milliseconds |
| SyncsNumOps               | Total number of Journal syncs            |
| SyncsAvgTime              | Average time of Journal syncs in milliseconds |
| TransactionsBatchedInSync | Total number of Journal transactions batched in sync |
| BlockReportNumOps         | 处理来自datanode块报告次数                        |
| BlockReportAvgTime        | Average time of processing block reports in milliseconds |
| CacheReportNumOps         | Total number of processing cache reports from DataNode |
| CacheReportAvgTime        | Average time of processing cache reports in milliseconds |
| SafeModeTime              | The interval between FSNameSystem starts and the last time safemode leaves in milliseconds.  (sometimes not equal to the time in SafeMode, see [HDFS-5156](https://issues.apache.org/jira/browse/HDFS-5156)) |
| FsImageLoadTime           | Time loading FS Image at startup in milliseconds |
| FsImageLoadTime           | Time loading FS Image at startup in milliseconds |
| GetEditNumOps             | Total number of edits downloads from SecondaryNameNode |
| GetEditAvgTime            | Average edits download time in milliseconds |
| GetImageNumOps            | Total number of fsimage downloads from SecondaryNameNode |
| GetImageAvgTime           | Average fsimage download time in milliseconds |
| PutImageNumOps            | Total number of fsimage uploads to SecondaryNameNode |
| PutImageAvgTime           | Average fsimage upload time in milliseconds |

## FSNamesystem

| Name                            | Description                              |
| ------------------------------- | ---------------------------------------- |
| MissingBlocks                   | 当前丢失的块数量                                 |
| ExpiredHeartbeats               | 失去心跳的数量                                  |
| TransactionsSinceLastCheckpoint | 自从上次检查点以来的总事务数                           |
| TransactionsSinceLastLogRoll    | 自从上次日志滚动以来的总事务数                          |
| LastWrittenTransactionId        | Last transaction ID written to the edit log |
| LastCheckpointTime              | Time in milliseconds since epoch of last checkpoint |
| CapacityTotal                   | 字节表示的当前数据节点的原始容量                         |
| CapacityTotalGB                 | GB表示的当前数据节点的原始容量                         |
| CapacityUsed                    | 以字节表示的当前在所有数据节点中使用的容量                    |
| CapacityUsedGB                  | 以GB表示的当前在所有数据节点中使用的容量                    |
| CapacityRemaining               | 用字节表示的当前剩余容量                             |
| CapacityRemainingGB             | 用GB表示的当前剩余容量                             |
| CapacityUsedNonDFS              | 当前空闲Current space used by DataNodes for non DFS purposes in bytes |
| TotalLoad                       | 当前连接数                                    |
| SnapshottableDirectories        | 当前可快照目录的数量                               |
| Snapshots                       | Current number of snapshots              |
| BlocksTotal                     | Current number of allocated blocks in the system |
| FilesTotal                      | Current number of files and directories  |
| PendingReplicationBlocks        | Current number of blocks pending to be replicated |
| UnderReplicatedBlocks           | Current number of blocks under replicated |
| CorruptBlocks                   | Current number of blocks with corrupt replicas. |
| ScheduledReplicationBlocks      | Current number of blocks scheduled for replications |
| PendingDeletionBlocks           | Current number of blocks pending deletion |
| ExcessBlocks                    | Current number of excess blocks          |
| PostponedMisreplicatedBlocks    | (HA-only) Current number of blocks postponed to replicate |
| PendingDataNodeMessageCourt     | (HA-only) Current number of pending block-related messages for later processing in the standby NameNode |
| MillisSinceLastLoadedEdits      | (HA-only) Time in milliseconds since the last time standby NameNode load edit log. In active NameNode, set to 0 |
| BlockCapacity                   | Current number of block capacity         |
| StaleDataNodes                  | Current number of DataNodes marked stale due to delayed heartbeat |
| TotalFiles                      | Current number of files and directories (same as FilesTotal) |

## JournalNode

| Name                                  | Description                              |
| ------------------------------------- | ---------------------------------------- |
| Syncs60sNumOps                        | Number of sync operations (1 minute granularity) |
| Syncs60s50thPercentileLatencyMicros   | The 50th percentile of sync latency in microseconds (1 minute granularity) |
| Syncs60s75thPercentileLatencyMicros   | The 75th percentile of sync latency in microseconds (1 minute granularity) |
| Syncs60s90thPercentileLatencyMicros   | The 90th percentile of sync latency in microseconds (1 minute granularity) |
| Syncs60s95thPercentileLatencyMicros   | The 95th percentile of sync latency in microseconds (1 minute granularity) |
| Syncs60s99thPercentileLatencyMicros   | The 99th percentile of sync latency in microseconds (1 minute granularity) |
| Syncs300sNumOps                       | Number of sync operations (5 minutes granularity) |
| Syncs300s50thPercentileLatencyMicros  | The 50th percentile of sync latency in microseconds (5 minutes granularity) |
| Syncs300s75thPercentileLatencyMicros  | The 75th percentile of sync latency in microseconds (5 minutes granularity) |
| Syncs300s90thPercentileLatencyMicros  | The 90th percentile of sync latency in microseconds (5 minutes granularity) |
| Syncs300s95thPercentileLatencyMicros  | The 95th percentile of sync latency in microseconds (5 minutes granularity) |
| Syncs300s99thPercentileLatencyMicros  | The 99th percentile of sync latency in microseconds (5 minutes granularity) |
| Syncs3600sNumOps                      | Number of sync operations (1 hour granularity) |
| Syncs3600s50thPercentileLatencyMicros | The 50th percentile of sync latency in microseconds (1 hour granularity) |
| Syncs3600s75thPercentileLatencyMicros | The 75th percentile of sync latency in microseconds (1 hour granularity) |
| Syncs3600s90thPercentileLatencyMicros | The 90th percentile of sync latency in microseconds (1 hour granularity) |
| Syncs3600s95thPercentileLatencyMicros | The 95th percentile of sync latency in microseconds (1 hour granularity) |
| Syncs3600s99thPercentileLatencyMicros | The 99th percentile of sync latency in microseconds (1 hour granularity) |
| BatchesWritten                        | Total number of batches written since startup |
| TxnsWritten                           | Total number of transactions written since startup |
| BytesWritten                          | Total number of bytes written since startup |
| BatchesWrittenWhileLagging            | Total number of batches written where this node was lagging |
| LastWriterEpoch                       | Current writer’s epoch number            |
| CurrentLagTxns                        | The number of transactions that this JournalNode is lagging |
| LastWrittenTxId                       | The highest transaction id stored on this JournalNode |
| LastPromisedEpoch                     | The last epoch number which this node has promised not to accept any lower epoch, or 0 if no promises have been made |

## datanode

| Name                                     | Description                              |
| ---------------------------------------- | ---------------------------------------- |
| BytesWritten                             | 写入datanode字节数                            |
| BytesRead                                | datanode读取字节数                            |
| BlocksWritten                            | 块写入datanode次数                            |
| BlocksRead                               | Datanode读取块次数                            |
| BlocksReplicated                         | 块复制次数                                    |
| BlocksRemoved                            | 块移除次数                                    |
| BlocksVerified                           | Total number of blocks verified          |
| BlockVerificationFailures                | Total number of verifications failures   |
| BlocksCached                             | Total number of blocks cached            |
| BlocksUncached                           | Total number of blocks uncached          |
| ReadsFromLocalClient                     | Total number of read operations from local client |
| ReadsFromRemoteClient                    | Total number of read operations from remote client |
| WritesFromLocalClient                    | Total number of write operations from local client |
| WritesFromRemoteClient                   | Total number of write operations from remote client |
| BlocksGetLocalPathInfo                   | Total number of operations to get local path names of blocks |
| FsyncCount                               | Total number of fsync                    |
| VolumeFailures                           | Total number of volume failures occurred |
| ReadBlockOpNumOps                        | Total number of read operations          |
| ReadBlockOpAvgTime                       | Average time of read operations in milliseconds |
| WriteBlockOpNumOps                       | Total number of write operations         |
| WriteBlockOpAvgTime                      | Average time of write operations in milliseconds |
| BlockChecksumOpNumOps                    | Total number of blockChecksum operations |
| BlockChecksumOpAvgTime                   | Average time of blockChecksum operations in milliseconds |
| CopyBlockOpNumOps                        | Total number of block copy operations    |
| CopyBlockOpAvgTime                       | Average time of block copy operations in milliseconds |
| ReplaceBlockOpNumOps                     | Total number of block replace operations |
| ReplaceBlockOpAvgTime                    | Average time of block replace operations in milliseconds |
| HeartbeatsNumOps                         | Total number of heartbeats               |
| HeartbeatsAvgTime                        | Average heartbeat time in milliseconds   |
| BlockReportsNumOps                       | Total number of block report operations  |
| BlockReportsAvgTime                      | Average time of block report operations in milliseconds |
| IncrementalBlockReportsNumOps            | Total number of incremental block report operations |
| IncrementalBlockReportsAvgTime           | Average time of incremental block report operations in milliseconds |
| CacheReportsNumOps                       | Total number of cache report operations  |
| CacheReportsAvgTime                      | Average time of cache report operations in milliseconds |
| PacketAckRoundTripTimeNanosNumOps        | Total number of ack round trip           |
| PacketAckRoundTripTimeNanosAvgTime       | Average time from ack send to receive minus the downstream ack time in nanoseconds |
| FlushNanosNumOps                         | Total number of flushes                  |
| FlushNanosAvgTime                        | Average flush time in nanoseconds        |
| FsyncNanosNumOps                         | Total number of fsync                    |
| FsyncNanosAvgTime                        | Average fsync time in nanoseconds        |
| SendDataPacketBlockedOnNetworkNanosNumOps | Total number of sending packets          |
| SendDataPacketBlockedOnNetworkNanosAvgTime | Average waiting time of sending packets in nanoseconds |
| SendDataPacketTransferNanosNumOps        | Total number of sending packets          |
| SendDataPacketTransferNanosAvgTime       | Average transfer time of sending packets in nanoseconds |

## yarn

| Name                 | Description                              |
| -------------------- | ---------------------------------------- |
| NumActiveNMs         | 当前活动状态nodemanager数                       |
| NumDecommissionedNMs | 当前丢失NodeManager数目                        |
| NumLostNMs           | Current number of lost NodeManagers for not sending heartbeats |
| NumUnhealthyNMs      | Current number of unhealthy NodeManagers |
| NumRebootedNMs       | Current number of rebooted NodeManagers  |



## QueueMetrics



| Name                         | Description                              |
| ---------------------------- | ---------------------------------------- |
| running_0                    | Current number of running applications whose elapsed time are less than 60 minutes |
| running_60                   | Current number of running applications whose elapsed time are between 60 and 300 minutes |
| running_300                  | Current number of running applications whose elapsed time are between 300 and 1440 minutes |
| running_1440                 | Current number of running applications elapsed time are more than 1440 minutes |
| AppsSubmitted                | Total number of submitted applications   |
| AppsRunning                  | Current number of running applications   |
| AppsPending                  | Current number of applications that have not yet been assigned by any containers |
| AppsCompleted                | Total number of completed applications   |
| AppsKilled                   | Total number of killed applications      |
| AppsFailed                   | Total number of failed applications      |
| AllocatedMB                  | Current allocated memory in MB           |
| AllocatedVCores              | Current allocated CPU in virtual cores   |
| AllocatedContainers          | Current number of allocated containers   |
| AggregateContainersAllocated | Total number of allocated containers     |
| AggregateContainersReleased  | Total number of released containers      |
| AvailableMB                  | Current available memory in MB           |
| AvailableVCores              | Current available CPU in virtual cores   |
| PendingMB                    | Current pending memory resource requests in MB that are not yet fulfilled by the scheduler |
| PendingVCores                | Current pending CPU allocation requests in virtual cores that are not yet fulfilled by the scheduler |
| PendingContainers            | Current pending resource requests that are not yet fulfilled by the scheduler |
| ReservedMB                   | Current reserved memory in MB            |
| ReservedVCores               | Current reserved CPU in virtual cores    |
| ReservedContainers           | Current number of reserved containers    |
| ActiveUsers                  | Current number of active users           |
| ActiveApplications           | Current number of active applications    |
| FairShareMB                  | (FairScheduler only) Current fair share of memory in MB |
| FairShareVCores              | (FairScheduler only) Current fair share of CPU in virtual cores |
| MinShareMB                   | (FairScheduler only) Minimum share of memory in MB |
| MinShareVCores               | (FairScheduler only) Minimum share of CPU in virtual cores |
| MaxShareMB                   | (FairScheduler only) Maximum share of memory in MB |
| MaxShareVCores               | (FairScheduler only) Maximum share of CPU in virtual cores |

## NodeManagerMetrics

| Name                | Description                              |
| ------------------- | ---------------------------------------- |
| containersLaunched  | Total number of launched containers      |
| containersCompleted | Total number of successfully completed containers |
| containersFailed    | Total number of failed containers        |
| containersKilled    | Total number of killed containers        |
| containersIniting   | Current number of initializing containers |
| containersRunning   | Current number of running containers     |
| allocatedContainers | Current number of allocated containers   |
| allocatedGB         | Current allocated memory in GB           |
| availableGB         | Current available memory in GB           |



## ugi 

| Name                                 | Description                              |
| ------------------------------------ | ---------------------------------------- |
| LoginSuccessNumOps                   | Total number of successful kerberos logins |
| LoginSuccessAvgTime                  | Average time for successful kerberos logins in milliseconds |
| LoginFailureNumOps                   | Total number of failed kerberos logins   |
| LoginFailureAvgTime                  | Average time for failed kerberos logins in milliseconds |
| getGroupsNumOps                      | Total number of group resolutions        |
| getGroupsAvgTime                     | Average time for group resolution in milliseconds |
| getGroups*num*sNumOps                | Total number of group resolutions (*num* seconds granularity). *num* is specified by hadoop.user.group.metrics.percentiles.intervals. |
| getGroups*num*s50thPercentileLatency | Shows the 50th percentile of group resolution time in milliseconds (*num* seconds granularity). *num* is specified by hadoop.user.group.metrics.percentiles.intervals. |
| getGroups*num*s75thPercentileLatency | Shows the 75th percentile of group resolution time in milliseconds (*num* seconds granularity). *num* is specified by hadoop.user.group.metrics.percentiles.intervals. |
| getGroups*num*s90thPercentileLatency | Shows the 90th percentile of group resolution time in milliseconds (*num* seconds granularity). *num* is specified by hadoop.user.group.metrics.percentiles.intervals. |
| getGroups*num*s95thPercentileLatency | Shows the 95th percentile of group resolution time in milliseconds (*num* seconds granularity). *num* is specified by hadoop.user.group.metrics.percentiles.intervals. |
| getGroups*num*s99thPercentileLatency | Shows the 99th percentile of group resolution time in milliseconds (*num* seconds granularity). *num* is specified by hadoop.user.group.metrics.percentiles.intervals. |

## metricssystem 

| Name                   | Description                              |
| ---------------------- | ---------------------------------------- |
| NumActiveSources       | Current number of active metrics sources |
| NumAllSources          | Total number of metrics sources          |
| NumActiveSinks         | Current number of active sinks           |
| NumAllSinks            | Total number of sinks  (BUT usually less than NumActiveSinks, see [HADOOP-9946](https://issues.apache.org/jira/browse/HADOOP-9946)) |
| SnapshotNumOps         | Total number of operations to snapshot statistics from a metrics source |
| SnapshotAvgTime        | Average time in milliseconds to snapshot statistics from a metrics source |
| PublishNumOps          | Total number of operations to publish statistics to a sink |
| PublishAvgTime         | Average time in milliseconds to publish statistics to a sink |
| DroppedPubAll          | Total number of dropped publishes        |
| Sink_*instance*NumOps  | Total number of sink operations for the *instance* |
| Sink_*instance*AvgTime | Average time in milliseconds of sink operations for the *instance* |
| Sink_*instance*Dropped | Total number of dropped sink operations for the *instance* |
| Sink_*instance*Qsize   | Current queue length of sink operations  (BUT always set to 0 because nothing to increment this metrics, see [HADOOP-9941](https://issues.apache.org/jira/browse/HADOOP-9941)) |



## default

## StartupProgress

NameDescriptionElapsedTimeTotal elapsed time in millisecondsPercentCompleteCurrent rate completed in NameNode startup progress  (The max value is not 100 but 1.0)*phase*CountTotal number of steps completed in the phase*phase*ElapsedTimeTotal elapsed time in the phase in milliseconds*phase*TotalTotal number of steps in the phase*phase*PercentCompleteCurrent rate completed in the phase  (The max value is not 100 but 1.0)

