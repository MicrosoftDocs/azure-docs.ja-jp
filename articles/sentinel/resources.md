---
title: Azure Sentinel の使用時に便利なリソース| Microsoft Docs
description: このドキュメントでは、Azure Sentinel の使用時に便利なリソースの一覧を示します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "77585273"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Azure Sentinel の操作に便利なリソース



この記事には、Azure Sentinel の操作の詳細を入手するのに役立つリソースをまとめています。

Azure Logic Apps コネクタ: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>監査とレポート
Azure Sentinel の監査ログは、[Azure アクティビティ ログ](../azure-monitor/platform/platform-logs-overview.md)に保持されます。

次のサポートされている操作を監査できます。

|操作の名前|    リソースの種類|
|----|----|
|ブックの作成または更新  |Microsoft.Insights/workbooks|
|ブックの削除    |Microsoft.Insights/workbooks|
|ワークフローの設定   |Microsoft.Logic/workflows|
|ワークフローの削除    |Microsoft.Logic/workflows|
|保存した検索条件を作成する    |Microsoft.OperationalInsights/workspaces/savedSearches|
|保存した検索条件の削除    |Microsoft.OperationalInsights/workspaces/savedSearches|
|警告ルールの更新 |Microsoft.SecurityInsights/alertRules|
|警告ルールの削除 |Microsoft.SecurityInsights/alertRules|
|警告ルールの応答アクションの更新 |Microsoft.SecurityInsights/alertRules/actions|
|警告ルールの応答アクションの削除 |Microsoft.SecurityInsights/alertRules/actions|
|ブックマークの更新   |Microsoft.SecurityInsights/bookmarks|
|ブックマークの削除   |Microsoft.SecurityInsights/bookmarks|
|ケースの更新   |Microsoft.SecurityInsights/Cases|
|ケース調査の更新  |Microsoft.SecurityInsights/Cases/investigations|
|ケース コメントの作成   |Microsoft.SecurityInsights/Cases/comments|
|データ コネクタの更新 |Microsoft.SecurityInsights/dataConnectors|
|データ コネクタの削除 |Microsoft.SecurityInsights/dataConnectors|
|設定の更新    |Microsoft.SecurityInsights/settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Azure Sentinel で監査とレポート データを表示する

このデータを表示するには、Azure アクティビティ ログから Azure Sentinel にそれをストリーミングし、そこで調査と分析を実行します。

1. [Azure アクティビティ](connect-azure-activity.md) データ ソースに接続します。 この操作を実行すると、監査イベントは、AzureActivity という名前の**ログ**画面で新しいテーブルにストリームされます。
2. 次に、他のテーブルの場合と同様に、KQL を使用してデータに対してクエリを実行します。



## <a name="vendor-documentation"></a>ベンダーのドキュメント

| **ベンダー名**  | **Azure Sentinel でインシデントを使用する** | **リンク**|
|----|----|----|
| GitHub| コミュニティ ページにアクセスするために使用| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| CEF を構成| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kusto クエリ言語コース| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>ブログとフォーラム

ご質問は、Azure Sentinel の [TechCommunity スペース](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)に投稿してください。

[TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) と [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/) から Azure Sentinel のブログ投稿を表示します。


## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Sentinel の使用時に役立つリソースの一覧を示します。 Azure のセキュリティとコンプライアンスに関する追加情報については、[Microsoft Azure Security とコンプライアンスに関するブログ](https://blogs.msdn.com/b/azuresecurity/)を参照してください。
