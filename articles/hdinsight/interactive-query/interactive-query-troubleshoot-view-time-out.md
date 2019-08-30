---
title: Azure HDInsight でクエリ結果をフェッチしているときに Apache Hive ビューがタイムアウトになる
description: Azure HDInsight でクエリ結果をフェッチしているときに Apache Hive ビューがタイムアウトになる
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: e5b16dc9163172b1c080a397990451cde685d25f
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947898"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>シナリオ: Azure HDInsight でクエリ結果をフェッチしているときに Apache Hive ビューがタイムアウトになる

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive ビューから特定のクエリを実行しているときに、次のエラーが発生する可能性があります。

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>原因

Hive ビューの既定のタイムアウト値が、実行しているクエリに適していない可能性があります。 指定された時間が短すぎて、Hive ビューがクエリ結果をフェッチできません。

## <a name="resolution"></a>解決策

`/etc/ambari-server/conf/ambari.properties` で次のプロパティを設定して、Apache Ambari Hive ビューのタイムアウト値を増加してください。

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

`HIVE_VIEW_INSTANCE_NAME` の値は、Hive ビューの URL の末尾にあります。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる - Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上する Microsoft Azure の公式アカウント。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)に関するページをご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
