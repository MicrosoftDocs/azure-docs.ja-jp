---
title: Azure HDInsight で Apache Tez アプリケーションがハングする
description: Azure HDInsight で Apache Tez アプリケーションがハングする
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: 56c68c26ae953034283031e2427b7a4afadee94e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935934"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>シナリオ: Azure HDInsight で Apache Tez アプリケーションがハングする

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive ジョブを送信した後、Tez ビューでジョブの状態は "実行中" になりますが、進行状況が表示されません。

## <a name="cause"></a>原因

送信されたジョブ数が多すぎます。長い Yarn キューです。

## <a name="resolution"></a>解決方法

クラスターをスケールアップするか、Yarn キューがドレインされるまで待機します。

`yarn.scheduler.capacity.maximum-applications` の既定では、実行中または保留中のアプリケーションの最大数が制御され、既定値は `10000` に設定されます。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。