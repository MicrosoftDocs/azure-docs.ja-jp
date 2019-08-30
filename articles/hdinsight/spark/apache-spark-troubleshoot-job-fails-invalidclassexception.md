---
title: Azure HDInsight で、Apache Spark ジョブが InvalidClassException で失敗し、クラスバージョンが一致しない
description: Azure HDInsight で、Apache Spark ジョブが InvalidClassException で失敗し、クラスバージョンが一致しない
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 6f81ed121c98c8428e710082aae7ab75b672335e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707416"
---
# <a name="scenario-apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>シナリオ: Azure HDInsight で、Apache Spark ジョブが InvalidClassException で失敗し、クラスバージョンが一致しない

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と考えられる解決策について説明します。

## <a name="issue"></a>問題

Spark 2.x クラスターに Apache Spark ジョブを作成しようとします。 次のようなエラーで失敗します。

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>原因

このエラーは、`spark.yarn.jars` 構成に jar をさらに追加することにより発生することがあります。これは、別のバージョンの `commons-lang3` パッケージを含み、クラスの不一致を招く「シェーデッド」jar になります。 既定では、Spark 2.1/2/3 はバージョン 3.5 の `commons-lang3` を使用します。

## <a name="resolution"></a>解決策

jar を削除するか、カスタマイズした jar (AzureLogAppender) を再コンパイルし、[maven-shade-plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) を使用してクラスを再配置します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる - Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上する Microsoft Azure の公式アカウント。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)に関するページをご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
