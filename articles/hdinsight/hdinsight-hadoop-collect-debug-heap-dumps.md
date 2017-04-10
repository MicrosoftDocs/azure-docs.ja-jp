---
title: "ヒープ ダンプによる Hadoop サービスのデバッグおよび分析 | Microsoft Docs"
description: "デバッグおよび分析に利用できるように、自動的に Hadoop サービスのヒープ ダンプを収集して Azure BLOB ストレージ アカウント内に配置します。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e4ec4ebb-fd32-4668-8382-f956581485c4
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 6407c371bc51461a05429fabaf38d3f9bc80d32c
ms.openlocfilehash: b6830f422b914722b8381a69c73cf8b02e107115
ms.lasthandoff: 02/07/2017


---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>BLOB ストレージのヒープ ダンプを収集して Hadoop サービスをデバッグおよび分析する
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

ヒープ ダンプには、ダンプが作成された時点の変数の値を含む、アプリケーションのメモリのスナップショットが含まれています。 これらは、実行時に発生する問題を診断するのに便利です。 Hadoop サービスのヒープ ダンプを自動的に収集し、HDInsightHeapDumps 下のユーザーの Azure BLOB ストレージ アカウント内に置くことができます。 

さまざまなサービスに対するヒープ ダンプの収集は、各クラスター上のサービスに対して、個別に有効にする必要があります。 既定では、クラスターに対してこの機能はオフになっています。 これらのヒープ ダンプのサイズは大きくなる可能性があるため、収集を有効にした後はヒープ ダンプの保存先である BLOB ストレージ アカウントを定期的に確認してください。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。 この記事の情報は、Windows ベースの HDInsight にのみ適用されます。 Linux ベースの HDInsight については、「[Linux ベースの HDInsight で Hadoop サービスのヒープ ダンプを有効にする](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>ヒープ ダンプの対象サービス
次のサービスのヒープ ダンプを有効にできます。

* **hcatalog** - tempelton
* **hive** - hiveserver2、metastore、derbyserver
* **mapreduce** - jobhistoryserver
* **yarn** - resourcemanager、nodemanager、timelineserver
* **hdfs** - datanode、secondarynamenode、namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>ヒープ ダンプを有効にする構成要素
サービスのヒープ ダンプを有効にするには、そのサービスのセクション内に適切な構成要素を設定する必要があります。これは **service_name** で指定します。

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

**service_name** の値には、上記のように、tempelton、hiveserver2、metastore、derbyserver、jobhistoryserver、resourcemanager、nodemanager、timelineserver、datanode、secondarynamenode、namenode のいずれかを指定できます。

## <a name="enable-using-azure-powershell"></a>Azure PowerShell を使用して有効にする
たとえば、Azure PowerShell を使用して jobhistoryserver のヒープ ダンプを有効にするには、次の操作を実行します。

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>.NET SDK を使用して有効にする
たとえば、Azure HDInsight .NET SDK を使用して jobhistoryserver のヒープ ダンプを有効にするには、次の操作を実行します。

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

