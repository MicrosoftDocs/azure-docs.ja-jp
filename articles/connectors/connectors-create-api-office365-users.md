---
title: "Logic Apps に Office 365 ユーザー コネクタを追加する | Microsoft Docs"
description: "Office 365 Users コネクタと REST API パラメーターの概要"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b2146481-9105-4f56-b4c2-7ae340cb922f
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 2e4af1c9369a97d518d027dc4679b9f01ca53d4c


---
# <a name="get-started-with-the-office-365-users-connector"></a>Office 365 Users コネクタの使用
Office 365 ユーザーに接続して、プロファイルの取得、ユーザーの検索などを行います。 

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
> 
> 

Office 365 ユーザーは、次のことを行えます。

* Office 365 ユーザーから取得したデータに基づいてビジネス フローを構築できます。 
* 直属の部下の取得、上司のユーザーのプロファイルの取得などのアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、ユーザーの直属の部下を取得し、この情報を利用して、SQL Azure Database を更新します。 

ロジック アプリに操作を追加する方法については、「 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)」を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
Office 365 Users コネクタでは、次のアクションを使用できます。 トリガーはありません。

| トリガー | アクション |
| --- | --- |
| なし |<ul><li>上司の取得</li><li>自分のプロファイルを取得</li><li>直接の部下を取得</li><li>ユーザー プロファイルの取得</li><li>ユーザーの検索</li></ul> |

すべてのコネクタは、JSON および XML 形式のデータに対応します。 

## <a name="create-a-connection-to-office-365-users"></a>Office 365 Users への接続を作成します
このコネクタをロジック アプリに追加する場合は、Office 365 ユーザー アカウントにサインインして、ロジック アプリでアカウントに接続できるようにする必要があります。

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

接続を作成したら、ユーザー ID など、Office 365 ユーザー プロパティを入力します。 これらのプロパティについては、このトピックの **REST API リファレンス** を参照してください。

> [!TIP]
> 他のロジック アプリでも、前述の Office 365 ユーザー接続を使用できます。
> 
> 

## <a name="office-365-users-rest-api-reference"></a>Office 365 Users REST API リファレンス
適用されるバージョン: 1.0。

### <a name="get-my-profile"></a>自分のプロファイルを取得
現在のユーザーのプロファイルを取得します。  
```GET: /users/me``` 

この呼び出しには、パラメーターはありません。

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 202 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="get-user-profile"></a>ユーザー プロファイルの取得
特定のユーザー プロファイルを取得します。  
```GET: /users/{userId}``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| userId |string |あり |path |なし |ユーザー プリンシパル名または電子メール ID |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 202 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="get-manager"></a>上司の取得
指定されたユーザーの上司のユーザー プロファイルを取得します。  
```GET: /users/{userId}/manager``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| userId |string |あり |path |なし |ユーザー プリンシパル名または電子メール ID |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 202 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="get-direct-reports"></a>直接の部下を取得
直接の部下を取得します。  
```GET: /users/{userId}/directReports``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| userId |string |あり |path |なし |ユーザー プリンシパル名または電子メール ID |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 202 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

### <a name="search-for-users"></a>ユーザーの検索
ユーザー プロファイルの検索結果を取得します。  
```GET: /users``` 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| searchTerm |string |× |query |なし |検索文字列 (以下に適用されます: 表示名、姓を含まない名前、姓、電子メール、メールのニックネーム、ユーザー プリンシパル名) |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |操作に成功しました |
| 202 |操作に成功しました |
| 400 |BadRequest |
| 401 |権限がありません |
| 403 |許可されていません |
| 500 |内部サーバー エラー |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
#### <a name="user-user-model-class"></a>ユーザー: ユーザー モデル クラス
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| DisplayName |string |× |
| GivenName |string |× |
| Surname |string |× |
| Mail |string |× |
| MailNickname |string |× |
| TelephoneNumber |string |× |
| AccountEnabled |ブール値 |× |
| ID |string |○ |
| UserPrincipalName |string |× |
| 学科 |string |× |
| JobTitle |string |× |
| MobilePhone |string |× |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。

[API リスト](apis-list.md)に戻ります。

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG



<!--HONumber=Jan17_HO3-->


