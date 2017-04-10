---
title: "Ambari API を使用した HDInsight の Hadoop クラスターの監視 | Microsoft Docs"
description: "Apache Ambari API は、Hadoop クラスターの作成、管理、および監視を目的として使用します。 直観的なオペレーター ツールと API によって、Hadoop の複雑さに悩まされずに作業を進められます。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
editor: cgronlun
manager: jhubbard
ms.assetid: 052135b3-d497-4acc-92ff-71cee49356ff
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 0587dfcd6079fc8df91bad5a5f902391d3657a6b
ms.openlocfilehash: 6d36976712ba1ea5d51f203fc532d7f89c3b0871
ms.lasthandoff: 12/08/2016


---
# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Ambari API を使用した HDInsight の Hadoop クラスターの監視
Ambari API を使用して HDInsight クラスターを監視する方法について説明します。

> [!NOTE]
> この記事の情報は、Ambari REST API の読み取り専用バージョンを提供する Windows ベースの HDInsight クラスターを主に対象としたものです。 Linux ベースのクラスターについては、 [Ambari を使用した Hadoop クラスターの管理](hdinsight-hadoop-manage-ambari.md)に関するページを参照してください。
> 
> 

## <a name="what-is-ambari"></a>Ambari とは
[Apache Ambari][ambari-home] は、Apache Hadoop クラスターのプロビジョニング、管理、監視を目的としています。 演算子ツールの直観的なコレクションおよび Hadoop の複雑さが見えないようにする信頼性の高い、一連の API が含まれており、クラスターの操作を単純化しています。 API の詳細については、「[Ambari API リファレンス][ambari-api-reference]」をご覧ください。 

HDInsight は現在、Ambari の監視機能のみをサポートしています。 Ambari API 1.0 は HDInsight バージョン 3.0 と 2.1 のクラスターによってサポートされています。 この記事では、HDInsight バージョン 3.1 と 2.1 のクラスターでの Ambari API へのアクセスを取り上げます。 この 2 つの重要な相違点は、新しい機能 (ジョブ履歴サーバーなど) の導入により、一部のコンポーネントに変更が加えられた点です。 

**前提条件**

このチュートリアルを開始する前に、次の項目を用意する必要があります。

* **Azure PowerShell を実行できるワークステーション**。
* [cURL][curl] は省略可能です。 インストールするには、「[cURL のリリースとダウンロード][curl-download]」をご覧ください。
  
  > [!NOTE]
  > Windows で cURL コマンドを使用する場合、オプション値には一重引用符の代わりに二重引用符を使用します。
  > 
  > 
* **Azure HDInsight クラスター**。 クラスターのプロビジョニングの手順については、「[Azure HDInsight の概要][hdinsight-get-started]」または「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」をご覧ください。 このチュートリアルを読み進めるには、次のデータが必要です。
  
  | クラスター プロパティ | Azure PowerShell 変数名 | 値 | Description |
  | --- | --- | --- | --- |
  |   HDInsight クラスター名 |$clusterName | |HDInsight クラスターの名前です。 |
  |   クラスター ユーザー名 |$clusterUsername | |クラスターのユーザー名は、クラスターの作成時に指定されています。 |
  |   クラスター パスワード |$clusterPassword | |クラスター ユーザー パスワードです。 |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Jump-start
いくつかの方法で、Ambari を使用して HDInsight クラスターを監視することができます。

**Azure PowerShell の使用**

以下の Azure PowerShell スクリプトは、*HDInsight 3.1 クラスターで* MapReduce job tracker 情報を取得します。  ここで重要な相違点は、(MapReduce ではなく) YARN サービスから詳細をプルする点です。

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

以下の PowerShell スクリプトは、*HDInsight 2.1 クラスターで* MapReduce job tracker 情報を取得します。

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

出力は次のようになります。

![Jobtracker の出力][img-jobtracker-output]

**cURL の使用**

以下の例では、cURL を使用してクラスター情報を取得します。

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

出力は次のようになります。

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**2014 年 10 月 8 日のリリースの注意点**:

Ambari エンドポイント (https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}) を使用すると、*host_name* フィールドがホスト名そのものではなくノードの完全修飾ドメイン名 (FQDN) を返します。 2014 年 10 月 8 日のリリースの前は、このサンプルは単純に "**headnode0**" を返していました。 2014 年 10 月 8 日のリリースの後は、上の例に示すように FQDN である "**headnode0.{ClusterDNS}.azurehdinsight.net**" が取得されます。 この変更は、1 つの仮想ネットワーク (VNET) に HBase や Hadoop などの複数のクラスターの種類をデプロイできるシナリオの実現を容易にするために必須でした。 このシナリオは、Hadoop のバックエンド プラットフォームとして HBase を使用する場合などが該当します。

## <a name="ambari-monitoring-apis"></a>Ambari での API の監視
以下のテーブルに最も一般的な Ambari での API 呼び出しの監視の一部を示します。 API の詳細については、「[Ambari API リファレンス][ambari-api-reference]」をご覧ください。

| API 呼び出しの監視 | URI | Description |
| --- | --- | --- |
| クラスターの取得 |`/api/v1/clusters` | |
| クラスター情報の取得 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |クラスター、サービス、ホスト |
| サービスの取得 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |含まれるサービス: hdfs、mapreduce |
| サービス情報の取得 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| サービス コンポーネントの取得 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS: namenode、datanodeMapReduce: jobtracker; tasktracker |
| コンポーネント情報の取得 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo、host-components、metrics |
| ホストの取得 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0、workernode0 |
| ホスト情報の取得 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| ホスト コンポーネントの取得 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode、resourcemanager |
| ホスト コンポーネント情報の取得 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles、component、host、metrics |
| 構成の取得 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |構成の種類: core-site、hdfs-site、mapred-site、hive-site |
| 構成情報の取得 |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |構成の種類: core-site、hdfs-site、mapred-site、hive-site |

## <a name="next-steps"></a>次のステップ
ここでは Ambari での API 呼び出しの監視の使用方法を説明しました。 詳細については、次を参照してください。

* [Azure Portal を使用した HDInsight クラスターの管理][hdinsight-admin-portal]
* [Azure PowerShell を使用した HDInsight クラスターの管理][hdinsight-admin-powershell]
* [コマンド ライン インターフェイスを使用した HDInsight クラスターの管理][hdinsight-admin-cli]
* [HDInsight のドキュメント][hdinsight-documentation]
* [HDInsight の概要][hdinsight-get-started]

[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png

