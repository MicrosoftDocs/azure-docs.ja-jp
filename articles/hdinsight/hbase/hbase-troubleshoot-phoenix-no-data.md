---
title: HDP をアップグレードすると、Azure HDInsight の Apache Phoenix ビューにデータが表示されない
description: HDP をアップグレードすると、Azure HDInsight の Apache Phoenix ビューにデータが表示されない
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: e42ab97b4508a606781af76f8dd5927b1a14a8b3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947926"
---
# <a name="scenario-hdp-upgrade-causes-no-data-in-apache-phoenix-views-in-azure-hdinsight"></a>シナリオ: HDP をアップグレードすると、Azure HDInsight の Apache Phoenix ビューにデータが表示されない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

HDP 2.4 から HDP 2.5 にアップグレードした後、Apache Phoenix ビューにデータが含まれません。

## <a name="cause"></a>原因

ビューのインデックス テーブル (ビューのすべてのインデックスは 1 つの物理 Apache HBase テーブルに格納される) は、アップグレード中に切り捨てられます

## <a name="resolution"></a>解決策

アップグレード後にビューすべてのインデックスを削除して再作成します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティを適切なリソース (回答、サポート、エキスパートなど) に結び付けます。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
