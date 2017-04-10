---
title: "メモリ不足エラー (OOM) - Hive 設定 | Microsoft Docs"
description: "HDInsight の Hadoop で、Hive クエリによるメモリ不足 (OOM) エラーを修正します。 ユーザーのシナリオは、多数の大きなテーブルに対するクエリです。"
keywords: "メモリ不足エラー、OOM、Hive 設定"
services: hdinsight
documentationcenter: 
author: rashimg
manager: jhubbard
editor: cgronlun
ms.assetid: 7bce3dff-9825-4fa0-a568-c52a9f7d1dad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2017
ms.author: rashimg;jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: bf0ff13a2d5ffc5bf0b07b80f482fc4144b0cd0f
ms.lasthandoff: 11/17/2016


---
# <a name="fix-an-out-of-memory-oom-error-with-hive-memory-settings-in-hadoop-in-azure-hdinsight"></a>Azure HDInsight の Hadoop の Hive メモリ設定を使用してメモリ不足 (OOM) エラーを修正する
ユーザーがよく経験する問題として、Hive を使用するときに発生するメモリ不足 (OOM) エラーがあります。 この記事では、ユーザーのシナリオと、問題を解決するために推奨される Hive 設定について説明します。

## <a name="scenario-hive-query-across-large-tables"></a>シナリオ: 大きなテーブル全体に対する Hive クエリ
ユーザーは Hive を使用して次のクエリを実行しました。

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

このクエリの特徴:

* T1 は、STRING 型の列が多数ある大きなテーブル TABLE1 のエイリアスです。
* 他のテーブルはそれほど大きくありませんが、多数の列があります。
* すべてのテーブルは相互に結合されています。場合によっては、TABLE1 などのテーブルにある複数の列によって結合されています。

ユーザーが 24 ノード A3 クラスターの MapReduce に Hive を使用してクエリを実行すると、クエリの実行時間は約 26 分間になりました。 ユーザーは、MapReduce に Hive を使用してクエリを実行すると、次の警告メッセージが表示されることに気づきました。

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

クエリは約 26 分間で実行を完了したので、ユーザーは警告を無視し、このクエリのパフォーマンスをさらに改善する方法に注目し始めました。

ユーザーは「 [HDInsight の Hadoop に対する Hive クエリの最適化](hdinsight-hadoop-optimize-hive-query.md)」を参照して、Tez 実行エンジンを使用することにしました。 Tez 設定を有効にして同じクエリを実行すると、クエリの実行時間は 15 分間になり、次のエラーがスローされました。

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

そこで、ユーザーは大きな VM の方がヒープ領域が増えると考えて、大きな VM (つまり D12) を使用することにしました。 それでも同じエラーが発生します。 ユーザーは HDInsight チームに連絡し、この問題のデバッグについて相談しました。

## <a name="debug-the-out-of-memory-oom-error"></a>メモリ不足 (OOM) エラーのデバッグ
サポート チームとエンジニアリング チームは協力して、メモリ不足 (OOM) エラーの原因となる問題の 1 つが、 [Apache JIRA の既知の問題](https://issues.apache.org/jira/browse/HIVE-8306)であることを見つけました。 JIRA の説明を引用します。

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

hive-site.xml ファイルを確認したところ、**hive.auto.convert.join.noconditionaltask** が **true** に設定されていることが判明しました。

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
              Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
              If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
              specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
      </property>

警告と JIRA の説明に基づき、Map Join が Java ヒープ領域 OOM エラーの原因であると仮説を立てました。 そしてこの問題をさらに深く掘り下げました。

[HDInsight の Hadoop Yarn メモリ設定](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx)に関するブログの投稿で説明したように、Tez 実行エンジンを使用すると、使用されるヒープ領域は、実際には Tez コンテナーに属します。 Tez コンテナー メモリについて説明した次の図を参照してください。

![Tez コンテナー メモリ図: Hive のメモリ不足 (OOM) エラー](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

ブログの投稿で提案したように、**hive.tez.container.size** と **hive.tez.java.opts** という 2 つのメモリ設定で、ヒープのコンテナー メモリを定義しています。 経験から判断すると、OOM 例外の原因は、コンテナー サイズが小さすぎることではありません。 Java ヒープ サイズ (hive.tez.java.opts) が小さすぎることが原因です。 そのため、OOM が発生する場合は、 **hive.tez.java.opts**を増やしてみてください。 必要に応じて、 **hive.tez.container.size**も増やす必要があります。 **java.opts** 設定は、**container.size** の約 80% にすることをお勧めします。

> [!NOTE]
> **hive.tez.java.opts** は、常に **hive.tez.container.size** よりも小さく設定する必要があります。
> 
> 

D12 コンピューターには 28 GB のメモリがあるので、10 GB (10,240 MB) のコンテナー サイズを使用し、80% を java.opts に割り当てることにしました。 そのために、Hive コンソールで次の設定を使用しました。

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

これらの設定に基づいて、クエリは 10 分間未満で正常に実行されました。

## <a name="conclusion-oom-errors-and-container-size"></a>結論: OOM エラーとコンテナー サイズ
OOM エラーの原因は、必ずしもコンテナー サイズが小さすぎるためではありません。 コンテナー サイズではなくヒープ サイズを増やし、コンテナー メモリ サイズの 80% 以上を割り当てるようにメモリ設定を構成することをお勧めします。


