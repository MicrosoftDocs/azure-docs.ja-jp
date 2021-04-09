---
title: Azure Monitor ログ クエリの workspace() 式 | Microsoft Docs
description: workspace 式は、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定のワークスペースからデータを取得するために、Azure Monitor ログ クエリで使用します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2f6eb3998c611cb7a72886d1c577c665d73cb5a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035570"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>Azure Monitor ログ クエリの workspace() 式

`workspace` 式は、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定のワークスペースからデータを取得するために、Azure Monitor クエリで使用されます。 これは、Application Insights クエリにログ データを含めたり、ログ クエリで複数のワークスペースのデータをクエリしたりする場合に便利です。


## <a name="syntax"></a>構文

`workspace(`*識別子*`)`

## <a name="arguments"></a>引数

- *識別子*: 次の表のいずれかの形式を使用してワークスペースを識別します。

| 識別子 | 説明 | 例
|:---|:---|:---|
| リソース名 | 人間が判読できるワークスペースの名前 (別名 "コンポーネント名") | workspace("contosoretail") |
| 修飾名 | "subscriptionName/resourceGroup/componentName" の形式のワークスペースのフルネーム | workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | ワークスペースの GUID | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Azure リソース ID | Azure リソースの識別子 | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>メモ

* ワークスペースへの読み取りアクセスが必要です。
* 関連する式は、Application Insights のアプリケーション間でクエリすることができる `app` です。

## <a name="examples"></a>例

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>次のステップ

- Application Insights アプリを参照する場合には、[app 式](./app-expression.md)に関するページをご覧ください。
- [Azure Monitor データ](./log-query-overview.md)の格納方法を確認します。
- [Kusto クエリ言語](/azure/kusto/query/)の完全なドキュメントにアクセスします。