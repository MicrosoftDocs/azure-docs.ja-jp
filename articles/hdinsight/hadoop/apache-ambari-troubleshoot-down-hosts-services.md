---
title: Azure HDInsight で Apache Ambari UI にダウンしたホストとサービスが表示される
description: Azure HDInsight で Apache Ambari UI にダウンしたホストとサービスが表示される
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/02/2019
ms.openlocfilehash: 1227635a193fdf1b55e2c1cc141a837a250ee08c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841946"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>シナリオ: Azure HDInsight で Apache Ambari UI にダウンしたホストとサービスが表示される

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Ambari UI にアクセスできますが、ほとんどのすべてのサービスがダウンしていると UI に表示され、すべてのホストがハートビート喪失を示しています。

## <a name="cause"></a>原因

ほとんどのシナリオで、これはアクティブなヘッドノードで実行されていない Ambari サーバーの問題です。 どのヘッドノードがアクティブなヘッドノードであるかを確認し、ambari-server が正しいヘッドノードで実行されていることを確認します。 ambari-server を手動で開始するのではなく、フェールオーバー コントローラー サービスの責任下で ambari-server を正しいヘッドノードで開始してください。 アクティブなヘッドノードを再起動して、フェールオーバーを強制します。

この問題はネットワークの問題によって発生する可能性もあります。 各クラスター ノードから、ping `headnodehost` を実行できるかどうか確認します。 次のように、どのクラスター ノードも `headnodehost` に接続できないまれな状況があります。

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>解決策

通常、アクティブなヘッドノードを再起動すると、この問題が軽減されます。 されない場合は、HDInsight サポート チームにお問い合わせください。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる - Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上する Microsoft Azure の公式アカウント。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)に関するページをご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
