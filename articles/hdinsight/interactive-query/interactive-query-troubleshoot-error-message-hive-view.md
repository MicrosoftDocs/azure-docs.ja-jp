---
title: Azure HDInsight の Apache Hive ビューにクエリのエラー メッセージが表示されない
description: Hive ビューの詳細情報のないクエリ エラー
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: e87bb42b09135b61dd4cf32c9ae30b0113d800dc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828827"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>シナリオ: Azure HDInsight の Apache Hive ビューにクエリのエラー メッセージが表示されない

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive ビューのクエリのエラー メッセージが次のように表示され、詳細情報がありません。

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>原因

クエリ エラーのエラー メッセージが大きすぎて、Hive ビューのメイン ページに表示できないことがあります。

## <a name="resolution"></a>解決策

Hive_view の右上にある [通知] タブで、完全なスタックトレースとエラー メッセージを確認します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる - Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上する Microsoft Azure の公式アカウント。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、[Azure のサポート リクエストを作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)に関するページを参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
