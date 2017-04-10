---
title: Azure Active Directory B2C | Microsoft Docs
description: "Azure Active Directory で導入された OpenID Connect 認証プロトコルを利用した Web アプリケーションの構築。"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: c371aaab-813a-4317-97df-b62e2f53d865
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 51061199e4929406c3ac77a26e2f972686236bd4


---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C: OAuth 2.0 承認コード フロー
デバイスにインストールされているアプリに、Web API など、保護されているリソースにアクセスする権利を与えるために OAuth 2.0 認証コード付与を利用できます。 Azure Active Directory (Azure AD) B2C で導入された OAuth 2.0 を利用することで、サインアップ、サインイン、その他の ID 管理タスクをモバイル アプリとデスクトップ アプリに追加できます。 このガイドは言語に依存しません。 オープンソース ライブラリを利用しないで、HTTP メッセージを送受信する方法について説明します。

<!-- TODO: Need link to libraries -->

OAuth 2.0 承認コード フローは、 [OAuth 2.0 仕様のセクション 4.1](http://tools.ietf.org/html/rfc6749)で規定されています。 [Web アプリ](active-directory-b2c-apps.md#web-apps)や[ネイティブにインストールされるアプリ](active-directory-b2c-apps.md#mobile-and-native-apps)を含め、大半のアプリの種類で認証と承認を行う際にこのフローを利用できます。 アプリは、このフローによって安全に **access_tokens** を取得し、[承認サーバー](active-directory-b2c-reference-protocols.md#the-basics)で保護されているリソースにアクセスできます。

本ガイドでは特に、OAuth 2.0 認証コード フローの**パブリック クライアント**について説明します。 パブリック クライアントとは、秘密のパスワードの整合性を守る目的で信頼できないクライアント アプリケーションのことです。 モバイル アプリ、デスクトップ アプリ、デバイスで実行され、access_tokens の取得が必要な大半のアプリが該当します。 Azure AD B2C を利用し、Web アプリに ID 管理を追加する場合、OAuth 2.0 ではなく、 [OpenID Connect](active-directory-b2c-reference-oidc.md) を使用する必要があります。

Azure AD B2C は、単純な認証と承認以上のことができるように標準の OAuth 2.0 プロトコルを拡張したものです。 [**ポリシー パラメーター**](active-directory-b2c-reference-policies.md)を導入しており、このパラメーターにより、OAuth 2.0 を利用し、サインアップ、サインイン、プロファイル管理などのユーザー操作をアプリに追加できます。 ここでは、OAuth 2.0 とポリシーを使用して、ネイティブ アプリケーションに各種のユーザー操作を導入する方法について説明します。 Web API にアクセスするための access_token を取得する方法についても説明します。

下の HTTP 要求例では、サンプル B2C ディレクトリの **fabrikamb2c.onmicrosoft.com**、サンプル アプリケーション、ポリシーを利用します。 これらの値を利用して、要求を自由に試すことができます。または、独自の値で置換できます。
[独自の B2C ディレクトリ、アプリケーション、ポリシーの取得方法](#use-your-own-b2c-directory)について学習してください。

## <a name="1-get-an-authorization-code"></a>1.承認コードを取得する
承認コード フローは、クライアントがユーザーを `/authorize` エンドポイントにリダイレクトさせることから始まります。 これはフローの対話部分であり、ユーザーが実際に操作します。 この要求では、クライアントはユーザーから取得する必要があるアクセス許可を `scope` パラメーターで示し、実行するポリシーを `p` パラメーターで示します。 3 つの例を以下に示します (読みやすいように改行してあります)。それぞれ異なるポリシーが使用されています。

#### <a name="use-a-sign-in-policy"></a>サインイン ポリシーを使用する
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>サインアップ ポリシーを使用する
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>プロファイル編集ポリシーを使用する
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| パラメーター | 必須 | Description |
| --- | --- | --- |
| client_id |必須 |[Azure Portal](https://portal.azure.com) によってアプリに割り当てられたアプリケーション ID。 |
| response_type |必須 |応答の種類。承認コード フローでは `code` を指定する必要があります。 |
| redirect_uri |必須 |アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope |必須 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 クライアント ID をスコープとして使用することは、同じクライアント ID で表される、独自のサービスまたは Web API に対して使用できる **アクセス トークン** をアプリが必要とすることを示します。  `offline_access` スコープは、リソースに長期アクセスするためにアプリは **refresh_token** を必要とすることを示します。  Azure AD B2C の **id_token** を要求するために、`openid` スコープを使用することもできます。 |
| response_mode |推奨 |結果として得られた authorization_code をアプリに返すときに使用するメソッド。 'query'、'form_post'、'fragment' のいずれかを指定できます。 |
| state |推奨 |要求に含まれ、かつトークンの応答として返される値。 任意の文字列を指定することができます。 クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページや実行中のポリシーなど) に関する情報をエンコードする目的にも使用されます。 |
| p |必須 |実行されるポリシー。 B2C ディレクトリに作成されたポリシーの名前です。 ポリシー名の値は、"b2c\_1\_" で始まっている必要があります。 ポリシーの詳細については、「 [拡張ポリシー フレームワーク](active-directory-b2c-reference-policies.md)」を参照してください。 |
| prompt |省略可能 |ユーザーとの必要な対話の種類。 現時点で有効な値は 'login' のみです。この場合ユーザーは要求時に、その資格情報を入力する必要があります。 シングル サインオンは作用しません。 |

この時点で、ユーザーにポリシーのワークフローの完了が求められます。 たとえば、ユーザー名とパスワードを入力したり、ソーシャル ID でサインインしたり、ディレクトリにサインアップしたりする必要があります。他にも、ポリシーの定義に基づき、多数の手順があります。

ユーザーがポリシーを完了すると、Azure AD は `response_mode` パラメーターに指定されたメソッドを使い、指定された `redirect_uri` でアプリに応答を返します。 実行されたポリシーに関係なく、上記のすべての例で応答はまったく同じになります。

`response_mode=query` を使用した場合の正常な応答は次のようになります。

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| パラメーター | Description |
| --- | --- |
| code |アプリが要求した authorization_code。 アプリは承認コードを使用して、対象リソースの access_token を要求します。 authorization_code は非常に短命です。 通常、約 10 分で期限が切れます。 |
| state |前の表の詳しい説明を参照してください。 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

アプリ側でエラーを適切に処理できるよう、 `redirect_uri` にはエラー応答も送信されます。

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| state |詳細についてはこのセクションの最初の表を参照してください。 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

## <a name="2-get-a-token"></a>2.トークンを取得する
authorization_code を取得したところで、`POST` 要求を `/token` エンドポイントに送信して、トークンの `code` を目的のリソースに適用できます。 Azure AD B2C では、トークンを要求できる唯一のリソースはアプリの独自のバックエンド Web API です。 自身のトークンを要求するには、アプリのクライアント ID をスコープとして使用します。

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| パラメーター | 必須 | Description |
| --- | --- | --- |
| p |必須 |認証コードの取得に使用されたポリシー。 この要求に別のポリシーを使用することはできません。 このパラメーターは、POST 本文ではなく、" *クエリ文字列*" に追加することに注意してください。 |
| client_id |必須 |[Azure Portal](https://portal.azure.com) によってアプリに割り当てられたアプリケーション ID。 |
| grant_type |必須 |許可の種類。承認コード フローでは `authorization_code` を指定する必要があります。 |
| scope |推奨 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 クライアント ID をスコープとして使用することは、同じクライアント ID で表される、独自のサービスまたは Web API に対して使用できる **アクセス トークン** をアプリが必要とすることを示します。  `offline_access` スコープは、リソースに長期アクセスするためにアプリは **refresh_token** を必要とすることを示します。  Azure AD B2C の **id_token** を要求するために、`openid` スコープを使用することもできます。 |
| code |必須 |フローの最初の段階で取得した authorization_code。 |
| redirect_uri |必須 |authorization_code を受け取った、アプリケーションの redirect_uri。 |

正常なトークン応答は次のようになります。

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| パラメーター | Description |
| --- | --- |
| not_before |トークンが有効と見なされる時間 (エポック時間)。 |
| token_type |トークン タイプ値。 Azure AD でサポートされるのは Bearer タイプのみです。 |
| access_token |要求した署名付きの JSON Web Token (JWT) トークン。 |
| scope |トークンの有効スコープ。後の利用のためにトークンのキャッシュに利用できます。 |
| expires_in |トークンが有効な時間の長さ (秒単位)。 |
| refresh_token |OAuth 2.0 の refresh_token。 現在のトークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のトークンを取得します。 refresh_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。 詳細については、 [B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。 |

エラー応答は次のようになります。

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| パラメーター | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## <a name="3-use-the-token"></a>手順 3.トークンを使用する
`access_token` を正常に取得したら、そのトークンを `Authorization` ヘッダーに追加することによって、バックエンド Web API への要求に使用することができます。

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4.トークンを更新する
アクセス トークンと ID トークンの有効期限は非常に短いです。 リソースに引き続きアクセスできるようにするには、有効期限が切れた後で更新する必要があります。 トークンを更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。 このとき、`code` の代わりに `refresh_token` を指定します。

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| パラメーター | 必須 | Description |
| --- | --- | --- |
| p |必須 |元の refresh_token の取得に使用されたポリシー。 この要求に別のポリシーを使用することはできません。 このパラメーターは、POST 本文ではなく、" *クエリ文字列*" に追加することに注意してください。 |
| client_id |推奨 |[Azure Portal](https://portal.azure.com) によってアプリに割り当てられたアプリケーション ID。 |
| grant_type |必須 |許可の種類。承認コード フローのこのレッグの `refresh_token` を指定する必要があります。 |
| scope |推奨 |スコープのスペース区切りリスト。 1 つのスコープ値が、要求されている両方のアクセス許可を Azure AD に示します。 クライアント ID をスコープとして使用することは、同じクライアント ID で表される、独自のサービスまたは Web API に対して使用できる **アクセス トークン** をアプリが必要とすることを示します。  `offline_access` スコープは、リソースに長期アクセスするためにアプリは **refresh_token** を必要とすることを示します。  Azure AD B2C の **id_token** を要求するために、`openid` スコープを使用することもできます。 |
| redirect_uri |省略可能 |authorization_code を受け取った、アプリケーションの redirect_uri。 |
| refresh_token |必須 |フローの第 2 段階で取得した元の refresh_token。 |

正常なトークン応答は次のようになります。

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| パラメーター | Description |
| --- | --- |
| not_before |トークンが有効と見なされる時間 (エポック時間)。 |
| token_type |トークン タイプ値。 Azure AD でサポートされるのは Bearer タイプのみです。 |
| access_token |要求した署名付きの JSON Web Token (JWT) トークン。 |
| scope |トークンの有効スコープ。後の利用のためにトークンのキャッシュに利用できます。 |
| expires_in |トークンが有効な時間の長さ (秒単位)。 |
| refresh_token |OAuth 2.0 の refresh_token。 現在のトークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のトークンを取得します。 refresh_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。 詳細については、 [B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。 |

エラー応答は次のようになります。

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| パラメーター | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## <a name="use-your-own-b2c-directory"></a>独自の B2C ディレクトリを使用する
これらの要求を自分で試す場合、最初に次の 3 つの手順を実行し、上記のサンプルの値を独自の値に置換する必要があります。

* [B2C ディレクトリを作成し](active-directory-b2c-get-started.md) 、要求でディレクトリの名前を使用します。
* [アプリケーションを作成し](active-directory-b2c-app-registration.md)、アプリケーション ID と redirect_uri を取得します。 アプリに **ネイティブ クライアント** を追加します。
* [ポリシーを作成し](active-directory-b2c-reference-policies.md) 、ポリシー名を取得します。




<!--HONumber=Nov16_HO3-->


