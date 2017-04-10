---
title: "Azure Active Directory B2C: アクセス トークンの要求 | Microsoft Docs"
description: "この記事では、クライアント アプリケーションをセットアップし、アクセス トークンを取得する方法について説明します。"
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 5d15ad7a4e75410390891b5e11f72c6a649ebf53
ms.lasthandoff: 03/23/2017


---


# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: アクセス トークンの要求


(**access\_token** で示される) アクセス トークンとは、[承認サーバー](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics)によってセキュリティ保護されているリソース (Web API など) にアクセスするためにクライアントが使用できるセキュリティ トークンの形式です。 アクセス トークンは [JWT](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) として表され、対象となるリソース サーバーと、付与されるサーバーへのアクセス許可に関する情報が含まれます。 リソース サーバーを呼び出すときは、HTTP 要求でアクセス トークンを提示する必要があります。

この記事では、クライアント アプリケーションを構成し、`authorize` エンドポイントと `token` エンドポイントから **access\_token** を取得するための要求を行う方法について説明します。

## <a name="prerequisite"></a>前提条件

アクセス トークンを要求する前に、Web API を登録し、クライアント アプリケーションに付与できるアクセス許可を発行しておく必要があります。 まず、「[Web API の登録](active-directory-b2c-app-registration.md)」の手順に従ってください。

## <a name="granting-permissions-to-a-web-api"></a>Web API へのアクセス許可の付与

クライアント アプリケーションが API への特定のアクセス許可を取得するには、Azure Portal でクライアント アプリケーションにそれらのアクセス許可を付与する必要があります。 クライアント アプリケーションにアクセス許可を付与するには、次の手順に従います。

1. B2C 機能ブレードの **[アプリケーション]** メニューに移動します。
2. クライアント アプリケーションをクリックします (アプリケーションがない場合は、[アプリケーションを登録](active-directory-b2c-app-registration.md)します)。
3. **[API アクセス]** を選択します。
4. **[追加]** をクリックします。
5. Web API と付与するスコープ (アクセス許可) を選択します。
6. **[OK]**をクリックします。

> [!NOTE]
> Azure AD B2C がクライアント アプリケーション ユーザーに同意を求めることはありません。 代わりに、上記のアプリケーション間で構成されたアクセス許可に基づいて、すべて管理者が同意します。 アプリケーションに付与されたアクセス許可を取り消すと、そのアクセス許可をこれまで取得できていたすべてのユーザーがアクセス許可を取得できなくなります。

## <a name="requesting-a-token"></a>トークンの要求

リソース アプリケーションのアクセス トークンを取得するには、クライアント アプリケーションが要求の **scope** パラメーターに必要なアクセス許可を指定する必要があります。 たとえば、アプリケーション ID URI が `https://contoso.onmicrosoft.com/notes` であるリソース アプリケーションに対する "読み取り" アクセス許可を取得する場合、スコープは `https://contoso.onmicrosoft.com/notes/read` になります。 `authorize` エンドポイントに対する承認コード要求の例を次に示します。

```
https://login.microsoftonline.com/<yourTenantId>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

同じ要求で複数のアクセス許可を取得するには、1 つの **scope** パラメーターに複数のエントリをスペースで区切って追加します。 次に例を示します。

デコードされた URL:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

エンコードされた URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

リソースに対するスコープ/アクセス許可を要求するときに、クライアント アプリケーションに付与するものよりも多くのスコープ/アクセス許可を要求できます。 この場合、少なくとも 1 つのアクセス許可が付与されていれば、呼び出しは成功します。 返される **access\_token** には、正常に付与されたアクセス許可だけが設定された "scp" 要求が含まれます。

> [!NOTE] 
> 1 つの要求で 2 つの異なる Web リソースに対するアクセス許可を要求することはできません。 このような要求は失敗します。

### <a name="special-cases"></a>特殊なケース

OpenID Connect 標準では、いくつかの特別な "scope" 値を指定します。 次の特別なスコープは、"ユーザーのプロファイルにアクセスする" ためのアクセス許可を表します。

* **openid**: ID トークンを要求します。
* **offline\_access**: 更新トークンを要求します (認証コード フローを使用)。

`authorize` 要求の "response\_type" パラメーターに "token" が含まれている場合、"scope" パラメーターには、付与するリソースへのアクセス許可 ("openid" および "offline\_access" 以外) が少なくとも 1 つは含まれている必要があります。 含まれていない場合、`authorize` 要求はエラーで終了します。

## <a name="the-returned-token"></a>返されるトークン

(`authorize` または `token` エンドポイントから) 正常に発行された **access\_token** では、次の要求が提示されます。

| 名前 | 要求 | 説明 |
| --- | --- | --- |
|対象となる読者 |`aud` |トークンによってアクセスが許可される 1 つのリソースの\*アプリケーション ID\*。 |
|Scope |`scp` |付与されるリソースへのアクセス許可。 付与される複数のアクセス許可はスペースで区切られます。 |
|Authorized Party |`azp` |要求を開始したクライアント アプリケーションの\*アプリケーション ID\*。 |

API は、**access\_token** を受け取ったら、[トークンを検証](active-directory-b2c-reference-tokens.md)して、トークンが認証済みであり、適切な要求が含まれていることを証明する必要があります。

ご意見とご提案をお待ちしております。 このトピックに問題がある場合、またはこのコンテンツを改善するためのご提案がある場合には、ページの下部でフィードバックを送信できます。 機能についてのご要望は、[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) までお寄せください。
