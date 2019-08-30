---
title: BindException - アドレスが Azure HDInsight で既に使用されている
description: BindException - アドレスが Azure HDInsight で既に使用されている
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: 8851a4dfb7deafab7ad77ef80619dd49ca46ed71
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947930"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>シナリオ: BindException - アドレスが Azure HDInsight で既に使用されている

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache HBase Region Server で再起動操作を完了できない。 リージョン サーバーの起動に失敗したワーカー ノードの `/var/log/hbase` ディレクトリにある `region-server.log` から、次のようなエラー メッセージが表示される場合があります。

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>原因

負荷の高いワークロード アクティビティ中に HBase リージョン サーバーを再起動しています。 ユーザーが Ambari UI から HBase リージョン サーバーで再起動処理を開始したときにバックグラウンドで何が行われているかを以下に示します。

1. Ambari エージェントがリージョン サーバーに停止要求を送信します。

1. その後 Ambari エージェントは、リージョン サーバーが正常にシャットダウンされるのを 30 秒間待ちます。

1. アプリケーションがリージョン サーバーへの接続を継続した場合、すぐにはシャットダウンされないため、先に 30 秒が経過してタイムアウトになります。

1. 30 秒の待ち時間が経過すると、Ambari エージェントは強制終了 (kill -9) をリージョン サーバーに送信します。

1. このように突然シャットダウンされるために、リージョン サーバーのプロセスが強制終了されても、プロセスに関連付けられているポートが解放されない場合があり、最終的に `AddressBindException` が発生します。

## <a name="resolution"></a>解決策

再起動を開始する前に HBase リージョン サーバーの負荷を軽減します。

または、ワーカー ノードで次のコマンドを使用してリージョン サーバーを手動で再起動します。

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティを適切なリソース (回答、サポート、エキスパートなど) に結び付けます。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
