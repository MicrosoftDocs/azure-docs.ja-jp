---
title: Azure HDInsight での "hbase hbck" コマンド使用時のタイムアウト
description: リージョンの割り当てを修復する際の "hbase hbck" コマンドのタイムアウトの問題
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 4f3f1c22fa1dc05a66a8b6bf0179903a44cef9b6
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737433"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>シナリオ: Azure HDInsight での "hbase hbck" コマンド使用時のタイムアウト

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

リージョンの割り当てを `hbase hbck` コマンドを使用して修正するときにタイムアウトが発生します。

## <a name="cause"></a>原因

この問題の原因として考えられるのは、複数のリージョンが長時間にわたって "移行中" 状態にあることです。 Apache HBase Master UI には、これらのリージョンがオフラインとして表示される可能性があります。 移行を試みているリージョンが多数存在すると、HBase Master がタイムアウトする可能性があり、その結果それらのリージョンをオンライン状態に戻すことができなくなります。

## <a name="resolution"></a>解決策

1. SSH を使用して HDInsight HBase クラスターにサインインします。

1. `hbase zkcli` コマンドを実行して Zookeeper シェルに接続します。

1. `rmr /hbase/regions-in-transition` または `rmr /hbase-unsecure/regions-in-transition` コマンドを実行します。

1. `exit` コマンドを使用して `hbase zkcli` シェルを終了します。

1. Ambari UI を開き、Ambari から Active HBase Master サービスを再起動します。

1. HBase Master UI の "region in transition" セクションを監視して、停止しているリージョンがないことを確認します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる - Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上する Microsoft Azure の公式アカウント。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
