---
title: SendGrid | Microsoft Docs
description: "Azure App Service を使用してロジック アプリを作成します。 SendGrid 接続プロバイダーを使用して、電子メールを送信し、受信者リストを管理できます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: e30231bd576436ae69f4fa42d0e2ab312c3938d6


---
# <a name="get-started-with-the-sendgrid-connector"></a>SendGrid コネクタの使用
SendGrid 接続プロバイダーを使用して、電子メールを送信し、受信者リストを管理できます。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。 
> 
> 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
SendGrid コネクタは、アクションとして使用できます。SendGrid コネクタにはトリガーがあります。 すべてのコネクタは、JSON および XML 形式のデータに対応します。 

 SendGrid コネクタで使用可能なアクションは次のとおりです。 トリガーはありません。

### <a name="sendgrid-actions"></a>SendGrid のアクション
実行できるアクションは以下のとおりです。

| アクション | 説明 |
| --- | --- |
| [SendEmail](connectors-create-api-sendgrid.md#sendemail) |SendGrid API を使用して電子メールを送信します (受信者は 10,000 に制限)。 |
| [AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist) |個々の受信者を受信者リストに追加します。 |

## <a name="create-a-connection-to-sendgrid"></a>SendGrid への接続の作成
SendGrid を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。 

| プロパティ | 必須 | Description |
| --- | --- | --- |
| ApiKey |はい |SendGrid API キーを指定します。 |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 
> [!TIP]
> 他のロジック アプリでもこの接続を使用できます。
> 
> 

接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

## <a name="reference-for-sendgrid"></a>SendGrid のリファレンス
適用されるバージョン: 1.0

## <a name="sendemail"></a>SendEmail
電子メールの送信: SendGrid API を使用して電子メールを送信します (受信者は 10,000 に制限)。 

```POST: /api/mail.send.json``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| request | |○ |body |なし |送信する電子メール メッセージ |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 429 |要求が多すぎます |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="addrecipienttolist"></a>AddRecipientToList
リストへの受信者の追加: 個々の受信者を受信者リストに追加します。 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | Description |
| --- | --- | --- | --- | --- | --- |
| listId |string |あり |path |なし |受信者リストの一意の ID |
| recipientId |string |あり |path |なし |受信者の一意の ID |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
### <a name="emailrequest"></a>EmailRequest
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ファイル |string |はい |
| fromname |string |なし |
| to |string |はい |
| toname |string |なし |
| subject |string |はい |
| body |string |はい |
| ishtml |boolean |なし |
| cc |string |なし |
| ccname |string |なし |
| bcc |string |なし |
| bccname |string |なし |
| replyto |string |なし |
| date |string |なし |
| headers |string |なし |
| ファイルのアップロード |array |なし |
| filenames |array |なし |

### <a name="emailresponse"></a>EmailResponse
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| message |string |なし |

### <a name="recipientlists"></a>RecipientLists
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| lists |array |なし |

### <a name="recipientlist"></a>RecipientList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |integer |なし |
| name |string |なし |
| recipient_count |integer |なし |

### <a name="recipients"></a>Recipients
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| recipients |array |なし |

### <a name="recipient"></a>Recipient
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 電子メール |string |なし |
| last_name |string |なし |
| first_name |string |なし |
| id |string |なし |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


