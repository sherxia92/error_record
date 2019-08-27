# Nexus error record

1. OrientDB
    ```script
    2019-08-27 13:28:55,293+0800 WARN  [FelixStartLevel]  *SYSTEM com.orientechnologies.orient.core.storage.impl.local.paginated.OLocalPaginatedStorage - $ANSI{green {db=config}} Storage 'config' was not closed properly. Will try to recover from write ahead log...
    2019-08-27 13:28:55,294+0800 INFO  [FelixStartLevel]  *SYSTEM com.orientechnologies.orient.core.storage.impl.local.paginated.OLocalPaginatedStorage - $ANSI{green {db=config}} Looking for last checkpoint...
    2019-08-27 13:28:56,404+0800 ERROR [FelixStartLevel]  *SYSTEM com.orientechnologies.orient.core.storage.impl.local.paginated.OLocalPaginatedStorage - Exception `231C1FF1` in storage `plocal:D:/software/nexus-3.13.0-01-win64/sonatype-work/nexus3/db/config`: 2.2.36 (build d3beb772c02098ceaea89779a7afd4b7305d3788, branch 2.2.x)
    java.lang.OutOfMemoryError: Java heap space
        at com.orientechnologies.orient.core.storage.impl.local.paginated.wal.OWALPageV2.getRecord(OWALPageV2.java:112)
        at com.orientechnologies.orient.core.storage.impl.local.paginated.wal.OLogSegmentV2.readRecord(OLogSegmentV2.java:748)
        at com.orientechnologies.orient.core.storage.impl.local.paginated.wal.ODiskWriteAheadLog.read(ODiskWriteAheadLog.java:848)
        at com.orientechnologies.orient.core.storage.impl.local.OAbstractPaginatedStorage.restoreFromWAL(OAbstractPaginatedStorage.java:4674)
        at com.orientechnologies.orient.core.storage.impl.local.OAbstractPaginatedStorage.recoverIfNeeded(OAbstractPaginatedStorage.java:3932)
        at com.orientechnologies.orient.core.storage.impl.local.OAbstractPaginatedStorage.open(OAbstractPaginatedStorage.java:288)
        at com.orientechnologies.orient.core.db.document.ODatabaseDocumentTx.open(ODatabaseDocumentTx.java:259)
        at org.sonatype.nexus.orient.DatabaseManagerSupport.connect(DatabaseManagerSupport.java:143)
        at org.sonatype.nexus.orient.DatabaseInstanceImpl.doStart(DatabaseInstanceImpl.java:59)
        at org.sonatype.goodies.lifecycle.LifecycleSupport.start(LifecycleSupport.java:104)
        at org.sonatype.goodies.lifecycle.Lifecycles.start(Lifecycles.java:44)
        at org.sonatype.nexus.orient.DatabaseManagerSupport.createInstance(DatabaseManagerSupport.java:252)
        at org.sonatype.nexus.orient.DatabaseManagerSupport$$Lambda$44/2130481737.apply(Unknown Source)
        at java.util.concurrent.ConcurrentHashMap.computeIfAbsent(Unknown Source)
        at org.sonatype.nexus.orient.DatabaseManagerSupport.instance(DatabaseManagerSupport.java:231)
        at org.sonatype.nexus.orient.restore.RestoreServiceImpl$$Lambda$43/463226765.accept(Unknown Source)
        at java.util.stream.ForEachOps$ForEachOp$OfRef.accept(Unknown Source)
        at java.util.Spliterators$ArraySpliterator.forEachRemaining(Unknown Source)
        at java.util.stream.AbstractPipeline.copyInto(Unknown Source)
        at java.util.stream.ForEachOps$ForEachTask.compute(Unknown Source)
        at java.util.concurrent.CountedCompleter.exec(Unknown Source)
        at java.util.concurrent.ForkJoinTask.doExec(Unknown Source)
        at java.util.concurrent.ForkJoinTask.doInvoke(Unknown Source)
        at java.util.concurrent.ForkJoinTask.invoke(Unknown Source)
        at java.util.stream.ForEachOps$ForEachOp.evaluateParallel(Unknown Source)
        at java.util.stream.ForEachOps$ForEachOp$OfRef.evaluateParallel(Unknown Source)
        at java.util.stream.AbstractPipeline.evaluate(Unknown Source)
        at java.util.stream.ReferencePipeline.forEach(Unknown Source)
        at java.util.stream.ReferencePipeline$Head.forEach(Unknown Source)
        at org.sonatype.nexus.orient.restore.RestoreServiceImpl.doStart(RestoreServiceImpl.java:76)
        at org.sonatype.nexus.common.stateguard.StateGuardLifecycleSupport.start(StateGuardLifecycleSupport.java:67)
        at org.sonatype.nexus.orient.restore.RestoreServiceImpl$$EnhancerByGuice$$b41e74a0.CGLIB$start$1(<generated>)
    2019-08-27 13:28:56,451+0800 ERROR [FelixStartLevel]  *SYSTEM org.sonatype.nexus.orient.DatabaseInstanceImpl - Lifecycle operation start failed
    ```
    ###### 参考
    [Fix Sonatype Nexus OrientDB](https://gist.github.com/marcelmaatkamp/123e8793e07a72a382d8d0e8d66bbd8f)
    
    [OrientDB corruption state in Nexus Repository version 3.2.0-01](https://stackoverflow.com/questions/42951710/orientdb-corruption-state-in-nexus-repository-version-3-2-0-01)
    
    [Nexus3 OrientDB Console使用](https://blog.csdn.net/annkie/article/details/84337641)
    ```shell script
    Unix
        java -jar ./lib/support/nexus-orient-console.jar
    Windows
        java -jar lib\support\nexus-orient-console.jar
    Mac
        .install4j/jre.bundle/Contents/Home/jre/bin/java -jar ./lib/support/nexus-orient-console.jar
    
    
    orientdb>CONNECT PLOCAL:${your_install_dir}/sonatype-work/nexus3/db/${database} admin admin
    orientdb {db=${database}> REBUILD INDEX *
    orientdb {db=${database}> REPAIR DATABASE --fix-graph
    orientdb {db=${database}> REPAIR DATABASE --fix-links
    orientdb {db=${database}> REPAIR DATABASE --fix-ridbags
    orientdb {db=${database}> REPAIR DATABASE --fix-bonsai
    orientdb {db=${database}> DISCONNECT
    ```
    
    ###### widdow下我的记录
    ```shell script
    D:\nexus-3.18.1-01-win64\nexus-3.18.1-01>java -jar lib\support\nexus-orient-console.jar
    orientdb> CONNECT PLOCAL:D:/nexus-3.18.1-01-win64/sonatype-work/nexus3/db/config admin admin
    
    Disconnecting from the database [null]...OK
    Connecting to database [PLOCAL:D:/nexus-3.18.1-01-win64/sonatype-work/nexus3/db/config] with user 'admin'...OK
    orientdb {db=config}> REBUILD INDEX *
    
    
    Rebuilding index(es)...
    Rebuilt index(es). Found 32 link(s) in 0.434000 sec(s).
    
    
    Index(es) rebuilt successfully
    orientdb {db=config}> REPAIR DATABASE --fix-graph
    
    Repairing database...
    - Removing broken links...
    -- Done! Fixed links: 0, modified documents: 0
    Repair database complete (0 errors)
    orientdb {db=config}> REPAIR DATABASE --fix-links
    
    Repairing database...
    - Removing broken links...
    -- Done! Fixed links: 0, modified documents: 0
    Repair database complete (0 errors)
    orientdb {db=config}> REPAIR DATABASE --fix-ridbags
    
    Repairing database...
    - Removing broken links...
    -- Done! Fixed links: 0, modified documents: 0
    Repair database complete (0 errors)
    orientdb {db=config}> REPAIR DATABASE --fix-bonsai
    
    Repairing database...
    - Removing broken links...
    -- Done! Fixed links: 0, modified documents: 0
    Repair database complete (0 errors)
    orientdb {db=config}> DISCONNECT
    
    Disconnecting from the database [config]...OK
    orientdb> exit
    ```

