---
title: InvalidNetworkSecurityGroupSecurityRules - Azure HDInsight でクラスターが失敗する
description: ErrorCode InvalidNetworkSecurityGroupSecurityRules でクラスターの作成が失敗する
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 7df978ad059cfe4ccd233400510b779b16472844
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087388"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>シナリオ: InvalidNetworkSecurityGroupSecurityRules - Azure HDInsight でクラスターの作成が失敗する

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

エラー コード `InvalidNetworkSecurityGroupSecurityRules` を受け取り、"サブネットで構成されたネットワーク セキュリティ グループのセキュリティ規則で、必要な受信/送信接続が許可されない。" のような説明が表示されます。

## <a name="cause"></a>原因

クラスターに対して構成された受信[ネットワーク セキュリティ グループ](../../virtual-network/virtual-network-vnet-plan-design-arm.md)規則に問題がある可能性があります。

## <a name="resolution"></a>解決策

Azure portal に移動し、クラスターがデプロイされているサブネットに関連付けられている NSG を特定します。 **受信セキュリティ規則**セクションで、[ここ](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)に記述されている IP アドレスについてポート 443 への受信アクセスがルールによって許可されていることを確認します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
