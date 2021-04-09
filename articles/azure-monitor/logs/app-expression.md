---
title: Azure Monitor ログ クエリの app() 式 | Microsoft Docs
description: app 式は、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定の Application Insights アプリからデータを取得するために、Azure Monitor ログ クエリで使用します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2019
ms.openlocfilehash: 235a6bf4a0dd9afcac8751067b2eac3ddb37c0cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031244"
---
# <a name="app-expression-in-azure-monitor-query"></a>Azure Monitor クエリでの app() 式

`app` 式は、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定の Application Insights アプリからデータを取得するために、Azure Monitor クエリで使用します。 これは、Azure Monitor ログ クエリにアプリケーション データを含めたり、Application Insights のクエリで複数のアプリケーションにわたるデータをクエリしたりする場合に便利です。

> [!IMPORTANT]
> ログ データは Log Analytics ワークスペースに格納されるため、[ワークスペースベースの Application Insights リソース](../app/create-workspace-resource.md)を使用している場合、app () 式は使用されません。 log () 式を使用して、複数のワークスペースにアプリケーションを含むクエリを記述します。 同じワークスペース内に複数のアプリケーションがある場合、クロス ワークスペース クエリは必要ありません。

## <a name="syntax"></a>構文

`app(`*識別子*`)`


## <a name="arguments"></a>引数

- *識別子*:次の表のいずれかの形式を使用してアプリを識別します。

| 識別子 | 説明 | 例
|:---|:---|:---|
| リソース名 | 人間が判読できるアプリの名前 (別名 "コンポーネント名") | app("fabrikamapp") |
| 修飾名 | "subscriptionName/resourceGroup/componentName" の形式のアプリのフルネーム | app('AI-Prototype/Fabrikam/fabrikamapp') |
| id | アプリの GUID | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure リソース ID | Azure リソースの識別子 |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Notes

* アプリケーションへの読み取りアクセスが必要です。
* アプリケーションをその名前で識別することで、アクセス可能なすべてのサブスクリプション全体で一意であるとみなされます。 指定した名前を持つ複数のアプリケーションが存在する場合は、あいまいさのためにクエリが失敗します。 この場合、その他の識別子のいずれかを使用する必要があります。
* 関連する[ワークスペース](../logs/workspace-expression.md)の式を使用して、Log Analytics ワークスペースにわたってクエリを実行します。
* Application Insights アプリケーションがアラート ルールのリソースとして使用されていない限り、Azure portal を使用して[カスタム ログ検索アラート ルール](../alerts/alerts-log.md)を作成する場合、現在のところ検索クエリには app() 式を使用できません。

## <a name="examples"></a>例

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- [ワークスペースの式](../logs/workspace-expression.md)に関するページを見て、Log Analytics ワークスペースについて参照します。
- [Azure Monitor データ](./log-query-overview.md)の格納方法を確認します。
- [Kusto クエリ言語](/azure/kusto/query/)の完全なドキュメントにアクセスします。