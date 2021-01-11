---
title: Azure HDInsight クラスターの VM を再起動する
description: HDInsight クラスターの応答していない VM を再起動する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: c0f0bd9eb423b3de6a602647dff93fd9fce6e13e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077016"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>HDInsight クラスターの VM を再起動する

HDInsight クラスターには、クラスター ノードとして VM のグループが含まれています。 実行時間の長いクラスターでは、さまざまな理由でこれらのノードが応答しなくなることがあります。 この記事では、HDInsight クラスター内の応答していない VM を再起動する方法について説明します。

## <a name="when-to-reboot"></a>再起動するタイミング

> [!WARNING]  
> クラスター内の VM を再起動すると、ノードのダウンタイムが発生し、ノード上のサービスが再起動されます。 

ノードの再起動中に、クラスターが異常になる可能性があり、ジョブの速度が低下したり、失敗したりすることがあります。 アクティブなヘッド ノードを再起動しようとすると、実行中のすべてのジョブが強制終了され、サービスが再び稼働状態になるまではクラスターにジョブを送信できません。 VM の再起動は、必要な場合にのみ検討するようにしてください。 VM の再起動を検討する場合に関するガイダンスを次に示します。

- ノードに SSH 接続できないが、ping には応答する。
- Ambari UI にハートビートが示されない状態でワーカー ノードが停止している。
- ノードの一時ディスクが満杯である。
- VM 上のプロセス テーブルには、プロセスが完了しているエントリが多数あるが、"終了状態" と示されている。

> [!WARNING]  
> **HBase** と **Kafka** クラスターの VM を再起動する場合は、データが失われる可能性があるため、より慎重に行う必要があります。

## <a name="use-powershell-to-reboot-vms"></a>PowerShell を使用して VM を再起動する

ノードの再起動操作を使用するには、ノードの一覧表示とノードの再起動という 2 つの手順が必要です。

1. ノードを一覧表示します。 クラスター ノードの一覧を取得するには [Get-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost) を使用します。 

  ```
  Get-AzHDInsightHost -ClusterName myclustername
  ```

2. ホストを再起動します。 再起動するノードの名前を取得したら、[Restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost) を使用してノードを再起動します。

  ```
  Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
  ```

## <a name="use-rest-api-to-reboot-vms"></a>REST API を使用して VM を再起動する

API ドキュメントの**使ってみる**機能を使用して、HDInsight に要求を送信できます。 ノードの再起動操作を使用するには、ノードの一覧表示とノードの再起動という 2 つの手順が必要です。

1. ノードを一覧表示します。 クラスター ノードの一覧は、REST API または Ambari から取得できます。 詳細については、[HDInsight の List Hosts REST API 操作](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts)に関する記事を参照してください。

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. ホストを再起動します。 再起動するノードの名前を取得したら、ノードの再起動 REST API を使用してノードを再起動します。ノード名は **"NodeType(wn/hn/zk/gw)" + "x" + "クラスター名の最初の 6 文字"** のパターンに従います。 詳細については、[HDInsight の Restart Hosts REST API 操作](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts)に関する記事を参照してください。

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

再起動するノードの実際の名前は、要求本文の JSON 配列に指定されています。

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>次のステップ

* [Restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [HDInsight Virtual machines REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](https://docs.microsoft.com/rest/api/hdinsight/)
