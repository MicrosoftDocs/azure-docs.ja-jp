---
title: " ロジック アプリでの Slack コネクタの使用 | Microsoft Docss"
description: "ロジック アプリで Slack コネクタを使用します"
services: 
documentationcenter: 
author: msftman
manager: anneta
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: 404776283ee10aa9410d002597e054757516bcab
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-slack-connector"></a>Slack コネクタの使用
Slack はチーム コミュニケーション ツールです。チームのすべてのコミュニケーションが&1; 個所にまとめられ、どこにいてもすぐに検索、使用できるようになります。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
> 
> 

Slack コネクタでは、次の操作を実行できます。

* ロジック アプリを構築できます

ロジック アプリに操作を追加する方法については、「 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)」を参照してください。

## <a name="lets-talk-about-triggers-and-actions"></a>トリガーとアクション
Slack コネクタは、アクションとして使用できます。トリガーはありません。 すべてのコネクタは、JSON および XML 形式のデータに対応します。 

 Slack コネクタでは、次のアクションやトリガーを使用できます。

### <a name="slack-actions"></a>Slack のアクション
実行できるアクションは以下のとおりです。

| アクション | 説明 |
| --- | --- |
| PostMessage |指定したチャネルにメッセージを投稿します。 |

## <a name="create-a-connection-to-slack"></a>Slack への接続を作成する
Slack コネクタを使用するには、最初に **接続** を作成し、以下のプロパティの詳細を指定します。 

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |あり |Slack の資格情報を指定します |

次の手順に従って、Slack にサインインし、ロジック アプリの Slack **接続** の構成を完了します。

1. **[繰り返し]**
2. **[頻度]** を選択し、**[間隔]** を入力します。
3. **[アクションの追加]** を選択します。  
   ![Slack の構成][1]  
4. 検索ボックスに「Slack」と入力し、名前に Slack が含まれたすべてのエントリが返されるまで待ちます。
5. **[Slack - メッセージの投稿]**
6. **[Slack にサインイン]** をクリックします。  
   ![Slack の構成][2]
7. アプリケーションを承認するために、Slack 資格情報を入力してサインインします。    
   ![Slack の構成][3]  
8. 組織のログイン ページにリダイレクトされます。 Slack がロジック アプリと対話することを**承認**します。      
   ![Slack の構成][5] 
9. 承認が完了すると、ロジック アプリにリダイレクトされます。**[Slack - すべてのメッセージを取得]** セクションを構成して、ロジック アプリを完成させます。 必要な他のトリガーやアクションを追加します。  
   ![Slack の構成][6]
10. 上部のメニュー バーの **[保存]** をクリックして、作業内容を保存します。

> [!TIP]
> 他のロジック アプリでもこの接続を使用できます。
> 
> 

