---
title: RSS | Microsoft Docs
description: "Azure App Service を使用してロジック アプリを作成します。 RSS コネクタを使用して、フィード アイテムを発行および取得できます。 また、新しいアイテムがフィードに発行されたときに操作をトリガーすることもできます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 5e13e126fecda66a453b4ced619016121af98b2c
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-rss-connector"></a>RSS コネクタの使用
RSS は、ブログ記事やニュースのヘッドラインのように、頻繁に更新されるコンテンツを発行するために使用する一般的な Web 配信形式です。  多くのコンテンツ発行元は、ユーザーがサブスクライブすることができる RSS フィードを提供します。  新しい項目を RSS フィードに発行するときに、RSS コネクタを使用してフィード情報を取得しフローをトリガーします。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。 
> 
> 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
RSS コネクタは、アクションとして使用できます。RSS コネクタにはトリガーがあります。 すべてのコネクタは、JSON および XML 形式のデータに対応します。 

 RSS コネクタでは、次のアクションやトリガーを使用できます。

### <a name="rss-actions"></a>RSS のアクション
実行できるアクションは以下のとおりです。

| アクション | 説明 |
| --- | --- |
| [ListFeedItems](connectors-create-api-rss.md#listfeeditems) |すべての RSS フィード アイテムを取得します。 |

### <a name="rss-triggers"></a>RSS のトリガー
次のイベントをリッスンできます。

| トリガー | Description |
| --- | --- |
| 新しいフィード アイテムが公開されたとき |新しいフィードが公開されたときに、ワークフローをトリガーします。 |

## <a name="create-a-connection-to-rss"></a>RSS への接続の作成
> [!INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]
> 
> [!TIP]
> 他のロジック アプリでもこの接続を使用できます。
> 
> 

## <a name="reference-for-rss"></a>RSS のリファレンス
適用されるバージョン: 1.0

## <a name="onnewfeed"></a>OnNewFeed
新しいフィード アイテムが公開されたとき: 新しいフィードが公開されたときにワークフローをトリガーします。 

```GET: /OnNewFeed``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| feedUrl |string |○ |query |なし |フィード URL |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="listfeeditems"></a>ListFeedItems
すべての RSS フィード アイテムの表示: すべての RSS フィード アイテムを取得します。 

```GET: /ListFeedItems``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| feedUrl |string |○ |query |なし |フィード URL |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse[FeedItem]
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |なし |

### <a name="feeditem"></a>FeedItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |はい |
| title |string |はい |
| content |string |はい |
| links |array |なし |
| updatedOn |string |なし |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)


