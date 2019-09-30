---
title: Azure HDInsight 内での Apache Ambari Tez ビューの読み込みに時間がかかる
description: Azure HDInsight で Apache Ambari Tez ビューの読み込みに時間がかかる場合や、まったく読み込まれない場合があります
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 218f1b66152354cbf7f23b48e0ebf01be62e5550
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091246"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>シナリオ: Azure HDInsight 内での Apache Ambari Tez ビューの読み込みに時間がかかる

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Ambari Tez ビューの読み込みに時間がかかる場合や、まったく読み込まれない場合があります。 Ambari Tez ビューを読み込むときに、応答しなくなっているプロセスがヘッドノードに表示されることがあります。

## <a name="cause"></a>原因

Yarn ATS API にアクセスすると、クラスター上で多数の Hive ジョブが実行されている場合に、2017 年 10 月より前に作成されたクラスター上でパフォーマンスが低下することがあります。

## <a name="resolution"></a>解決策

これは、2017 年 10 月に修正された問題です。 クラスターを再作成すると、この問題が再び発生することはなくなります。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
