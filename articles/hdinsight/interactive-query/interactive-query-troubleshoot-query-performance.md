---
title: Azure HDInsight での Apache Hive LLAP クエリのパフォーマンスの低下
description: Azure HDInsight で、Apache Hive LLAP でのクエリの実行速度が予想より遅くなっています。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895135"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>シナリオ:Azure HDInsight での Apache Hive LLAP クエリのパフォーマンスの低下

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

既定のクラスター構成が、ワークロードに対して十分にチューニングされていません。 Hive LLAP でのクエリの実行速度が予想より遅くなっています。

## <a name="cause"></a>原因

これは、さまざまな理由で発生する可能性があります。

## <a name="resolution"></a>解決策

LLAP は、結合と集計を含むクエリに対して最適化されています。 対話型 Hive クラスターでは、次のようなクエリはうまく動作しません。

```
select * from table where column = "columnvalue"
```

Hive LLAP でのポイント クエリのパフォーマンスを向上させるには、次の構成を設定します。

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

次のように構成を変更し、LLAP キャッシュの使用率を上げてパフォーマンスを向上させることもできます。

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
