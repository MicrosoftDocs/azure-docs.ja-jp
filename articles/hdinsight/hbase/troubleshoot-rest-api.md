---
title: Azure HDInsight で Apache HBase にクエリを実行するための REST API
description: この記事では、Azure HDInsight クラスターで Apache HBase コンポーネントを操作する際のトラブルシューティングの手順について説明します。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 636f84c4c6aa097288dc2fb5481dcedd6863409d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942872"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>Azure HDInsight で Apache HBase にクエリを実行するための REST API

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache HBase REST インターフェイスを使用して、既定以外の名前空間にあるテーブルに対してクエリを実行すると、ランタイム エラーが発生します (HTTP ステータス 500)。

## <a name="cause"></a>原因

HBase REST API は、既定の名前空間を使用する場合にのみサポートされます。 これは、HBase 名前空間の使用に関する、または HDInsight 上の REST サーバーを含む列ファミリを持つ列に対する特定の GET を参照する呼び出しに関する既知の問題です。 この問題は、HDInsight ゲートウェイのセキュリティ問題が原因で発生します。 API を使用して名前空間を持つテーブルを作成するときに、列ファミリを介して列にアクセスするには、`:` 文字を指定する必要があります。これは、IIS ゲートウェイ モジュールでのセキュリティ問題と見なされます。

## <a name="mitigation"></a>対応策

HDInsight クラスターと同じ Azure VNet に配置されている VM にアプリケーションをデプロイすることにより、ゲートウェイまたは REST サーバーをバイパスします。 次に、RPC を介して直接 (REST サーバーを完全にバイパスして)、または HDInsight ゲートウェイをバイパスしてワーカー ノードで実行されている個々の REST サーバーを介して、HBase と通信できます。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。