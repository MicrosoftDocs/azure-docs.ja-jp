---
title: Azure Active Directory B2C でのアクセス トークンの要求 | Microsoft Docs
description: この記事では、クライアント アプリケーションをセットアップし、アクセス トークンを取得する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 0ea781188e40d6389da8188379d792c922d3bdca
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768345"
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C:アクセス トークンの要求

アクセス トークン (Azure AD B2C からの応答では **access\_token** として示されます) とは、 [承認サーバー](active-directory-b2c-reference-protocols.md)によってセキュリティ保護されているリソース (Web API など) にアクセスするためにクライアントが使用できるセキュリティ トークンの形式です。 アクセス トークンは [JWT](active-directory-b2c-reference-tokens.md) として表され、対象となるリソース サーバーと、付与されるサーバーへのアクセス許可に関する情報が含まれます。 リソース サーバーを呼び出すときは、HTTP 要求でアクセス トークンを提示する必要があります。

この記事では、**アクセス\_ トークン**を取得するためにクライアント アプリケーションと Web API を構成する方法について説明します。

> [!NOTE]
> **Web API チェーン (On-Behalf-Of) は Azure AD B2C でサポートされていません。**
>
> 多くのアーキテクチャには、別のダウンストリーム Web API を呼び出す必要がある Web API が含まれており、これらの API は、共に Azure AD B2C によってセキュリティ保護されています。 このシナリオは、バックエンドの Web API から Microsoft オンライン サービス (Azure AD Graph API など) を順に呼び出すネイティブ クライアントでよく見られます。
>
> このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 JWT Bearer Credential Grant (On-Behalf-Of フロー) を使用してサポートできます。 ただし、現時点では、Azure AD B2C に On-Behalf-Of フローは実装されていません。

## <a name="register-a-web-api-and-publish-permissions"></a>Web API の登録とアクセス許可の発行

アクセス トークンを要求する前に、Web API を登録し、クライアント アプリケーションに付与できるアクセス許可 (スコープ) を発行しておく必要があります。

### <a name="register-a-web-api"></a>Web API の登録

1. Azure Portal の Azure AD B2C 機能メニューで、**[アプリケーション]** をクリックします。
2. メニューの上部にある **[+追加]** をクリックします。
3. アプリケーションの **[名前]** には、コンシューマーがアプリケーションの機能を把握できるような名前を設定します。 たとえば、「Contoso API」と入力します。
4. **[Include web app / web API (Web アプリ/Web API を含める)]** スイッチを **[はい]** に切り替えます。
5. **[返信 URL]** に任意の値を入力します。 たとえば、「 `https://localhost:44316/`」のように入力します。 API は Azure AD B2C から直接トークンを受け取らないため、この値は重要ではありません。
6. **[アプリケーション ID/URI]** を入力します。 これは Web API に使用される ID です。 たとえば、ボックスに「notes」と入力します。 **[アプリケーション ID/URI]** は `https://{tenantName}.onmicrosoft.com/notes` となります。
7. **[作成]** をクリックして、アプリケーションを登録します。
8. 作成したアプリケーションをクリックし、後でコードの作成時に使用するために、グローバルに一意の **アプリケーション クライアント ID** をメモしておきます。

### <a name="publishing-permissions"></a>アクセス許可の発行

スコープはアクセス許可に似ていますが、これはアプリが API を呼び出すときに必要となります。 スコープの例には "読み取り" や "書き込み" などがあります。 Web アプリまたはネイティブ アプリで API から "読み取り" を行うとします。 アプリは Azure AD B2C を呼び出し、"読み取り" スコープにアクセスするためのアクセス トークンを要求します。 Azure AD B2C でこのようなアクセス トークンを発行するには、特定の API からの "読み取り" アクセス許可がアプリに付与されている必要があります。 これを行うには、まず API で "読み取り" スコープを発行する必要があります。

1. Azure AD B2C の **[アプリケーション]** メニュー内で、Web API アプリケーション ("Contoso API") を開きます。
2. **[Published scopes (公開スコープ)]** をクリックします。 ここで、他のアプリケーションに付与できるアクセス許可 (スコープ) を定義します。
3. 必要に応じて**スコープ値**を追加します (たとえば、"read")。 既定では、"user_impersonation" スコープが定義されます。 これは、不要であれば無視できます。 **[スコープ名]** 列にスコープの説明を入力します。
4. **[Save]** をクリックします。

> [!IMPORTANT]
> **[スコープ名]** は、**スコープ値**の説明です。 スコープを使用する場合は、必ず**スコープ値**を使用してください。

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>ネイティブ アプリまたは Web アプリに対する Web API へのアクセス許可の付与

API を構成してスコープを発行したら、Azure Portal を使用して、クライアント アプリケーションにこれらのスコープへのアクセス許可を付与する必要があります。

