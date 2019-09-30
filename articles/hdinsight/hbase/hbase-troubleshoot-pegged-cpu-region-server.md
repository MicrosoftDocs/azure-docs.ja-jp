---
title: Azure HDInsight の Apache HBase クラスターのリージョン サーバーの固定 CPU
description: Azure HDInsight の Apache HBase クラスター内のリージョン サーバー上にある固定 CPU のトラブルシューティング
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 0712e6e8e9fe6db370d913d04e562c19b72d69a7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091660"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>シナリオ: Azure HDInsight の Apache HBase クラスターのリージョン サーバーの固定 CPU

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache HBase リージョン サーバー プロセスの CPU 占有率が 200% に近くなり始め、HBase Master プロセスでアラートが発生し、クラスターが全容量で機能しなくなります。

## <a name="cause"></a>原因

HBase クラスター v3.4 を実行している場合に、jdk バージョン 1.7.0_151 へのアップグレードが原因である潜在的なバグに遭遇することがあります。 確認されている症状は、リージョン サーバーのプロセスが 200% に近い CPU 占有率を示し始めるというものです (これを検証するには、`top` コマンドを実行します。CPU 占有率が 200% に近いプロセスがある場合、その pid を取得し、`ps -aux | grep` を実行して、それがリージョン サーバーのプロセスであることを確認します)。

## <a name="resolution"></a>解決策

1. 次のようにして、クラスターのすべてのノードに jdk 1.8 をインストールします。

    * スクリプト操作 `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` を実行します。 必ず、すべてのノードで実行するオプションを選択してください。

    * すべての個別ノードにサインインして、コマンド `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` を実行することもできます。

1. Ambari UI に移動します - `https://<clusterdnsname>.azurehdinsight.net`。

1. **[HBase]->[Configs]->[Advanced]->[Advanced]** `hbase-env configs` に移動し、変数 `JAVA_HOME` を `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` に変更します。 構成変更を保存します。

1. (省略可能だが推奨) [クラスター上のすべてのテーブルをフラッシュします](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)。

1. もう一度 Ambari UI から、再起動が必要なすべての HBase サービスを再起動します。

1. クラスター上のデータによっては、クラスターが安定した状態になるまでに数分から最大 1 時間かかる場合があります。 クラスターが安定した状態になったことを確認するには、Ambari (refresh) から HMaster UI を確認します (すべてのリージョン サーバーがアクティブなはずです)。または、ヘッドノードから HBase シェルを実行し、status コマンドを実行します。

アップグレードが成功したことを確認するには、適切な java バージョンを使用して、関連する HBase プロセスが開始されていることを確認します。たとえば、リージョン サーバーの場合は次のように確認します。

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
