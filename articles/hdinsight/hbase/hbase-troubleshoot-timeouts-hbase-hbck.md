---
title: Azure HDInsight での "hbase hbck" コマンド使用時のタイムアウト
description: リージョンの割り当てを修復する際の "hbase hbck" コマンドのタイムアウトの問題
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 800182498ab77993d0861c9b5383e0d71b302b6f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091571"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>シナリオ: Azure HDInsight での "hbase hbck" コマンド使用時のタイムアウト

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

リージョンの割り当てを `hbase hbck` コマンドを使用して修正するときにタイムアウトが発生します。

## <a name="cause"></a>原因

`hbck` コマンドを使用したときのタイムアウトの問題の原因として、複数のリージョンが長時間にわたって "移行中" 状態にあることが考えられます。 これらのリージョンは、HBase Master UI にオフラインとして表示されていることがわかります。 多数のリージョンが移行を試行中であるために、HBase Master がタイムアウトし、それらのリージョンをオンラインに戻すことができなくなります。

## <a name="resolution"></a>解決策

1. SSH を使用して HDInsight HBase クラスターにサインインします。

1. `hbase zkcli` コマンドを実行して Apache ZooKeeper シェルに接続します。

1. `rmr /hbase/regions-in-transition` または `rmr /hbase-unsecure/regions-in-transition` コマンドを実行します。

1. `exit` コマンドを使用して `hbase zkcli` シェルを終了します。

1. Apache Ambari UI から、Active HBase Master サービスを再起動します。

1. `hbase hbck -fixAssignments` コマンドを実行します。

1. HBase Master UI の "region in transition" セクションを監視して、停止しているリージョンがないことを確認します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

- [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

- [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

- さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
