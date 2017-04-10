---
title: "ロジック アプリで Twitter コネクタを使用する方法 | Microsoft Docs"
description: "Twitter コネクタと REST API パラメーターの概要"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 13ecb8936484b1c86938a16c7dd6da8000d4ffec


---
# <a name="get-started-with-the-twitter-connector"></a>Twitter コネクタの使用
Twitter コネクタでは、次のことが可能です。

* ツイートの投稿とツイートの取得
* タイムライン、友達、フォロワーへのアクセス
* 以下で説明するその他のアクションやトリガーの実行  

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)から始めることができます。  

## <a name="connect-to-twitter"></a>Twitter への接続
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 [接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。  

### <a name="create-a-connection-to-twitter"></a>Twitter への接続を作成する
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Twitter トリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

この例では、**When a new tweet is posted (新しいツイートの投稿時)** トリガーを使用して #Seattle を検索し、#Seattle が見つかった場合に、Dropbox のファイルをツイートのテキストで更新する方法について説明します。 企業での使用例として、自社の名前を検索し、SQL データベースをツイートのテキストで更新できます。

1. Logic Apps デザイナーの検索ボックスに「*twitter*」と入力し、**[Twitter - When a new tweet is posted (Twitter - 新しいツイートの投稿時)]** トリガーを選択します。   
   ![Twitter トリガーの画像 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. **[Search Text (検索テキスト)]** コントロールに「*#Seattle*」と入力します。  
   ![Twitter トリガーの画像 2](./media/connectors-create-api-twitter/trigger-2.png) 

これで、ワークフローでその他のトリガーとアクションの実行を開始するトリガーのあるロジック アプリが構成されました。 

> [!NOTE]
> ロジック アプリを機能させるには、少なくとも&1; つのトリガーとアクションが含まれている必要があります。 アクションを追加するには、次のセクションの手順に従います。  
> 
> 

## <a name="add-a-condition"></a>条件を追加する
ここでは、50 人以上のユーザーを持つユーザーのツイートだけを対象とするので、まずフォロワー数を確認する条件をロジック アプリに追加する必要があります。  

1. **[+ 新しいステップ]** をクリックして、新しいツイートで #Seattle が見つかったときに実行するアクションを追加します。  
   ![Twitter アクションの画像 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. **[条件の追加]** リンクをクリックします。  
   ![Twitter 条件の画像 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   **[条件]** コントロールが開きます。ここで、*[is equal to (次の値に等しい)]*、*[is less than (次の値より小さい)]*、*[is greater than (次の値より大きい)]*、*[contains (次の値を含む)]* などの条件を確認できます。  
   ![Twitter 条件の画像 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. **[値の選択]** コントロールを選択します。  
   このコントロールでは、条件が true または false に評価される値として、以前のアクションまたはトリガーの&1; つ以上のプロパティを選択できます。
   ![Twitter 条件の画像 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. 使用可能なすべてのプロパティを確認できるように、**[...]** をクリックしてプロパティの一覧を展開します。        
   ![Twitter 条件の画像 4](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. **[Followers count (フォロワー数)]** プロパティを選択します。    
   ![Twitter 条件の画像 5](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. 値コントロールに [Followers count (フォロワー数)] プロパティが表示されていることを確認します。    
   ![Twitter 条件の画像 6](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. 演算子の一覧から **[is greater than (次の値より大きい)]** を選択します。    
   ![Twitter 条件の画像 7](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. "*is greater than (次の値より大きい)*" 演算子のオペランドとして「50」と入力します。  
   これで条件が追加されました。 上部のメニューの **[保存]** リンクをクリックして作業内容を保存します。    
   ![Twitter 条件の画像 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Twitter アクションの使用
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。  

トリガーを追加したら、次の手順に従って、トリガーによって検出されたツイートの内容を含む新しいツイートを投稿するアクションを追加します。 このチュートリアルでは、50 人以上のフォロワーを持つユーザーのツイートだけを投稿します。  

次の手順では、50 人以上のフォロワーを持つユーザーが投稿した各ツイートの一部のプロパティを使用してツイートを投稿する Twitter アクションを追加します。  

1. **[アクションの追加]**を選択します。 他のアクションやトリガーを検索できる検索コントロールが開きます。  
   ![Twitter 条件の画像 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. 検索ボックスに「*twitter*」と入力し、**[Twitter - Post a tweet (Twitter - ツイートの投稿)]** アクションを選択します。 **[Post a tweet (ツイートの投稿)]** コントロールが開きます。ここで、投稿するツイートの詳細をすべて入力します。      
   ![Twitter アクションの画像 1 ～ 5](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. **[Tweet text (ツイート テキスト)]** を選択します。 ロジック アプリでの以前のアクションとトリガーのすべての出力が表示されます。 これらのいずれかを選択し、新しいツイートのツイート テキストの一部として使用できます。     
   ![Twitter アクションの画像 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. **[ユーザー名]** を選択します。   
5. [Tweet text (ツイート テキスト)] コントロールで、「*のコメント:*」と入力します。 これはユーザー名の直後に入力します。  
6. *[Tweet text (ツイート テキスト)]* を選択します。       
   ![Twitter アクションの画像 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. 作業内容を保存し、#Seattle というハッシュタグを含めたツイートを送信してワークフローをアクティブにします。  

## <a name="technical-details"></a>技術的な詳細
ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

## <a name="twitter-triggers"></a>Twitter トリガー
Twitter コネクタには、次のトリガーがあります。  

| トリガー | 説明 |
| --- | --- |
| [新しいツイートの投稿時](connectors-create-api-twitter.md#when-a-new-tweet-is-posted) |この操作では、特定の検索クエリに一致する新しいツイートが投稿されたときにフローをトリガーします。 |

## <a name="twitter-actions"></a>Twitter アクション
Twitter コネクタには、次のアクションがあります。

| [操作] | 説明 |
| --- | --- |
| [ユーザーのタイムラインを取得する](connectors-create-api-twitter.md#get-user-timeline) |この操作では、特定のユーザーによって投稿された最近のツイートの一覧を取得します。 |
| [ホーム タイムラインを取得する](connectors-create-api-twitter.md#get-home-timeline) |この操作では、自分と自分のフォロワーによって投稿された最近のツイートとリツイートを取得します。 |
| [ツイートの検索](connectors-create-api-twitter.md#search-tweets) |この操作では、検索クエリに一致する関連ツイートの一覧を取得します。 |
| [フォロワーを取得する](connectors-create-api-twitter.md#get-followers) |この操作では、特定のユーザーをフォローしているユーザーの一覧を取得します。 |
| [自分のフォロワーを取得する](connectors-create-api-twitter.md#get-my-followers) |この操作では、自分をフォローしているユーザーの一覧を取得します。 |
| [フォローを取得する](connectors-create-api-twitter.md#get-following) |この操作では、特定のユーザーがフォローしている相手の一覧を取得します。 |
| [自分のフォローを取得する](connectors-create-api-twitter.md#get-my-following) |この操作では、自分がフォローしているユーザーの一覧を取得します。 |
| [ユーザーを取得する](connectors-create-api-twitter.md#get-user) |この操作では、ユーザー名、説明、フォロワー数など、特定のユーザーのプロフィールの詳細を取得します。 |
| [ツイートを投稿する](connectors-create-api-twitter.md#post-a-tweet) |この操作では、新しいツイートを投稿します。 |

## <a name="action-details"></a>アクションの詳細
ここでは、このコネクタのアクションおよびトリガーとその応答について詳しく説明します。

### <a name="get-user-timeline"></a>ユーザーのタイムラインを取得する
この操作では、特定のユーザーによって投稿された最近のツイートの一覧を取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| userName* |ユーザー名 |ユーザーの Twitter ハンドル |
| maxResults |Maximum results (結果の最大数) |返されるツイートの最大数 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
TweetModel: ツイート オブジェクトを表すモデル

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| tweetText |string |ツイートのテキストの内容 |
| TweetId |string |ツイートの ID |
| CreatedAt |string |ツイートが投稿された時刻 |
| RetweetCount |integer |ツイートのリツイートの総数 |
| TweetedBy |string |ツイートを投稿したユーザーの名前 |
| MediaUrls |array |ツイートと共に投稿されたメディアの URI |
| TweetLanguageCode |string |ツイートの言語コード |
| TweetInReplyToUserId |string |現在のツイートの返信先であるツイートの作成者のユーザー ID |
| Favorited |boolean |ツイートがお気に入りとしてマークされているかどうかを示します。 |
| UserMentions |array |ツイートに記載されているユーザーのリスト |
| OriginalTweet |未定義 |現在のツイートがリツイートされた元のツイート |
| UserDetails |未定義 |ツイートしたユーザーの詳細 |

### <a name="get-home-timeline"></a>ホーム タイムラインを取得する
この操作では、自分と自分のフォロワーによって投稿された最近のツイートとリツイートを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| maxResults |Maximum results (結果の最大数) |返されるツイートの最大数 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
TweetModel: ツイート オブジェクトを表すモデル

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| tweetText |string |ツイートのテキストの内容 |
| TweetId |string |ツイートの ID |
| CreatedAt |string |ツイートが投稿された時刻 |
| RetweetCount |integer |ツイートのリツイートの総数 |
| TweetedBy |string |ツイートを投稿したユーザーの名前 |
| MediaUrls |array |ツイートと共に投稿されたメディアの URI |
| TweetLanguageCode |string |ツイートの言語コード |
| TweetInReplyToUserId |string |現在のツイートの返信先であるツイートの作成者のユーザー ID |
| Favorited |boolean |ツイートがお気に入りとしてマークされているかどうかを示します。 |
| UserMentions |array |ツイートに記載されているユーザーのリスト |
| OriginalTweet |未定義 |現在のツイートがリツイートされた元のツイート |
| UserDetails |未定義 |ツイートしたユーザーの詳細 |

### <a name="search-tweets"></a>ツイートの検索
この操作では、検索クエリに一致する関連ツイートのリストを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| searchQuery* |検索テキスト |happy hour、#haiku、love OR hate のような検索語句 |
| maxResults |Maximum results (結果の最大数) |返されるツイートの最大数 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
TweetModel: ツイート オブジェクトを表すモデル

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| tweetText |string |ツイートのテキストの内容 |
| TweetId |string |ツイートの ID |
| CreatedAt |string |ツイートが投稿された時刻 |
| RetweetCount |integer |ツイートのリツイートの総数 |
| TweetedBy |string |ツイートを投稿したユーザーの名前 |
| MediaUrls |array |ツイートと共に投稿されたメディアの URI |
| TweetLanguageCode |string |ツイートの言語コード |
| TweetInReplyToUserId |string |現在のツイートの返信先であるツイートの作成者のユーザー ID |
| Favorited |boolean |ツイートがお気に入りとしてマークされているかどうかを示します。 |
| UserMentions |array |ツイートに記載されているユーザーのリスト |
| OriginalTweet |未定義 |現在のツイートがリツイートされた元のツイート |
| UserDetails |未定義 |ツイートしたユーザーの詳細 |

### <a name="get-followers"></a>フォロワーを取得する
この操作では、特定のユーザーをフォローしているユーザーの一覧を取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| userName* |ユーザー名 |ユーザーの Twitter ハンドル |
| maxResults |Maximum results (結果の最大数) |返されるユーザーの最大数 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
UserDetailsModel: Twitter ユーザーの詳細

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| FullName |string |ユーザーの名前 |
| 場所 |string |ユーザーの場所 |
| ID |integer |ユーザーの Twitter ID |
| UserName |string |ユーザーのスクリーン名 |
| FollowersCount |integer |フォロワー数 |
| 説明 |string |ユーザーの説明 |
| StatusesCount |integer |ユーザーの状態数 |
| FriendsCount |integer |友達の数 |
| FavouritesCount |integer |ユーザーがお気に入りに登録したツイートの数 |
| ProfileImageUrl |string |プロフィール画像の URL |

### <a name="get-my-followers"></a>自分のフォロワーを取得する
この操作では、自分をフォローしているユーザーの一覧を取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| maxResults |Maximum results (結果の最大数) |取得するユーザーの最大数 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
UserDetailsModel: Twitter ユーザーの詳細

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| FullName |string |ユーザーの名前 |
| 場所 |string |ユーザーの場所 |
| ID |integer |ユーザーの Twitter ID |
| UserName |string |ユーザーのスクリーン名 |
| FollowersCount |integer |フォロワー数 |
| 説明 |string |ユーザーの説明 |
| StatusesCount |integer |ユーザーの状態数 |
| FriendsCount |integer |友達の数 |
| FavouritesCount |integer |ユーザーがお気に入りに登録したツイートの数 |
| ProfileImageUrl |string |プロフィール画像の URL |

### <a name="get-following"></a>フォローを取得する
この操作では、特定のユーザーがフォローするユーザーのリストを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| userName* |ユーザー名 |ユーザーの Twitter ハンドル |
| maxResults |Maximum results (結果の最大数) |返されるユーザーの最大数 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
UserDetailsModel: Twitter ユーザーの詳細

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| FullName |string |ユーザーの名前 |
| 場所 |string |ユーザーの場所 |
| ID |integer |ユーザーの Twitter ID |
| UserName |string |ユーザーのスクリーン名 |
| FollowersCount |integer |フォロワー数 |
| 説明 |string |ユーザーの説明 |
| StatusesCount |integer |ユーザーの状態数 |
| FriendsCount |integer |友達の数 |
| FavouritesCount |integer |ユーザーがお気に入りに登録したツイートの数 |
| ProfileImageUrl |string |プロフィール画像の URL |

### <a name="get-my-following"></a>自分のフォローを取得する
この操作では、自分がフォローしているユーザーのリストを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| maxResults |Maximum results (結果の最大数) |返されるユーザーの最大数 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
UserDetailsModel: Twitter ユーザーの詳細

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| FullName |string |ユーザーの名前 |
| 場所 |string |ユーザーの場所 |
| ID |integer |ユーザーの Twitter ID |
| UserName |string |ユーザーのスクリーン名 |
| FollowersCount |integer |フォロワー数 |
| 説明 |string |ユーザーの説明 |
| StatusesCount |integer |ユーザーの状態数 |
| FriendsCount |integer |友達の数 |
| FavouritesCount |integer |ユーザーがお気に入りに登録したツイートの数 |
| ProfileImageUrl |string |プロフィール画像の URL |

### <a name="get-user"></a>ユーザーを取得する
この操作では、ユーザー名、説明、フォロワー数など、特定のユーザーのプロフィールの詳細を取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| userName* |ユーザー名 |ユーザーの Twitter ハンドル |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
UserDetailsModel: Twitter ユーザーの詳細

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| FullName |string |ユーザーの名前 |
| 場所 |string |ユーザーの場所 |
| ID |integer |ユーザーの Twitter ID |
| UserName |string |ユーザーのスクリーン名 |
| FollowersCount |integer |フォロワー数 |
| 説明 |string |ユーザーの説明 |
| StatusesCount |integer |ユーザーの状態数 |
| FriendsCount |integer |友達の数 |
| FavouritesCount |integer |ユーザーがお気に入りに登録したツイートの数 |
| ProfileImageUrl |string |プロファイル画像の URL |

### <a name="post-a-tweet"></a>ツイートを投稿する
この操作では、新しいツイートを投稿します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| tweetText |ツイート テキスト |投稿するテキスト |
| body |メディア |投稿するメディア |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
TweetResponseModel: 投稿されたツイートを表すモデル

| プロパティ名 | データ型 | 説明 |
| --- | --- | --- |
| TweetId |string |取得したツイートの ID |

### <a name="when-a-new-tweet-is-posted"></a>When a new tweet is posted (新しいツイートの投稿時)
この操作では、特定の検索クエリに一致する新しいツイートが投稿されたときにフローをトリガーします。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| searchQuery* |検索テキスト |"happy hour"、#haiku、好き嫌いなどの検索語句 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
TriggerBatchResponse[TweetModel]

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


