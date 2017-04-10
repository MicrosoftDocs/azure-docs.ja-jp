---
title: "ロジック アプリに Facebook コネクタを追加する | Microsoft Docs"
description: "Facebook コネクタと REST API パラメーターの概要"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: b8a66308c4f4f1df610cdacd092ef133bd605665
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-facebook-connector"></a>Facebook コネクタの使用
Facebook に接続し、タイムラインへの投稿、ページ フィードの取得などを行います。 

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
> 
> 

Facebook では、次の操作を実行できます。

* Facebook から取得したデータに基づいてビジネス フローを構築できます。 
* 新しい投稿を取得したときにトリガーを使用できます。
* タイムラインへの投稿、ページ フィードの取得などのアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、タイムラインに新しい投稿がある場合、その投稿を取得して、Twitter フィードにプッシュすることができます。 

ロジック アプリに操作を追加する方法については、「 [SaaS サービスを接続する新しいロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)」をご覧ください。

## <a name="triggers-and-actions"></a>トリガーとアクション
Facebook コネクタには、次のトリガーとアクションがあります。 

| トリガー | アクション |
| --- | --- |
| <ul><li>タイムラインに新しい投稿がある場合</li></ul> |<ul><li>タイムラインからフィードを取得する</li><li>タイムラインに投稿する</li><li>タイムラインに新しい投稿がある場合</li><li>ページ フィードを取得する</li><li>ユーザーのタイムラインを取得する</li><li>ページに投稿する</li></ul> |

すべてのコネクタは、JSON および XML 形式のデータに対応します。

## <a name="create-a-connection-to-facebook"></a>Facebook への接続を作成する
ロジック アプリにこのコネクタを追加するとき、Facebook に接続するロジック アプリを承認する必要があります。

1. Facebook アカウントにサインインします。
2. **[Authorize]**を選択し、ロジック アプリが Facebook に接続して使用することを許可します。 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 
> [!TIP]
> 他のロジック アプリでも、この同じ Facebook 接続を使用できます。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API リファレンス
適用されるバージョン: 1.0。

### <a name="get-feed-from-my-timeline"></a>タイムラインからフィードを取得する
ログインしているユーザーのタイムラインからフィードを取得します。  
```GET: /me/feed```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| fields |string |× |query |なし |取得フィールドを指定します。 例 (id、name、picture)。 |
| limit |integer |× |query |なし |取得する投稿の最大数 |
| を以下に置き換えることができます。 |string |× |query |なし |投稿の一覧を、接続している場所の投稿に制限します。 |
| filter |string |× |query |なし |特定のストリーム フィルターに一致する投稿のみを取得します。 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="post-to-my-timeline"></a>タイムラインに投稿する
ログインしているユーザーのタイムラインにステータス メッセージを投稿します。  
```POST: /me/feed```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| post |string |あり |body |なし |投稿する新しいメッセージ |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="when-there-is-a-new-post-on-my-timeline"></a>タイムラインに新しい投稿がある場合
ログインしているユーザーのタイムラインに新しい投稿があるときに新しいフローをトリガーします。  
```GET: /trigger/me/feed```

パラメーターはありません。 

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="get-page-feed"></a>ページ フィードを取得する
指定したページのフィードから投稿を取得します。  
```GET: /{pageId}/feed```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| pageId |string |あり |path |なし |取得する投稿のページ ID。 |
| limit |integer |× |query |なし |取得する投稿の最大数 |
| include_hidden |boolean |× |query |なし |ページで非表示にされていた投稿を含めるかどうか |
| fields |string |× |query |なし |取得フィールドを指定します。 例 (id、name、picture)。 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="get-user-timeline"></a>ユーザーのタイムラインを取得する
ユーザーのタイムラインから投稿を取得します。  
```GET: /{userId}/feed```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| userId |string |あり |path |なし |タイムラインを取得するユーザーの ID。 |
| limit |integer |× |query |なし |取得する投稿の最大数 |
| を以下に置き換えることができます。 |string |× |query |なし |投稿の一覧を、接続している場所の投稿に制限します。 |
| filter |string |× |query |なし |特定のストリーム フィルターに一致する投稿のみを取得します。 |
| fields |string |× |query |なし |取得フィールドを指定します。 例 (id、name、picture)。 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="post-to-page"></a>ページに投稿する
ログインしているユーザーとして Facebook ページにメッセージを投稿します。  
```POST: /{pageId}/feed```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| pageId |string |あり |path |なし |投稿するページの ID。 |
| post |many |はい |body |なし |投稿する新しいメッセージ。 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
#### <a name="getfeedresponse"></a>GetFeedResponse
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| data |array |× |

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| data |array |× |

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem: プロファイルのフィードの単一エントリ
プロファイルには、user、page、app、または group を指定できます。 

| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |× |
| admin_creator |array |× |
| caption |string |× |
| created_time |string |× |
| 説明 |string |× |
| feed_targeting |未定義 |× |
| from |未定義 |× |
| icon |string |× |
| is_hidden |boolean |× |
| is_published |boolean |× |
| link |string |× |
| message |string |× |
| 名前 |string |× |
| object_id |string |× |
| picture |string |× |
| place |未定義 |× |
| privacy |未定義 |× |
| properties |array |× |
| source |string |× |
| status_type |string |× |
| story |string |× |
| targeting |未定義 |× |
| to |array |× |
| type |string |× |
| updated_time |string |× |
| with_tags |未定義 |× |

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem: プロファイルのフィードの単一エントリ
プロファイルには、user、page、app、または group を指定できます。

| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |× |
| created_time |string |× |
| from |未定義 |× |
| message |string |× |
| type |string |× |

#### <a name="adminitem"></a>AdminItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |× |
| link |string |× |

#### <a name="propertyitem"></a>PropertyItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |× |
| text |string |× |
| href |string |× |

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| message |string |○ |
| link |string |× |
| picture |string |× |
| 名前 |string |× |
| caption |string |× |
| 説明 |string |× |
| place |string |× |
| tags |string |× |
| privacy |未定義 |× |
| object_attachment |string |× |

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| message |string |○ |
| link |string |× |
| picture |string |× |
| 名前 |string |× |
| caption |string |× |
| 説明 |string |× |
| アクション |array |× |
| place |string |× |
| tags |string |× |
| object_attachment |string |× |
| targeting |未定義 |× |
| feed_targeting |未定義 |× |
| published |ブール値 |× |
| scheduled_publish_time |string |× |
| backdated_time |string |× |
| backdated_time_granularity |string |× |
| child_attachments |array |× |
| multi_share_end_card |boolean |× |

#### <a name="postfeedresponse"></a>PostFeedResponse
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |× |

#### <a name="profilecollection"></a>ProfileCollection
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| data |array |× |

#### <a name="useritem"></a>UserItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |× |
| first_name |string |× |
| last_name |string |× |
| 名前 |string |× |
| gender |string |× |
| about |string |× |

#### <a name="actionitem"></a>ActionItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |× |
| link |string |× |

#### <a name="targetitem"></a>TargetItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| countries |array |× |
| locales |array |× |
| regions |array |× |
| cities |array |× |

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem: この投稿を対象とするニュース フィードを制御するオブジェクト
これらのグループのメンバーはこの投稿を表示する可能性が高く、他のユーザーは可能性が低い場合です。 ページにのみ適用されます。

| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| countries |array |× |
| regions |array |× |
| cities |array |× |
| age_min |integer |× |
| age_max |integer |× |
| genders |array |× |
| relationship_statuses |array |× |
| interested_in |array |× |
| college_years |array |× |
| interests |array |× |
| relevant_until |integer |× |
| education_statuses |array |× |
| locales |array |× |

#### <a name="placeitem"></a>PlaceItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |× |
| 名前 |string |× |
| overall_rating |number |× |
| location |未定義 |× |

#### <a name="locationitem"></a>LocationItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| city |string |× |
| country |string |× |
| latitude |number |× |
| located_in |string |× |
| longitude |number |× |
| 名前 |string |× |
| region |string |× |
| state |string |× |
| street |string |× |
| zip |string |× |

#### <a name="privacyitem"></a>PrivacyItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 説明 |string |× |
| 値 |string |○ |
| allow |string |× |
| deny |string |× |
| friends |string |× |

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| link |string |× |
| picture |string |× |
| image_hash |string |× |
| 名前 |string |× |
| 説明 |string |× |

#### <a name="postphotorequest"></a>PostPhotoRequest
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| url |string |○ |
| caption |string |× |

#### <a name="postphotoresponse"></a>PostPhotoResponse
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |○ |
| post_id |string |○ |

#### <a name="postvideorequest"></a>PostVideoRequest
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| videoData |string |○ |
| 説明 |string |○ |
| title |string |○ |
| uploadedVideoName |string |× |

#### <a name="getphotoresponse"></a>GetPhotoResponse
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| data |未定義 |○ |

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| url |string |○ |
| is_silhouette |boolean |○ |
| height |string |× |
| width |string |× |

#### <a name="geteventresponse"></a>GetEventResponse
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| data |array |○ |

#### <a name="geteventresponseitem"></a>GetEventResponseItem
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |○ |
| 名前 |string |○ |
| start_time |string |× |
| end_time |string |× |
| timezone |string |× |
| location |string |× |
| 説明 |string |× |
| ticket_uri |string |× |
| rsvp_status |string |はい |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。


