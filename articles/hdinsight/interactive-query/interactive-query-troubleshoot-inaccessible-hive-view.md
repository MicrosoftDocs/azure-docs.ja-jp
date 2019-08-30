---
title: Apache Hive から Azure HDInsight の Apache Zookeeper への接続を確立できない
description: ZooKeeper の問題のために Hive ビューにアクセスできない
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: d8e94627cefccc0be4cbe35ae09104fb104034f6
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816890"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>シナリオ: Apache Hive から Azure HDInsight の Apache Zookeeper への接続を確立できない

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Hive ビューにアクセスできません。また、`/var/log/hive` のログに次のようなエラーが表示されます。

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>原因

Hive からZookeeper への接続を確立できないことがあります。これにより、Hive ビューを起動できません。

## <a name="resolution"></a>解決策

1. Zookeeper サービスが正常であることを確認します。

1. Zookeeper サービスに Hive Server2 の ZNode エントリがあるかどうかを確認します。 値が見つからないか、正しくありません。

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. 接続を再確立するには、Zookeeper ノードを再起動し、HiveServer2 を再起動します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる - Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上する Microsoft Azure の公式アカウント。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、[Azure のサポート リクエストを作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)に関するページを参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
