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
ms.date: 01/27/2021
ms.author: yelevin
ms.openlocfilehash: 11c5c77623fcc693210d5a42bf94c968e884fec3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920022"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Azure Sentinel の操作に便利なリソース



この記事には、Azure Sentinel の操作の詳細を入手するのに役立つリソースをまとめています。

- **Azure Logic Apps コネクタ**: <https://docs.microsoft.com/connectors/>


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

1. [Azure アクティビティ](connect-azure-activity.md) データ ソースに接続します。 この操作を実行すると、監査イベントは、AzureActivity という名前の **ログ** 画面で新しいテーブルにストリームされます。

1. 次に、他のテーブルの場合と同様に、KQL を使用してデータに対してクエリを実行します。

    たとえば、特定の分析ルールを最後に編集したユーザーを確認するには、次のクエリを使用します (`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` を、確認するルールのルール ID に置き換えます)。

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```



## <a name="vendor-documentation"></a>ベンダーのドキュメント

| **ベンダー名**  | **Azure Sentinel でインシデントを使用する** | **リンク**|
|----|----|----|
| GitHub| コミュニティ ページにアクセスするために使用| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| CEF を構成| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kusto クエリ言語コース| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>ブログとフォーラム

ユーザーの方のご意見をお待ちしています。

- Azure Sentinel の [TechCommunity スペース](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)に **ご質問を投稿** してください。 

- [User Voice](https://feedback.azure.com/forums/920458-azure-sentinel) プログラムを使用して、**改善のためのご提案を送信** してください。

- Azure Sentinel のブログ投稿を **閲覧し、コメントをお寄せください**:

    - [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 
    - [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)

Azure のセキュリティとコンプライアンスの詳細については、[Microsoft Azure のセキュリティとコンプライアンスに関するブログ](https://techcommunity.microsoft.com/t5/microsoft-security-and/bg-p/MicrosoftSecurityandCompliance)を参照してください。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [認定を受ける](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [お客様のユース ケース ストーリーを読む](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)