## <a name="slack-rest-api-reference"></a>Slack REST API リファレンス
#### <a name="this-documentation-is-for-version-10"></a>このドキュメントの対象バージョン: 1.0
### <a name="post-a-message-to-a-specified-channel"></a>指定したチャネルにメッセージを投稿します。
**```POST: /chat.postMessage```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| channel |string |○ |query |なし |メッセージを送信するチャネル、プライベート グループ、IM チャネル。 名前 (例: #general) またはエンコードされた ID を指定できます。 |
| text |string |○ |query |なし |送信するメッセージのテキスト。 書式設定オプションについては、https://api.slack.com/docs/formatting をご覧ください。 |
| username |string |× |query |なし |ボットの名前 |
| as_user |boolean |× |query |なし |ボットとしてではなく、認証されたユーザーとして、メッセージを投稿するには、true を渡します |
| parse |string |× |query |なし |メッセージの処理方法を変更します。 詳細については、https://api.slack.com/docs/formatting をご覧ください。 |
| link_names |integer |× |query |なし |チャネル名とユーザー名を検索してリンクします。 |
| unfurl_links |boolean |× |query |なし |主にテキストベースのコンテンツの展開を有効にするには、true を渡します。 |
| unfurl_media |boolean |× |query |なし |メディア コンテンツの展開を無効にするには、false を渡します。 |
| icon_url |string |× |query |なし |このメッセージのアイコンとして使用する画像の URL |
| icon_emoji |string |× |query |なし |このメッセージのアイコンとして使用する絵文字 |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 400 |正しくない要求 |
| 408 |要求タイムアウト |
| 429 |要求が多すぎます |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| 503 |Slack サービス利用不可 |
| 504 |ゲートウェイ タイムアウト |
| default |操作に失敗しました。 |

- - -
## <a name="object-definitions"></a>オブジェクト定義:
 **メッセージ**: Slack メッセージ

Message の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| id |integer |
| content_excerpt |string |
| sender_id |integer |
| replied_to_id |integer |
| created_at |string |
| network_id |integer |
| message_type |string |
| sender_type |string |
| url |string |
| web_url |string |
| group_id |integer |
| body |未定義 |
| thread_id |integer |
| direct_message |boolean |
| client_type |string |
| client_url |string |
| 言語 |string |
| notified_user_ids |array |
| privacy |string |
| liked_by |未定義 |
| system_message |ブール値 |

 **PostOperationRequest**: Slack に投稿する Slack コネクタの post 要求を表します

PostOperationRequest の必須プロパティ:

body

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| body |string |
| group_id |integer |
| replied_to_id |integer |
| direct_to_id |integer |
| broadcast |ブール値 |
| topic1 |string |
| topic2 |string |
| topic3 |string |
| topic4 |string |
| topic5 |string |
| topic6 |string |
| topic7 |string |
| topic8 |string |
| topic9 |string |
| topic10 |string |
| topic11 |string |
| topic12 |string |
| topic13 |string |
| topic14 |string |
| topic15 |string |
| topic16 |string |
| topic17 |string |
| topic18 |string |
| topic19 |string |
| topic20 |string |

 **MessageList**: メッセージの一覧

MessageList の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| messages |array |

 **MessageBody**: メッセージ本文

MessageBody の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| parsed |string |
| plain |string |
| rich |string |

 **LikedBy**: いいねを付けたユーザー

LikedBy の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| count |integer |
| names |array |

 **YammmerEntity**: いいねを付けたユーザー

YammmerEntity の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| type |string |
| id |integer |
| full_name |string |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)

## <a name="object-definitions"></a>オブジェクト定義:
 **WebResultModel**: Bing の Web 検索結果

WebResultModel の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| タイトル |string |
| 説明 |string |
| DisplayUrl |string |
| id |string |
| FullUrl |string |

 **VideoResultModel**: Bing の動画検索結果

VideoResultModel の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| タイトル |string |
| DisplayUrl |string |
| id |string |
| MediaUrl |string |
| ランタイム |integer |
| サムネイル |未定義 |

 **ThumbnailModel**: マルチメディア要素のサムネイルのプロパティ

ThumbnailModel の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| MediaUrl |string |
| ContentType |string |
| 幅 |integer |
| 高さ |integer |
| FileSize |integer |

 **ImageResultModel**: Bing の画像検索結果

ImageResultModel の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| タイトル |string |
| DisplayUrl |string |
| id |string |
| MediaUrl |string |
| SourceUrl |string |
| サムネイル |未定義 |

 **NewsResultModel**: Bing のニュース検索結果

NewsResultModel の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| タイトル |string |
| 説明 |string |
| DisplayUrl |string |
| id |string |
| から |string |
| Date |string |

 **SpellResultModel**: Bing の候補の検索結果

SpellResultModel の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| id |string |
| 値 |string |

 **RelatedSearchResultModel**: Bing の関連項目の検索結果

RelatedSearchResultModel の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| タイトル |string |
| id |string |
| BingUrl |string |

 **CompositeSearchResultModel**: Bing の複合検索結果

CompositeSearchResultModel の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| WebResultsTotal |integer |
| ImageResultsTotal |integer |
| VideoResultsTotal |integer |
| NewsResultsTotal |integer |
| SpellSuggestionsTotal |integer |
| WebResults |array |
| ImageResults |array |
| VideoResults |array |
| NewsResults |array |
| SpellSuggestionResults |array |
| RelatedSearchResults |array |

## <a name="object-definitions"></a>オブジェクト定義:
 **PostOperationResponse**: Slack への投稿に対する Slack コネクタの post 操作の応答を表します。

PostOperationResponse の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| OK |ブール値 |
| channel |string |
| ts |string |
| Message |未定義 |
| error |string |

 **MessageItem**: チャネル メッセージ。

MessageItem の必須のプロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| text |string |
| id |string |
| ユーザー |string |
| created |integer |
| is_user-deleted |boolean |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png