1. Azure AD B2C 機能メニューの **[アプリケーション]** メニューに移動します。
2. まだであれば、クライアント アプリケーション ([Web アプリ](active-directory-b2c-app-registration.md)または[ネイティブ クライアント](active-directory-b2c-app-registration.md)) を登録します。 出発点としてこのガイドを利用している場合は、クライアント アプリケーションを登録する必要があります。
3. **[API アクセス]** をクリックします。
4. **[追加]** をクリックします。
5. Web API と付与するスコープ (アクセス許可) を選択します。
6. Click **OK**.

> [!NOTE]
> Azure AD B2C がクライアント アプリケーション ユーザーに同意を求めることはありません。 代わりに、上記のアプリケーション間で構成されたアクセス許可に基づいて、すべて管理者が同意します。 アプリケーションに付与されたアクセス許可を取り消すと、そのアクセス許可をこれまで取得できていたすべてのユーザーがアクセス許可を取得できなくなります。

## <a name="requesting-a-token"></a>トークンの要求

アクセス トークンを要求するには、クライアント アプリケーションで、必要なアクセス許可を要求の **scope** パラメーターに指定する必要があります。 たとえば、`https://contoso.onmicrosoft.com/notes` の **[アプリケーション ID/URI]** を持つ API に**スコープ値** "read" を指定する場合、スコープは `https://contoso.onmicrosoft.com/notes/read` になります。 `/authorize` エンドポイントに対する承認コード要求の例を次に示します。

> [!NOTE]
> 現在、カスタム ドメインとアクセス トークンの併用はサポートされていません。 要求 URL には、tenantName.onmicrosoft.com ドメインを使用する必要があります。

次の例では、これらの値を置き換えます。

- `<tenant-name>` - Azure AD B2C テナントの名前。
- `<policy-name>` - カスタム ポリシーまたはユーザー フローの名前。
- `<application-ID>` - 登録したクライアント アプリケーションのアプリケーション識別子。
- `<redirect-uri>` - クライアント アプリケーションを登録したときに入力した**リダイレクト URI**。

```
https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?client_id=<application-ID>&nonce=anyRandomValue&redirect_uri=<redirect_uri>&scope=https%3A%2F%2F<tenant-name>.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

同じ要求で複数のアクセス許可を取得するには、1 つの **scope** パラメーターに複数のエントリをスペースで区切って追加します。 例: 

デコードされた URL:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

エンコードされた URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

リソースに対しては、クライアント アプリケーションに付与されているスコープよりも多くのスコープ (アクセス許可) を要求できます。 この場合、少なくとも 1 つのアクセス許可が付与されていれば、呼び出しは成功します。 返される **access\_token** には、正常に付与されたアクセス許可だけが設定された "scp" 要求が含まれます。

> [!NOTE] 
> 1 つの要求で 2 つの異なる Web リソースに対するアクセス許可を要求することはできません。 このような要求は失敗します。

### <a name="special-cases"></a>特殊なケース

OpenID Connect 標準では、いくつかの特別な "scope" 値を指定します。 次の特別なスコープは、"ユーザーのプロファイルにアクセスする" ためのアクセス許可を表します。

* **openid**: ID トークンを要求します
* **offline\_access**: 更新トークンを要求します ([認証コード フロー](active-directory-b2c-reference-oauth-code.md)を使用)。

`/authorize` 要求の `response_type` パラメーターに `token` が含まれる場合、`scope` パラメーターには、付与されるリソース スコープ (`openid` と `offline_access` 以外) を少なくとも 1 つ含める必要があります。 含まれていない場合、`/authorize` 要求はエラーで終了します。

## <a name="the-returned-token"></a>返されるトークン

(`/authorize` または `/token` エンドポイントから) 正常に発行された **access\_token** では、次の要求が提示されます。

| Name | 要求 | 説明 |
| --- | --- | --- |
|対象ユーザー |`aud` |トークンによってアクセスが許可される 1 つのリソースの**アプリケーション ID**。 |
|Scope (スコープ) |`scp` |付与されるリソースへのアクセス許可。 付与される複数のアクセス許可はスペースで区切られます。 |
|Authorized Party |`azp` |要求を開始したクライアント アプリケーションの**アプリケーション ID**。 |

API は、**access\_token** を受け取ったら、[トークンを検証](active-directory-b2c-reference-tokens.md)して、トークンが認証済みであり、適切な要求が含まれていることを証明する必要があります。

ご意見とご提案をお待ちしております。 このトピックで問題が発生した場合は、Stack Overflow にタグ ['azure-ad-b2c'](https://stackoverflow.com/questions/tagged/azure-ad-b2c) を使用して投稿してください。 機能についてのご要望は、[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) までお寄せください。

## <a name="next-steps"></a>次の手順

* [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi) を使用した Web API の構築
* [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) を使用した Web API の構築
