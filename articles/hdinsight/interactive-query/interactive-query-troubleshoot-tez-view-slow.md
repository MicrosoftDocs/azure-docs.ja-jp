---
title: Azure HDInsight 内での Apache Ambari Tez ビューの読み込みに時間がかかる
description: Apache Ambari Tez ビューの読み込みに時間がかかる場合や、まったく読み込まれない場合がある
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 70e8ef1f36de30d659e09a15aa935f7a5b7693d6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841922"
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

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる - Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上する Microsoft Azure の公式アカウント。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、[Azure サポート リクエストを作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)に関するページをご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
