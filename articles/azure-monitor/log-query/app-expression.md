---
title: Azure Monitor ログ クエリの app() 式 | Microsoft Docs
description: app 式は、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定の Application Insights アプリからデータを取得するために、Azure Monitor ログ クエリで使用します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/25/2019
ms.author: bwren
ms.openlocfilehash: a1a605bc733597430f64dceeb6c485db0abf657b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889456"
---
# <a name="app-expression-in-azure-monitor-query"></a>Azure Monitor クエリでの app() 式

`app` 式は、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定の Application Insights アプリからデータを取得するために、Azure Monitor クエリで使用します。 これは、Azure Monitor ログ クエリにアプリケーション データを含めたり、Application Insights のクエリで複数のアプリケーションにわたるデータをクエリしたりする場合に便利です。



## <a name="syntax"></a>構文

`app(`*識別子*`)`


## <a name="arguments"></a>引数

- *識別子*: 次の表のいずれかの形式を使用してアプリを識別します。

| ID | 説明 | 例
|:---|:---|:---|
| リソース名 | 人間が判読できるアプリの名前 (別名 "コンポーネント名") | app("fabrikamapp") |
| 修飾名 | "subscriptionName/resourceGroup/componentName" の形式のアプリのフルネーム | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ID | アプリの GUID | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| Azure リソース ID | Azure リソースの識別子 |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>メモ

* アプリケーションへの読み取りアクセスが必要です。
* アプリケーションをその名前で識別することで、アクセス可能なすべてのサブスクリプション全体で一意であるとみなされます。 指定した名前を持つ複数のアプリケーションが存在する場合は、あいまいさのためにクエリが失敗します。 この場合、その他の識別子のいずれかを使用する必要があります。
* 関連する[ワークスペース](workspace-expression.md)の式を使用して、Log Analytics ワークスペースにわたってクエリを実行します。
* Application Insights アプリケーションがアラート ルールのリソースとして使用されていない限り、Azure portal を使用して[カスタム ログ検索アラート ルール](../platform/alerts-log.md)を作成する場合、現在のところ検索クエリには app() 式を使用できません。

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

## <a name="next-steps"></a>次の手順

- [ワークスペースの式](workspace-expression.md)に関するページを見て、Log Analytics ワークスペースについて参照します。
- [Azure Monitor データ](../../azure-monitor/log-query/log-query-overview.md)の格納方法を確認します。
- [Kusto クエリ言語](/azure/kusto/query/)の完全なドキュメントにアクセスします。
