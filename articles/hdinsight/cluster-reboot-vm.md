---
title: Azure HDInsight クラスターの VM を再起動する
description: Azure HDInsight クラスターの応答していない VM を再起動する方法について説明します。
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: a61735dba60860459d007eb54d4655f41d5ae51a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946868"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>HDInsight クラスターの VM を再起動する

Azure HDInsight クラスターには、クラスター ノードとして仮想マシン (VM) のグループが含まれています。 実行時間の長いクラスターでは、さまざまな理由でこれらのノードが応答しなくなることがあります。 この記事では、HDInsight クラスター内の応答していない VM を再起動する方法について説明します。

## <a name="when-to-reboot"></a>再起動するタイミング

> [!WARNING]
> クラスターで VM を再起動すると、ノードは使用できなくなり、ノード上のサービスは再起動する必要があります。

ノードの再起動中はクラスターが異常な状態になる場合があり、ジョブの速度が低下したり、ジョブが失敗したりする可能性があります。 アクティブなヘッド ノードを再起動しようとすると、実行中のすべてのジョブが停止します。 サービスが再起動されて実行中となるまで、クラスターにジョブを送信することはできません。 これらの理由から、必要な場合にのみ VM を再起動する必要があります。 次の場合に、VM の再起動を検討してください。

- SSH を使用してノードにアクセスできないが、ping には応答する。
- ワーカー ノードが停止していて、Ambari UI にハートビートが表示されない。
- ノードの一時ディスクが満杯である。
- VM 上のプロセス テーブルには、プロセスが完了しているエントリが多数あるが、"終了状態" と示されている。

> [!NOTE]
> 再起動によってデータが失われる可能性があるため、**HBase** クラスターと **Kafka** クラスターでは VM を再起動できません。

## <a name="use-powershell-to-reboot-vms"></a>PowerShell を使用して VM を再起動する

ノードの再起動操作を使用するには、ノードの一覧表示とノードの再起動という 2 つの手順が必要です。

1. ノードを一覧表示します。 クラスター ノードの一覧を取得するには、[Get-AzHDInsightHost](/powershell/module/az.hdinsight/get-azhdinsighthost) を使用します。

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. ホストを再起動します。 再起動するノードの名前を取得したら、[Restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost) を使用してノードを再起動します。

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>REST API を使用して VM を再起動する

API ドキュメントの **使ってみる** 機能を使用して、HDInsight に要求を送信できます。 ノードの再起動操作を使用するには、ノードの一覧表示とノードの再起動という 2 つの手順が必要です。

1. ノードを一覧表示します。 クラスター ノードの一覧は、REST API または Ambari から取得できます。 詳細については、[HDInsight list hosts REST API の操作](/rest/api/hdinsight/virtualmachines/listhosts)に関するページを参照してください。

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. ホストを再起動します。 再起動するノードの名前を取得したら、ノードを再起動する REST API を使用して、ノードを再起動します。 ノード名は、*NodeType(wn/hn/zk/gw)*  + *x* + *クラスター名の最初の 6 文字* のパターンに従います。 詳細については、[HDInsight restart hosts REST API の操作](/rest/api/hdinsight/virtualmachines/restarthosts)に関するページを参照してください。

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

* [Restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [HDInsight virtual machines REST API](/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](/rest/api/hdinsight/)