---
title: "ロジック アプリに Yammer コネクタを追加する | Microsoft Docs"
description: "Yammer コネクタと REST API パラメーターの概要"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: c69f7af9f9427eb295481657eaefcdad76b48ccb
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-yammer-connector"></a>Yammer コネクタの使用
Yammer に接続し、エンタープライズ ネットワークの会話にアクセスします。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
> 
> 

Yammer で次のことができます。

* Yammer から取得したデータに基づいてビジネス フローを構築します。 
* グループまたはフォローしているフィードで新しいメッセージが発生したときにトリガーを使用します。
* アクションを使用し、メッセージを投稿したり、すべてのメッセージを取得したりします。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、新しいメッセージが表示されたら、Office 365 を使用して電子メールを送信できます。

ロジック アプリに操作を追加する方法については、「 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)」を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
Yammer には、次のトリガーとアクションが含まれています。 

| トリガー | アクション |
| --- | --- |
| <ul><li>グループに新しいメッセージがあるとき</li><li>フォローしているフィードに新しいメッセージがあるとき</li></ul> |<ul><li>すべてのメッセージを取得する</li><li>グループのメッセージを取得する</li><li>フォローしているフィードからメッセージを取得する</li><li>メッセージの投稿</li><li>グループに新しいメッセージがあるとき</li><li>フォローしているフィードに新しいメッセージがあるとき</li></ul> |

すべてのコネクタは、JSON および XML 形式のデータに対応します。 

## <a name="create-a-connection-to-yammer"></a>Yammer への接続を作成する
Yammer コネクタを使用するには、最初に**接続**を作成し、以下のプロパティの詳細を指定します。 

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |あり |Yammer 資格情報を入力します |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 
> [!TIP]
> 他のロジック アプリでもこの接続を使用できます。
> 
> 

## <a name="yammer-rest-api-reference"></a>Yammer REST API リファレンス
このドキュメントの対象バージョン: 1.0

### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>ログイン ユーザーの Yammer ネットワークのすべてのパブリック メッセージを取得します。
Yammer Web インターフェイスの "すべて" の会話に対応します。  
```GET: /messages.json```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| older_then |integer |× |query |なし |数値文字列として指定されたメッセージ ID よりも古いメッセージを返します。 これはメッセージのページネーションに便利です。 たとえば、20 件のメッセージを表示しているとき、最も古いメッセージが 2912 番の場合、「?older_than=2912」を要求に追加すると、現在表示されているメッセージより前の 20 件が取得されます。 |
| newer_then |integer |× |query |なし |数値文字列として指定されたメッセージ ID よりも新しいメッセージを返します。 新しいメッセージのポーリング時にこれを使用します。 メッセージを表示しているとき、返された最も新しいメッセージが 3516 番の場合、要求に「?newer_than=3516」というパラメーターを指定すると、ページに既に表示されているメッセージが重複して取得されることがありません。 |
| limit |integer |× |query |なし |指定した数のメッセージのみを返します。 |
| page |integer |× |query |なし |指定されたページを取得します。 返されたデータが上限を超える場合、このフィールドを使用し、後続のページにアクセスできます。 |

### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 408 |要求タイムアウト |
| 429 |要求が多すぎます |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| 503 |Yammer サービス利用不可 |
| 504 |ゲートウェイ タイムアウト |
| default |操作に失敗しました。 |

### <a name="post-a-message-to-a-group-or-all-company-feed"></a>グループまたはすべての会社フィードにメッセージを投稿する
グループ ID が指定されている場合、メッセージは指定されたグループに投稿されます。指定されていない場合、すべての会社フィードに投稿されます。    
```POST: /messages.json``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| input | |あり |body |なし |メッセージの投稿要求 |

### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 201 |作成日時 |

## <a name="object-definitions"></a>オブジェクト定義
#### <a name="message-yammer-message"></a>メッセージ: Yammer メッセージ
| 名前 | データ型 | 必須 |
| --- | --- | --- |
| id |integer |× |
| content_excerpt |string |× |
| sender_id |integer |× |
| replied_to_id |integer |× |
| created_at |string |× |
| network_id |integer |× |
| message_type |string |× |
| sender_type |string |× |
| url |string |× |
| web_url |string |× |
| group_id |integer |× |
| body |未定義 |× |
| thread_id |integer |× |
| direct_message |boolean |× |
| client_type |string |× |
| client_url |string |× |
| 言語 |string |× |
| notified_user_ids |array |× |
| privacy |string |× |
| liked_by |未定義 |× |
| system_message |boolean |× |

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest: Yammer に投稿する Yammer コネクタの投稿要求を表す
| 名前 | データ型 | 必須 |
| --- | --- | --- |
| body |string |○ |
| group_id |integer |× |
| replied_to_id |integer |× |
| direct_to_id |integer |× |
| broadcast |ブール値 |× |
| topic1 |string |× |
| topic2 |string |× |
| topic3 |string |× |
| topic4 |string |× |
| topic5 |string |× |
| topic6 |string |× |
| topic7 |string |× |
| topic8 |string |× |
| topic9 |string |× |
| topic10 |string |× |
| topic11 |string |× |
| topic12 |string |× |
| topic13 |string |× |
| topic14 |string |× |
| topic15 |string |× |
| topic16 |string |× |
| topic17 |string |× |
| topic18 |string |× |
| topic19 |string |× |
| topic20 |string |× |

#### <a name="messagelist-list-of-messages"></a>MessageList: メッセージの一覧
| 名前 | データ型 | 必須 |
| --- | --- | --- |
| messages |array |× |

#### <a name="messagebody-message-body"></a>MessageBody: メッセージの本文
| 名前 | データ型 | 必須 |
| --- | --- | --- |
| parsed |string |× |
| plain |string |× |
| rich |string |× |

#### <a name="likedby-liked-by"></a>LikedBy: いいねを付けたユーザー
| 名前 | データ型 | 必須 |
| --- | --- | --- |
| count |integer |× |
| names |array |× |

#### <a name="yammmerentity-liked-by"></a>YammmerEntity: いいねを付けたユーザー
| 名前 | データ型 | 必須 |
| --- | --- | --- |
| type |string |× |
| id |integer |× |
| full_name |string |× |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png

