---
title: Azure HDInsight でレジューサーが遅い
description: レジューサーは Azure HDInsight のデータ スキューが原因で遅くなる可能性があります
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5fe1b2c720bbc55e8e245f9592755e046e0b04a3
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968378"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>シナリオ: Azure HDInsight でレジューサーが遅い

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

`insert into table1 partition(a,b) select a,b,c from table2` などのクエリを実行すると、クエリ プランによって一連のレジューサーが開始されますが、各パーティションのデータは 1 つのレジューサーに送信されます。 その結果、最も大きなパーティションのレジューサーの場合と同じくらいにクエリが遅くなります。

## <a name="cause"></a>原因

[beeline](../hadoop/apache-hadoop-use-hive-beeline.md) を開き、set `hive.optimize.sort.dynamic.partition` の値を確認します。

この変数の値は、データの性質に基づいて true または false に設定されます。

入力テーブル内のパーティション数が少なく (たとえば 10 未満)、出力パーティション数も同様であり、変数が `true` に設定されている場合は、パーティションごとに 1 つのレジューサーを使用してデータの並べ替えと書き込みがグローバルに行われます。 使用できるレジューサー数が多い場合でも、データ スキューによっていくつかのレジューサーに遅延が発生し、並列処理の最大数が達成されない可能性があります。 `false` に変更すると、複数のレジューサーで 1 つのパーティションを処理できるようになり、複数の小さいファイルが書き出されるので、挿入が高速になります。 この場合、小さなファイルが存在するため、クエリへの影響がさらに大きくなる可能性があります。

`true` の値は、パーティション数が多く、データ スキューが発生していない場合に意味があります。 このような場合、1 つのレジューサーで各パーティションが処理され、後続のクエリのパフォーマンスが向上するように、マップ フェーズの結果が書き出されます。

## <a name="resolution"></a>解決策

1. 複数のパーティションに正規化するようにデータのパーティションを再分割してみます。

1. 1 の手順を実行できない場合は、beeline セッションで config の値を false に設定してから、クエリを再試行します。 `set hive.optimize.sort.dynamic.partition=false` クラスター レベルで値を false に設定することは推奨されません。 `true` の値が最適なので、データとクエリの性質に基づいて必要に応じてこのパラメーターを設定します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる - Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上する Microsoft Azure の公式アカウント。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)に関するページをご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
