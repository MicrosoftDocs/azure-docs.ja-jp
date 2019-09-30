---
title: Azure HDInsight クラスターにログインできない
description: Azure HDInsight で Apache Hadoop クラスターにログインできない理由のトラブルシューティング
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: c4abc8a1b8d81f4ed0ef7e1c3b37c4e0e755c1b8
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087260"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>シナリオ: Azure HDInsight クラスターにログインできない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Azure HDInsight クラスターにログインできません。

## <a name="cause"></a>原因

さまざまな理由が考えられます。 クラスターまたはアプリのダッシュボードにログインする場合は、"クラスター ログイン" または HTTP 資格情報を使用することに注意してください。 リモート接続する場合は、Secure Shell (SSH) またはリモート デスクトップの資格情報を使用します。

## <a name="resolution"></a>解決策

一般的な問題を解決するには、次の手順のうち 1 つ以上を試してください。

* プライバシー モードの新しいブラウザー タブでクラスター ダッシュボードを開いてみます。

* SSH 資格情報を思い出すことができない場合、[Ambari UI 内で資格情報をリセット](../hdinsight-administer-use-portal-linux.md#change-passwords)できます。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
