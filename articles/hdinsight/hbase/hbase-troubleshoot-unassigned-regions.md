---
title: Azure HDInsight のリージョン サーバーに関する問題
description: Azure HDInsight のリージョン サーバーに関する問題
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/16/2019
ms.openlocfilehash: 6e734a661557b024257fcd1b9d9c2da6a3bc8f85
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640221"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Azure HDInsight のリージョン サーバーに関する問題

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="scenario-unassigned-regions"></a>シナリオ: 未割り当てリージョン

### <a name="issue"></a>問題

`hbase hbck` コマンドを実行しているときに、次のようなエラー メッセージが表示されます。

```
multiple regions being unassigned or holes in the chain of regions
```

Apache HBase Master UI から、すべてのリージョン サーバーのバランスが取れていないリージョンの数を確認できます。 この場合、`hbase hbck` コマンドを実行して、リージョン チェーンの穴を確認できます。

### <a name="cause"></a>原因

この穴は、オフライン リージョンによって生じた可能性があります。

### <a name="resolution"></a>解決策

割り当てを修正します。 以下の手順に従って、未割り当てのリージョンを通常の状態に戻してください。

1. SSH を使用して HDInsight HBase クラスターにサインインします。

1. `hbase zkcli` コマンドを実行して ZooKeeper シェルに接続します。

1. `rmr /hbase/regions-in-transition` または `rmr /hbase-unsecure/regions-in-transition` コマンドを実行します。

1. `exit` コマンドを使用して Zookeeper シェルを終了します。

1. Apache Ambari UI を開き、Active HBase Master サービスを再起動します。

1. `hbase hbck` コマンドを (オプションを追加せずに) もう一度実行します。 出力をチェックして、すべてのリージョンが割り当てられていることを確認します。

---

## <a name="scenario-dead-region-servers"></a>シナリオ: 稼働していないリージョン サーバー

### <a name="issue"></a>問題

リージョン サーバーを起動できません。

### <a name="cause"></a>原因

WAL ディレクトリが複数に分割されています。

1. 現在の WAL の一覧を取得します: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`。

1. `wals.out` ファイルを確認します。 (*-splitting で始まる) 分割ディレクトリの数が多すぎる場合、これらのディレクトリのためにリージョン サーバーが失敗している可能性があります。

### <a name="resolution"></a>解決策

1. Ambari ポータルから HBase を停止します。

1. `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` を実行して、最新の WAL の一覧を取得します。

1. *-splitting ディレクトリを一時フォルダー `splitWAL` に移動して、*-splitting ディレクトリを削除します。

1. `hbase zkcli` コマンドを実行して Zookeeper シェルに接続します。

1. `rmr /hbase-unsecure/splitWAL` を実行します。

1. HBase サービスを再起動します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
