---
title: "ロジック アプリで Office 365 Video コネクタを使用する | Microsoft Docs"
description: "Microsoft Azure App Service Logic Apps で Office 365 Video コネクタを使用する方法の概要について説明します"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 738e5aa7-2523-4116-8b65-211b9063852d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: e6f569355dd73919824f22c88ce3eead10e5b853


---
# <a name="get-started-with-the-office365-video-connector"></a>Office&365; Video コネクタの使用
Office 365 ビデオに関する情報の取得、ビデオの一覧の取得などを行うには、Office 365 ビデオに接続します。 Office 365 Video コネクタは、次のツールから使用できます。

* Logic Apps 

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。 このコネクタは、以前のスキーマ バージョンではサポートされていません。
> 
> 

Office 365 ビデオは、次のことを行えます。

* Office 365 ビデオから取得したデータに基づいてビジネス フローを構築できます。 
* ビデオ ポータルの状態の確認、チャネル内にあるすべてのビデオの一覧の取得などのアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、Bing Search コネクタを使用して Office 365 ビデオを検索でき、Office 365 Video コネクタを使用してそのビデオに関する情報を取得できます。 ビデオが要件を満たしている場合は、Facebook にこのビデオを投稿できます。 

ロジック アプリに操作を追加する方法については、「 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)」を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
Office 365 Video コネクタでは、次のアクションを使用できます。 トリガーはありません。

| トリガー | アクション |
| --- | --- |
| なし |<ul><li>ビデオ ポータルの状態を確認します</li><li>表示可能なすべてのチャネルを取得します</li><li>ビデオの Azure Media Services マニフェストの再生 URL を取得します</li><li>ベアラー トークンを取得して、ビデオを復号化するアクセス権を得ます</li><li>特定の Office&365; ビデオの情報を取得します</li><li>チャネル内にあるすべての Office&365; ビデオを一覧表示します</li></ul> |

すべてのコネクタは、JSON および XML 形式のデータに対応します。 

## <a name="create-a-connection-to-office365-video-connector"></a>Office&365; Video コネクタへの接続を作成する
このコネクタをロジック アプリに追加する場合は、Office 365 Video アカウントにサインインして、ロジック アプリでアカウントに接続できるようにする必要があります。

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

接続を作成したら、テナント名またはチャネル ID など、Office 365 ビデオ プロパティを入力します。 これらのプロパティについては、このトピックの **REST API リファレンス** を参照してください。

> [!TIP]
> 他のロジック アプリでも、前述の Office 365 ビデオ接続を使用できます。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API リファレンス
適用されるバージョン: 1.0。

### <a name="checks-video-portal-status"></a>ビデオ ポータルの状態を確認します
ビデオ ポータルの状態で、ビデオ サービスが有効かどうかを確認します。  
```GET: /{tenant}/IsEnabled``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テナント |string |あり |path |なし |ユーザーが属するディレクトリのテナント名 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 404 |見つかりません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="get-all-viewable-channels"></a>表示可能なすべてのチャネルを取得します
ユーザーが表示アクセス権を持っているすべてのチャネルを取得します。  
```GET: /{tenant}/Channels``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テナント |string |あり |path |なし |ユーザーが属するディレクトリのテナント名 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 404 |見つかりません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>チャネル内にあるすべての Office&365; ビデオを一覧表示します
チャネル内にあるすべての Office&365; ビデオを一覧表示します。  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テナント |string |あり |path |なし |ユーザーが属するディレクトリのテナント名 |
| channelId |string |あり |path |なし |ビデオを取得するチャネルの ID |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 404 |見つかりません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="gets-information-about-a-particular-office365-video"></a>特定の Office&365; ビデオの情報を取得します
特定の Office&365; ビデオの情報を取得します。  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テナント |string |あり |path |なし |ユーザーが属するディレクトリのテナント名 |
| channelId |string |あり |path |なし |チャネル ID |
| videoId |string |あり |path |なし |ビデオ ID |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 404 |見つかりません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>ビデオの Azure Media Services マニフェストの再生 URL を取得します
ビデオの Azure Media Services マニフェストの再生 URL を取得します。  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テナント |string |あり |path |なし |ユーザーが属するディレクトリのテナント名 |
| channelId |string |あり |path |なし |チャネル ID |
| videoId |string |あり |path |なし |ビデオ ID |
| streamingFormatType |string |○ |query |なし |ストリーミング形式の種類。 1 - Smooth Streaming または MPEG-DASH。 0 - HLS Streaming |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 404 |見つかりません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>ベアラー トークンを取得して、ビデオを復号化するアクセス権を得ます
ベアラー トークンを取得して、ビデオを復号化するアクセス権を得ます。  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| テナント |string |あり |path |なし |ユーザーが属するディレクトリのテナント名 |
| channelId |string |あり |path |なし |チャネル ID |
| videoId |string |あり |path |なし |ビデオ ID |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 404 |見つかりません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
#### <a name="channel-channel-class"></a>チャネル: チャネル クラス
| 名前 | データ型 | 必須 |
| --- | --- | --- |
| id |string |× |
| タイトル |string |× |
| 説明 |string |× |

#### <a name="video"></a>ビデオ
| 名前 | データ型 | 必須 |
| --- | --- | --- |
| id |string |× |
| タイトル |string |× |
| 説明 |string |× |
| CreationDate |string |× |
| 所有者 |string |× |
| ThumbnailUrl |string |× |
| VideoUrl |string |× |
| VideoDuration |整数 |× |
| VideoProcessingStatus |整数 |× |
| ViewCount |整数 |× |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。




<!--HONumber=Jan17_HO3-->


