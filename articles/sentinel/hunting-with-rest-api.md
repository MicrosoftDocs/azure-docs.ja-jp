---
title: REST API を使用して Azure Sentinel でのハンティングとライブストリームのクエリを管理する | Microsoft Docs
description: この記事では、Azure Sentinel のハンティング機能を利用して、Log Analytics の REST API を活用してハンティングとライブストリームのクエリを管理する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: 64f05e18ff757d9f086cf06d74109bf64e32a05c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795696"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>REST API を使用して Azure Sentinel でのハンティングとライブストリームのクエリを管理する

Azure Sentinel は、一部分が Azure Monitor Log Analytics 上に構築されており、Log Analytics の REST API を使用してハンティングとライブストリームのクエリを管理することができます。 このドキュメントでは、REST API を使用して、ハンティング クエリを作成および管理する方法について説明します。  この方法で作成されたクエリは、Azure Sentinel UI に表示されます。

[保存された検索 API](/rest/api/loganalytics/savedsearches) の詳細については、最も信頼できる REST API リファレンスを参照してください。

## <a name="api-examples"></a>API の例

次の例では、これらのプレースホルダーを、次の表に示す置換を使用して置き換えます。

| プレースホルダー | 置換後の文字列 |
|-|-|
| **{subscriptionId}** | ハンティングまたはライブストリームのいずれかのクエリを適用するサブスクリプションの名前。 |
| **{resourceGroupName}** | ハンティングまたはライブストリームのいずれかのクエリを適用するリソース グループの名前。 |
| **{savedSearchId}** | 各ハンティング クエリの一意の ID (GUID)。 |
| **{WorkspaceName}** | クエリの対象となる Log Analytics ワークスペースの名前。 |
| **{DisplayName}** | クエリに対して選択した表示名。 |
| **{Description}** | ハンティングまたはライブストリームのクエリの説明。 |
| **{Tactics}** | クエリに適用される MITRE ATT&CK 関連する戦略。 |
| **{Query}** | クエリのクエリ式。 |
|  

### <a name="example-1"></a>例 1

この例では、特定の Azure Sentinel ワークスペースに対してハンティング クエリを作成または更新する方法を示します。  ライブストリーム クエリの場合は、 *"Category":"Hunting Queries"* を *"Category":"Livestream Queries"* に **要求本文** で置き換えます。 

#### <a name="request-header"></a>要求ヘッダー

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>要求本文

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>例 2

この例では、特定の Azure Sentinel ワークスペースに対してハンティングまたはライブストリームのクエリを削除する方法を示します。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>例 3

この例では、特定のワークスペースに対してハンティングまたはライブストリームのクエリを取得する方法を示します。

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>次のステップ

この記事では、Log Analytics API を使用して Azure Sentinel でハンティングとライブストリームのクエリを管理する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [脅威を事前に検出する](hunting.md)
- [ノートブックを使用して自動化された検出キャンペーンを実行する](notebooks.md)