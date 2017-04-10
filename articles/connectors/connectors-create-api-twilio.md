---
title: "ロジック アプリに Twilio コネクタを追加する | Microsoft Docs"
description: "Twilio コネクタと REST API パラメーターの概要"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 6770aa8387fe3d381fd9c566ca0c38c88a4b1e55
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-twilio-connector"></a>Twilio コネクタの使用
Twilio に接続し、グローバル SMS、MMS、IP メッセージを送受信します。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
> 
> 

Twilio では次のことができます。

* Twilio から取得したデータに基づいてビジネス フローを構築します。 
* メッセージを取得したり、メッセージを一覧表示したりするアクションを使用します。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、新しい Twilio メッセージを取得したとき、そのメッセージを Service Bus ワークフローで利用できます。 

ロジック アプリに操作を追加する方法については、「 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)」を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
Twilio コネクタには、次のアクションがあります。 トリガーはありません。 

| トリガー | アクション |
| --- | --- |
| なし |<ul><li>メッセージを取得する</li><li>メッセージを一覧表示する</li><li>メッセージを送信する</li></ul> |

すべてのコネクタは、JSON および XML 形式のデータに対応します。 

## <a name="create-a-connection-to-twilio"></a>Twilio への接続を作成する
このコネクタをロジック アプリに追加するときに、次の Twilio 値を入力します。

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| Account ID |あり |Twilio アカウント ID を入力します。 |
| Access Token |あり |Twilio アカウント トークンを入力します。 |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

アクセス トークンがない場合は、[Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) を参照してアクセス トークンを作成してください。

> [!TIP]
> 他のロジック アプリでこの同じ Twilio 接続を使用できます。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API リファレンス
#### <a name="this-documentation-is-for-version-10"></a>このドキュメントの対象バージョン: 1.0
### <a name="get-message"></a>メッセージを取得する
入力したメッセージ ID で指定された&1; つのメッセージを返します。  
```GET: /Messages/{MessageId}.json```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| MessageId |string |あり |path |なし |メッセージ ID |

### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |正しくない要求 |
| 404 |メッセージが見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

### <a name="list-messages"></a>メッセージを一覧表示する
自分のアカウントに関連付けられているメッセージの一覧を返します。  
```GET: /Messages.json```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| To |string |× |query |なし |宛先の電話番号 |
| ファイル |string |× |query |なし |送信元の電話番号 |
| DateSent |string |× |query |なし |この YYYY-MM-DD 日付 (GMT 形式) で送信されたメッセージのみを表示します。 例: DateSent=2009-07-06。 不等号も指定できます。たとえば、ある日付の真夜中以前に送信されたメッセージの場合は「DateSent<=YYYY-MM-DD」と、ある日付の真夜中以後に送信されたメッセージの場合は「DateSent>=YYYY-MM-DD」と指定できます。 |
| PageSize |integer |× |query |50 |各リスト ページで返されるリソースの数。 既定は 50 です。 |
| ページ |integer |× |query |0 |ページ番号。 既定値は 0 です。 |

### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

### <a name="send-message"></a>メッセージを送信する
携帯電話番号に新しいメッセージを送信します。  
```POST: /Messages.json```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| sendMessageRequest | |あり |body |なし |送信するメッセージ |

### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
#### <a name="sendmessagerequest-request-model-for-send-message-operation"></a>SendMessageRequest: メッセージ送信操作の要求モデル
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ファイル |string |あり |
| To |string |あり |
| body |string |○ |
| media_url |array |× |
| status_callback |string |× |
| messaging_service_sid |string |× |
| application_sid |string |× |
| max_price |string |× |

#### <a name="message-model-for-message"></a>Message: メッセージのモデル
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| body |string |× |
| ファイル |string |× |
| To |string |× |
| status |string |× |
| sid |string |× |
| account_sid |string |× |
| api_version |string |× |
| num_segments |string |× |
| num_media |string |× |
| date_created |string |× |
| date_sent |string |× |
| date_updated |string |× |
| direction |string |× |
| error_code |string |× |
| error_message |string |× |
| price |string |× |
| price_unit |string |× |
| uri |string |× |
| subresource_uris |array |× |
| messaging_service_sid |string |× |

#### <a name="messagelist-response-model-for-list-messages-operation"></a>MessageList: メッセージの一覧表示操作の応答モデル
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| messages |array |× |
| ページ |integer |× |
| page_size |integer |× |
| num_pages |integer |× |
| uri |string |× |
| first_page_uri |string |× |
| next_page_uri |string |× |
| total |integer |× |
| previous_page_uri |string |× |

#### <a name="incomingphonenumberlist-response-model-for-list-messages-operation"></a>IncomingPhoneNumberList: メッセージの一覧表示操作の応答モデル
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| incoming_phone_numbers |array |× |
| ページ |integer |× |
| page_size |integer |× |
| num_pages |integer |× |
| uri |string |× |
| first_page_uri |string |× |
| next_page_uri |string |× |

#### <a name="addincomingphonenumberrequest-request-model-for-add-incoming-number-operation"></a>AddIncomingPhoneNumberRequest: 着信番号追加操作の要求モデル
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| PhoneNumber |string |あり |
| AreaCode |string |× |
| FriendlyName |string |× |

#### <a name="incomingphonenumber-incoming-phone-number"></a>IncomingPhoneNumber: 着信電話番号
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| phone_number |string |× |
| friendly_name |string |× |
| sid |string |× |
| account_sid |string |× |
| date_created |string |× |
| date_updated |string |× |
| capabilities |未定義 |× |
| status_callback |string |× |
| status_callback_method |string |× |
| api_version |string |× |

#### <a name="capabilities-phone-number-capabilities"></a>Capabilities: 電話番号機能
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| mms |ブール値 |× |
| sms |ブール値 |× |
| voice |ブール値 |× |

#### <a name="availablephonenumbers-available-phone-numbers"></a>AvailablePhoneNumbers: 利用可能な電話番号
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| phone_number |string |× |
| friendly_name |string |× |
| lata |string |× |
| latitude |string |× |
| longitude |string |× |
| postal_code |string |× |
| rate_center |string |× |
| region |string |× |
| mms |ブール値 |× |
| sms |ブール値 |× |
| voice |ブール値 |× |

#### <a name="usagerecords-usage-records-class"></a>UsageRecords: 使用状況レコード クラス
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| カテゴリ |string |× |
| usage |string |× |
| usage_unit |string |× |
| 説明 |string |× |
| price |number |× |
| price_unit |string |× |
| count |string |× |
| count_unit |string |× |
| start_date |string |× |
| end_date |string |× |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)


