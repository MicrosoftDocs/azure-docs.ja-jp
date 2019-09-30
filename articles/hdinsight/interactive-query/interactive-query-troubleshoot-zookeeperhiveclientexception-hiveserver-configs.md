---
title: Azure HDInsight で Apache Hive Zeppelin インタープリターから Zookeeper エラーが発生する
description: Apache Zeppelin Hive JDBC インタープリターが、Azure HDInsight で間違った URL を指しています
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 185d8e4954091d9b54e0604fc2bd3f3d010f926e
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091319"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>シナリオ: Azure HDInsight で Apache Hive Zeppelin インタープリターから Zookeeper エラーが発生する

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive LLAP クラスターで、クエリを実行しようとすると Zeppelin インタープリターによって次のエラー メッセージが表示されます。

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>原因

The Zeppelin Hive JDBC Interpreter is pointing to the wrong URL. (Zeppelin Hive JDBC インタープリターが間違った URL を指しています。)

## <a name="resolution"></a>解決策

1. Hive コンポーネントの概要に移動し、"Hive JDBC Url" をクリップボードにコピーします。

1. `https://clustername.azurehdinsight.net/zeppelin/#/interpreter` に移動します

1. JDBC の設定を編集する: hive.url の値を、手順 1 でコピーした Hive JDBC URL に更新します

1. 保存してから、クエリを再試行します

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
