---
title: Azure HDInsight でのリージョン エラーにおいて十分な障害ドメインがない
description: Azure HDInsight のリージョンに十分な障害ドメインがないため、クラスターの作成に失敗した
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: ed9e98750a469f78855096c3149254cf92c2788f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501347"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>シナリオ: Azure HDInsight での `not sufficient fault domains in region` のために、クラスターの作成に失敗した

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Kafka クラスターを作成しようとしているときに、`not sufficient fault domains in region` のようなエラー メッセージを受け取ります。

## <a name="cause"></a>原因

障害ドメインとは、Azure データ センター内にある基になるハードウェアの論理的なグループです。 各障害ドメインは、一般的な電源とネットワーク スイッチを共有します。 HDInsight クラスター内のノードを実装する仮想マシンと管理ディスクは、これらの障害ドメインに分散されます。 このアーキテクチャにより、物理的なハードウェア障害の潜在的な影響が制限されます。

各 Azure リージョンには、特定の数の障害ドメインがあります。 ドメインと、それに含まれる障害ドメインの数の一覧については、[可用性セット](../../virtual-machines/availability.md)に関するドキュメントを参照してください。

HDInsight では、Kafka クラスターは、3 つ以上の障害ドメインがあるリージョンにプロビジョニングする必要があります。

## <a name="resolution"></a>解決策

クラスターを作成するリージョンに十分な障害ドメインがない場合は、製品チームに連絡して、3 つの障害ドメインがない場合でもクラスターのプロビジョニングを許可するよう依頼してください。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。