---
title: "Logic Apps での Salesforce コネクタの使用方法 | Microsoft Docs"
description: "Azure App Service を使用してロジック アプリを作成します。 Salesforce コネクタは、Salesforce オブジェクトを操作するための API を提供します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: b6758aa36120c9c187e91ee5d9e7ceb5041eae6a


---
# <a name="get-started-with-the-salesforce-connector"></a>Salesforce コネクタの使用
Salesforce コネクタは、Salesforce オブジェクトを操作するための API を提供します。

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)から始めることができます。

## <a name="connect-to-salesforce-connector"></a>Salesforce コネクタに接続する
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 [接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。  

### <a name="create-a-connection-to-salesforce-connector"></a>Salesforce コネクタへの接続を作成する
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>Salesforce コネクタ トリガーを使用する
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>条件を追加する
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>Salesforce コネクタ アクションを使用する
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="technical-details"></a>技術的な詳細
ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

## <a name="salesforce-connector-triggers"></a>Salesforce コネクタ トリガー
Salesforce コネクタには、次のトリガーがあります。  

| トリガー | 説明 |
| --- | --- |
| [オブジェクトの作成時](connectors-create-api-salesforce.md#when-an-object-is-created) |この操作では、オブジェクトが作成されたときにフローをトリガーします。 |
| [オブジェクトの変更時](connectors-create-api-salesforce.md#when-an-object-is-modified) |この操作では、オブジェクトが変更されたときにフローをトリガーします。 |

## <a name="salesforce-connector-actions"></a>Salesforce コネクタ アクション
Salesforce コネクタには、次のアクションがあります。

| [操作] | 説明 |
| --- | --- |
| [オブジェクトの取得](connectors-create-api-salesforce.md#get-objects) |この操作では、"潜在顧客" のような特定の種類のオブジェクトを取得します。 |
| [オブジェクトの作成](connectors-create-api-salesforce.md#create-object) |この操作では、オブジェクトを作成します。 |
| [オブジェクトの取得](connectors-create-api-salesforce.md#get-object) |この操作では、オブジェクトを取得します。 |
| [オブジェクトの削除](connectors-create-api-salesforce.md#delete-object) |この操作では、オブジェクトを削除します。 |
| [オブジェクトの更新](connectors-create-api-salesforce.md#update-object) |この操作では、オブジェクトを更新します。 |
| [オブジェクトの種類の取得](connectors-create-api-salesforce.md#get-object-types) |この操作では、使用可能なオブジェクトの種類を一覧表示します。 |

### <a name="action-details"></a>アクションの詳細
ここでは、このコネクタのアクションとトリガー、および応答について詳しく説明します。

### <a name="get-objects"></a>複数のオブジェクトを取得する
この操作では、"潜在顧客" のような特定の種類のオブジェクトを取得します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |オブジェクトの種類 |Salesforce オブジェクトの種類 (例: "潜在顧客") |
| $filter |Filter Query (フィルター クエリ) |エントリ数を制限する ODATA filter クエリ |
| $orderby |Order By (並べ替え) |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |Skip Count (スキップ数) |スキップするエントリの数 (既定値 = 0) |
| $top |Maximum Get Count (最大取得数) |取得するエントリの最大数 (既定値 = 256) |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
ItemsList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

### <a name="create-object"></a>オブジェクトを作成する
この操作では、オブジェクトを作成します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |オブジェクトの種類 |オブジェクトの種類 (例: "潜在顧客") |
| item* |オブジェクト |作成するオブジェクト |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
項目

| プロパティ名 | データ型 |
| --- | --- |
| ItemInternalId |string |

### <a name="get-object"></a>オブジェクトを取得する
この操作では、オブジェクトを取得します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |オブジェクトの種類 |Salesforce オブジェクトの種類 (例: "潜在顧客") |
| id* |オブジェクト ID |取得するオブジェクトの識別子 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
項目

| プロパティ名 | データ型 |
| --- | --- |
| ItemInternalId |string |

### <a name="delete-object"></a>オブジェクトを削除する
この操作では、オブジェクトを削除します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |オブジェクトの種類 |オブジェクトの種類 (例: "潜在顧客") |
| id* |オブジェクト ID |削除するオブジェクトの識別子 |

* は、必須のプロパティを示します。

### <a name="update-object"></a>オブジェクトを更新する
この操作では、オブジェクトを更新します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |オブジェクトの種類 |オブジェクトの種類 (例: "潜在顧客") |
| id* |オブジェクト ID |更新するオブジェクトの識別子 |
| item* |オブジェクト |変更されたプロパティがあるオブジェクト |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
項目

| プロパティ名 | データ型 |
| --- | --- |
| ItemInternalId |string |

### <a name="when-an-object-is-created"></a>オブジェクトの作成時
この操作では、オブジェクトが作成されたときにフローをトリガーします。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |オブジェクトの種類 |オブジェクトの種類 (例: "潜在顧客") |
| $filter |Filter Query (フィルター クエリ) |エントリ数を制限する ODATA filter クエリ |
| $orderby |Order By (並べ替え) |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |Skip Count (スキップ数) |スキップするエントリの数 (既定値 = 0) |
| $top |Maximum Get Count (最大取得数) |取得するエントリの最大数 (既定値 = 256) |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
ItemsList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

### <a name="when-an-object-is-modified"></a>オブジェクトの変更時
この操作では、オブジェクトが変更されたときにフローをトリガーします。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |オブジェクトの種類 |オブジェクトの種類 (例: "潜在顧客") |
| $filter |Filter Query (フィルター クエリ) |エントリ数を制限する ODATA filter クエリ |
| $orderby |Order By (並べ替え) |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |Skip Count (スキップ数) |スキップするエントリの数 (既定値 = 0) |
| $top |Maximum Get Count (最大取得数) |取得するエントリの最大数 (既定値 = 256) |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
ItemsList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

### <a name="get-object-types"></a>オブジェクトの種類を取得する
この操作では、使用可能なオブジェクトの種類を一覧表示します。 

この呼び出しには、パラメーターはありません

#### <a name="output-details"></a>出力の詳細
TablesList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

## <a name="http-responses"></a>HTTP 応答
上記のアクションとトリガーは、次の HTTP 状態コードを&1; つ以上返す場合があります。 

| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました。 |
| default |操作に失敗しました。 |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


