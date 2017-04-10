---
title: "ロジック アプリに HTTP アクションを追加する | Microsoft Docs"
description: "HTTP アクションとプロパティの概要"
services: 
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
tags: connectors
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: d3514dad84bea024ad6215711877a9784a8d8ffd
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-http-action"></a>HTTP アクションの概要
HTTP アクションを使用すると、組織のワークフローを拡張し、HTTP 経由で任意のエンドポイントと通信することができます。

そのための方法は次のとおりです。

* 管理対象の Web サイトがダウンしたときにアクティブ化する (トリガーする) ロジック アプリ ワークフローを作成します。
* HTTP 経由で任意のエンドポイントと通信して、ワークフローを他のサービスに拡張します。

ロジック アプリで HTTP アクションの使用を開始する方法については、 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事をご覧ください。

## <a name="use-the-http-trigger"></a>HTTP トリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 [トリガーの詳細についてはこちらを参照してください](connectors-overview.md)。

ロジック アプリ デザイナーで HTTP トリガーをセットアップする方法の例を次に示します。

1. ロジック アプリに HTTP トリガーを追加します。
2. ポーリングする HTTP エンドポイントのパラメーターを入力します。
3. ポーリングする頻度の繰り返し間隔を変更します。
4. これで、ロジック アプリを起動すると各チェック時に内容が返されるようになりました。

![HTTP トリガー](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>HTTP トリガーのしくみ
HTTP トリガーは、定期的な間隔で HTTP エンドポイントを呼び出します。 既定では、HTTP 応答コードが 300 より小さい場合にロジック アプリが実行されます。 HTTP 呼び出しの後に、ロジック アプリを起動するかどうかを判断するための評価条件をコード ビューに追加できます。 返された状態コードが `400`以上の場合に常に起動する HTTP トリガーの例を次に示します。

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

HTTP トリガーのパラメーターの詳細については、 [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger)を参照してください。

## <a name="use-the-http-action"></a>HTTP アクションの使用
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 [アクションの詳細についてはこちらを参照してください](connectors-overview.md)。

1. **[新しいステップ]** をクリックします。
2. **[アクションの追加]**を選択します。
3. アクションの検索ボックスに「 **HTTP** 」と入力して、HTTP アクションの一覧を表示します。
   
    ![Select the HTTP action](./media/connectors-native-http/using-action-1.png)
4. HTTP 呼び出しに必要なすべてのパラメーターを追加します。
   
    ![Complete the HTTP action](./media/connectors-native-http/using-action-2.png)
5. ツール バーの左上隅をクリックして、保存します。 ロジック アプリが保存されて発行 (アクティブ化) されます。

## <a name="http-trigger"></a>HTTP トリガー
ここでは、このコネクタでサポートされているトリガーの詳細について説明します。 HTTP コネクタにはトリガーが&1; つあります。

| トリガー | Description |
| --- | --- |
| HTTP |HTTP 呼び出しを実行し、応答コンテンツを返します。 |

## <a name="http-action"></a>HTTP アクション
ここでは、このコネクタでサポートされているアクションの詳細について説明します。 HTTP コネクタには、使用可能なアクションが&1; つあります。

| アクション | Description |
| --- | --- |
| HTTP |HTTP 呼び出しを実行し、応答コンテンツを返します。 |

## <a name="http-details"></a>HTTP の詳細
次の表に、アクションの必須および省略可能な入力フィールドと、各アクションの使用に伴う出力の詳細を示します。

#### <a name="http-request"></a>HTTP 要求
HTTP 送信要求を実行するアクションの入力フィールドを次に示します。
* は、必須フィールドであることを示しています。

| 表示名 | プロパティ名 | Description |
| --- | --- | --- |
| メソッド* |静的メソッド |使用する HTTP 動詞 |
| URI* |uri |HTTP 要求の URI |
| ヘッダー |ヘッダー |含める HTTP ヘッダーの JSON オブジェクト |
| 本文 |本文 |HTTP 要求の本文 |
| 認証 |認証 |詳細については「 [認証](#authentication) 」セクションを参照 |

<br>

#### <a name="output-details"></a>出力の詳細
HTTP 応答の出力の詳細を次に示します。

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| headers |オブジェクト |応答ヘッダー |
| 本文 |オブジェクト |応答オブジェクト |
| 状態コード |int |HTTP 状態コード |

## <a name="authentication"></a>認証
Logic Apps 機能では、HTTP エンドポイントに対してさまざまな種類の認証を使用できます。 この認証は、**HTTP**、**[HTTP + Swagger](connectors-native-http-swagger.md)**、**[HTTP Webhook](connectors-native-webhook.md)** の各コネクタで使用できます。 次の種類の認証を構成できます。

* [基本認証](#basic-authentication)
* [クライアント証明書認証](#client-certificate-authentication)
* [Azure Active Directory (Azure AD) OAuth 認証](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>基本認証
基本認証には、次の認証オブジェクトが必要です。
* は、必須フィールドであることを示しています。

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| Type* |type |認証の種類 (基本認証の場合は `Basic` を指定する必要があります) |
| Username* |username |認証するユーザー名 |
| Password* |パスワード |認証するパスワード。 |

> [!TIP]
> 定義から取得できないパスワードを使用する場合は、`securestring` パラメーターと `@parameters()` [ワークフロー定義関数](http://aka.ms/logicappdocs)を使用します。
> 
> 

認証フィールドで次のようなオブジェクトを作成します。

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>クライアント証明書認証
クライアント証明書認証には、次の認証オブジェクトが必要です。 * は、必須フィールドであることを示しています。

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| Type* |type |認証の種類 (SSL クライアント証明書の場合は、 `ClientCertificate` を指定する必要があります) |
| PFX* |pfx |Base64 でエンコードされた Personal Information Exchange (PFX) ファイルのコンテンツ |
| Password* |パスワード |PFX ファイルにアクセスするためのパスワード |

> [!TIP]
> ロジック アプリの保存後に定義内で読み取ることができなくなるパラメーターを使用するには、`securestring` パラメーターと `@parameters()` [ワークフロー定義関数](http://aka.ms/logicappdocs)を使用します。
> 
> 

次に例を示します。

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Azure AD OAuth 認証
Azure AD OAuth 認証には、次の認証オブジェクトが必要です。 * は、必須フィールドであることを示しています。

| プロパティ名 | データ型 | Description |
| --- | --- | --- |
| Type* |type |認証の種類 (Azure AD OAuth 認証の場合は `ActiveDirectoryOAuth` を指定する必要があります) |
| Tenant* |テナント |Azure AD テナントのテナント識別子。 |
| Audience* |対象となる読者 |`https://management.core.windows.net/` |
| Client ID* |clientId |Azure AD アプリケーションのクライアント識別子 |
| Secret* |secret |トークンを要求しているクライアントのシークレット |

> [!TIP]
> 保存後に定義内で読み取ることができなくなるパラメーターを使用するには、`securestring` パラメーターと `@parameters()` [ワークフロー定義関数](http://aka.ms/logicappdocs)を使用します。
> 
> 

次に例を示します。

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>次のステップ
プラットフォームを試用し、 [ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)してください。 [API リスト](apis-list.md)を参照すると、Logic Apps で使用可能な他のコネクタについて確認できます。


