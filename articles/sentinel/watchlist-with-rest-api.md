---
title: REST API を使用して Azure Sentinel でウォッチリストを管理する | Microsoft Docs
description: この記事では、Log Analytics の REST API を使用して Azure Sentinel のウォッチリストを管理し、ウォッチリストとその項目を作成、変更、および削除する方法について説明します。
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
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98798039"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>REST API を使用して Azure Sentinel でウォッチリストを管理する

> [!IMPORTANT]
> ウォッチリスト機能は現在 **プレビュー** 段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Azure Sentinel は、一部分が Azure Monitor Log Analytics 上に構築されており、Log Analytics の REST API を使用してウォッチリストを管理することができます。 このドキュメントでは、REST API を使用してウォッチリストとその項目を作成、変更、および削除する方法について説明します。  この方法で作成されたウォッチリストは、Azure Sentinel UI に表示されます。

## <a name="common-uri-parameters"></a>共通 URI パラメーター

すべてのウォッチリスト API コマンドの共通 URI パラメーターを次に示します。

| 名前 | / | 必須 | Type | [説明] |
|-|-|-|-|-|
| **{subscriptionId}** | path | yes | GUID | Azure サブスクリプション ID |
| **{resourceGroupName}** | path | yes | string | サブスクリプション内のリソース グループの名前 |
| **{workspaceName}** | path | yes | string | Log Analytics ワークスペースの名前 |
| **{watchlistAlias}** | path | はい* | string | 指定されたウォッチリストの名前<br>\* すべてのウォッチリストを取得するときは不要 |
| **{watchlistItemId}** | path | はい** | GUID | ウォッチリストに作成、追加、またはそれから削除する項目の ID<br>\*\* ウォッチリスト項目コマンドにのみ必須 |
| **{api-version}** | query | yes | string | この要求を行うために使用されるプロトコルのバージョン。 2020 年 10 月 29日の時点で、ウォッチリスト API のバージョンは *2019-01-01-preview* です |
| **{bearerToken}** | authorization | yes | token | ベアラー認証トークン |
|  

## <a name="retrieve-all-watchlists"></a>すべてのウォッチリストを取得する

このコマンドを使用して、ワークスペースに関連付けられているすべてのウォッチリストを、項目を含めずに取得します。

### <a name="request-uri"></a>要求 URI
(URI は単一の行です。読みやすくするために分割しています)

| Method | 要求 URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>応答

| status code | 応答本文 | [説明] |
|-|-|-|
| 200 (OK) | 既存のウォッチリストの一覧。または、ウォッチリストが見つからない場合は空 |  |
| 400 (無効な要求) |  | 要求の構文形式に誤りがある、無効な要求パラメーター... |
|

## <a name="look-up-a-watchlist-by-name"></a>ウォッチリストを名前で検索する

このコマンドを使用して、ワークスペースに関連付けられている特定のウォッチリストを、項目を含めずに取得します。

### <a name="request-uri"></a>要求 URI
(URI は単一の行です。読みやすくするために分割しています)

| Method | 要求 URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>応答

| status code | 応答本文 | [説明] |
|-|-|-|
| 200 (OK) | 要求されたウォッチリスト |  |
| 400 (無効な要求) |  | 要求の構文形式に誤りがある、無効な要求パラメーター... |
| 404 (見つかりません) |  | 要求された名前のウォッチリストは見つかりませんでした |
|

## <a name="create-a-watchlist"></a>ウォッチリストを作成する

このコマンドを使用して、ウォッチリストを作成し、そこに項目を追加します。 ウォッチリストとその項目を作成するために、このエンドポイントに対して 2 回の呼び出しを行います。最初の呼び出しでウォッチリスト (親) を作成し、2 回目で項目を追加します。

### <a name="request-uri"></a>要求 URI
(URI は単一の行です。読みやすくするために分割しています)

| Method | 要求 URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>要求本文

ウォッチリスト作成要求の要求本文の例を次に示します。

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>応答

| status code | 応答本文 | [説明] |
|-|-|-|
| 200 (OK) | 要求によって作成された、項目を含まないウォッチリスト |  |
| 400 (無効な要求) |  | 要求の構文形式に誤りがある、無効な要求パラメーター... |
| 409 (競合) |  | 操作に失敗しました。同じエイリアスを持つ既存のウォッチリストがあります |
|

## <a name="delete-a-watchlist"></a>ウォッチリストを削除する

このコマンドを使用して、ウォッチリストとその項目を削除します。

### <a name="request-uri"></a>要求 URI
(URI は単一の行です。読みやすくするために分割しています)

| Method | 要求 URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>応答

| status code | 応答本文 | [説明] |
|-|-|-|
| 200 (OK) | 空の応答本文 |  |
| 204 (コンテンツなし) | 空の応答本文 | 何も削除されていません |
| 400 (無効な要求) |  | 要求の構文形式に誤りがある、無効な要求パラメーター... |
|

## <a name="add-or-update-a-watchlist-item"></a>ウォッチリスト項目を追加または更新する

このコマンドを既存の *watchlisItemId* (GUID) に対して実行すると、要求本文に指定されたデータで項目が更新されます。 それ以外の場合は、新しい項目が作成されます。

### <a name="request-uri"></a>要求 URI
(URI は単一の行です。読みやすくするために分割しています)

| Method | 要求 URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>要求本文

ウォッチリスト項目の追加または更新要求の要求本文の例を次に示します。

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>応答

| status code | 応答本文 | [説明] |
|-|-|-|
| 200 (OK) | 要求によってウォッチリスト項目が作成または更新されました |  |
| 400 (無効な要求) |  | 要求の構文形式に誤りがある、無効な要求パラメーター... |
| 409 (競合) |  | 操作に失敗しました。同じエイリアスを持つ既存のウォッチリストがあります |
|

## <a name="delete-a-watchlist-item"></a>ウォッチリスト項目を削除する

このコマンドを使用して、既存のウォッチリスト項目を削除します。

### <a name="request-uri"></a>要求 URI
(URI は単一の行です。読みやすくするために分割しています)

| Method | 要求 URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>応答

| status code | 応答本文 | [説明] |
|-|-|-|
| 200 (OK) | 空の応答本文 |  |
| 204 (コンテンツなし) | 空の応答本文 | 何も削除されていません |
| 400 (無効な要求) |  | 要求の構文形式に誤りがある、無効な要求パラメーター... |
|

## <a name="next-steps"></a>次のステップ

この記事では、Log Analytics API を使用して Azure Sentinel のウォッチリストとその項目を管理する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [ウォッチリスト](watchlists.md)の詳細情報
- [Azure Sentinel API](/rest/api/securityinsights/) の他の使用方法を確認する