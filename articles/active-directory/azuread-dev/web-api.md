---
title: Azure Active Directory の Web API アプリ
description: Web API アプリケーションについて、およびこの種のアプリのプロトコル フロー、登録、およびトークンの有効期限の基本について説明します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154425"
---
# <a name="web-api"></a>Web API

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web API アプリケーションは、Web API からリソースを取得する必要がある Web アプリケーションです。 このシナリオでは、Web アプリケーションは、認証と Web API の呼び出しに 2 種類の ID を使用できます。

- **アプリケーション ID**: このシナリオでは OAuth 2.0 クライアント資格情報付与を使用して、アプリケーションとして認証し、Web API にアクセスできます。 アプリケーション ID を使用すると、Web API はユーザーに関する情報を受け取らないので、Web アプリケーションから呼び出されていることだけを検出できます。 アプリケーションがユーザーに関する情報を受け取った場合、その情報はアプリケーション プロトコルによって送信されるので、Azure AD による署名は行われません。 Web API は、Web アプリケーションがユーザーを認証済みであることを信頼します。 そのため、このパターンは信頼されたサブシステムと呼ばれます。
- **委任ユーザー ID**: OpenID Connect と、秘密のクライアントを使用した OAuth 2.0 認証コード付与の 2 とおりの方法でシナリオを実行できます。 Web アプリケーションは、ユーザーのアクセス トークンを取得することで、ユーザーが Web アプリケーションに対して正常に認証されたことと、Web アプリケーションが Web API を呼び出すために委任ユーザー ID を取得できたことを Web API に証明します。 このアクセス トークンが要求で Web API に送信されます。Web API がユーザーを認証し、目的のリソースを返します。

以下のフローでは、アプリケーション ID と委任ユーザー ID の両方について説明します。 これらの ID の重要な違いは、委任ユーザー ID では、まず承認コードを取得しておかないと、ユーザーがサインインし、Web API にアクセスすることはできない点です。

## <a name="diagram"></a>ダイアグラム

![Web アプリケーション対 Web API のダイアグラム](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>プロトコル フロー

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>アプリケーション ID と OAuth 2.0 クライアント資格情報付与

1. ユーザーは、Web アプリケーションで Azure AD にサインインします (詳細については、**Web アプリ** に関するセクションを参照してください)。
1. Web アプリケーションは、Web API に対して認証し、目的のリソースを取得できるように、アクセス トークンを取得する必要があります。 Web アプリケーションは、資格情報、アプリケーション ID、Web API のアプリケーション ID の URI を提供して、Azure AD のトークン エンドポイントに要求を送信します。
1. Azure AD がアプリケーションを認証し、Web API の呼び出しに使用する JWT アクセス トークンを返します。
1. Web アプリケーションでは、HTTPS 経由で返された JWT アクセス トークンを使用して、Web API への要求の Authorization ヘッダーに、"Bearer" を指定した JWT 文字列を追加します。 その後、Web API が JWT を検証します。検証が正常に行われると、目的のリソースが返されます。

### <a name="delegated-user-identity-with-openid-connect"></a>委任ユーザー ID と OpenID Connect

1. ユーザーが Azure AD を使用して Web アプリケーションにサインインしています (前述の「Web ブラウザー対 Web アプリケーション」セクションを参照)。 Web アプリケーションのユーザーは、Web アプリケーションがユーザーに代わって Web API を呼び出すことを許可することにまだ同意していない場合、これに同意する必要があります。 アプリケーションが必要なアクセス許可を表示します。そのいずれかが管理者レベルのアクセス許可の場合、ディレクトリ内の通常のユーザーが同意することはできません。 シングル テナント アプリケーションには、必要なアクセス許可が既にあると考えられるので、この同意プロセスはマルチテナント アプリケーションにのみ適用されます。 ユーザーがサインインしたときに、Web アプリケーションは、ユーザーに関する情報が含まれた ID トークンと、認証コードを受信しています。
1. Azure AD によって発行された認証コードを使用して、Web アプリケーションは、認証コード、クライアント アプリケーションの詳細 (アプリケーション ID とリダイレクト URI)、目的のリソース (Web API のアプリケーション ID の URI) を要求に含めて Azure AD のトークン エンドポイントに送信します。
1. 認証コードおよび Web アプリケーションと Web API に関する情報が Azure AD によって検証されます。 検証が正常に行われると、Azure AD は JWT アクセス トークンと JWT 更新トークンの 2 つのトークンを返します。
1. Web アプリケーションでは、HTTPS 経由で返された JWT アクセス トークンを使用して、Web API への要求の Authorization ヘッダーに、"Bearer" を指定した JWT 文字列を追加します。 その後、Web API が JWT を検証します。検証が正常に行われると、目的のリソースが返されます。

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>委任ユーザー ID と OAuth 2.0 承認コードの付与

1. ユーザーは、認証メカニズムが Azure AD とは無関係の Web アプリケーションに既にサインインしています。
1. Web アプリケーションは、アクセス トークンを取得するために認証コードを必要とします。そのため、認証の成功後、Web アプリケーションのアプリケーション ID とリダイレクト URI を提供して、Azure AD の認証エンドポイントにブラウザーを介して要求を発行します。 ユーザーが Azure AD にサインインします。
1. Web アプリケーションのユーザーは、Web アプリケーションがユーザーに代わって Web API を呼び出すことを許可することにまだ同意していない場合、これに同意する必要があります。 アプリケーションが必要なアクセス許可を表示します。そのいずれかが管理者レベルのアクセス許可の場合、ディレクトリ内の通常のユーザーが同意することはできません。 この同意は、シングル テナントおよびマルチ テナント アプリケーションに適用されます。 シングル テナントの場合、管理者は、そのユーザーに代わって管理者の同意を実行できます。 これは、[Azure portal](https://portal.azure.com) で `Grant Permissions` ボタンをクリックして実行します。 
1. ユーザーが同意したら、Web アプリケーションは、アクセス トークンを取得するために必要な認証コードを受信します。
1. Azure AD によって発行された認証コードを使用して、Web アプリケーションは、認証コード、クライアント アプリケーションの詳細 (アプリケーション ID とリダイレクト URI)、目的のリソース (Web API のアプリケーション ID の URI) を要求に含めて Azure AD のトークン エンドポイントに送信します。
1. 認証コードおよび Web アプリケーションと Web API に関する情報が Azure AD によって検証されます。 検証が正常に行われると、Azure AD は JWT アクセス トークンと JWT 更新トークンの 2 つのトークンを返します。
1. Web アプリケーションでは、HTTPS 経由で返された JWT アクセス トークンを使用して、Web API への要求の Authorization ヘッダーに、"Bearer" を指定した JWT 文字列を追加します。 その後、Web API が JWT を検証します。検証が正常に行われると、目的のリソースが返されます。

## <a name="code-samples"></a>コード サンプル

Web アプリケーション対 Web API のシナリオのコード サンプルをご覧ください。 新しいサンプルが頻繁に追加されているので、頻繁に確認してください。 [Web アプリケーション対 Web API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity)

## <a name="app-registration"></a>アプリの登録

Azure AD v1.0 エンドポイントにアプリケーションを登録するには、[アプリの登録](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)に関するページをご覧ください。

* シングル テナント: アプリケーション ID と委任ユーザー ID のどちらの場合も、Web アプリケーションと Web API を Azure AD の同じディレクトリに登録する必要があります。 Web アプリケーションによるリソースへのアクセスを制限するために使用する一連のアクセス許可を公開するように Web API を構成できます。 委任ユーザー ID を使用している場合、Web アプリケーションで Azure portal の **[他のアプリケーションに対するアクセス許可]** ドロップダウン メニューから目的のアクセス許可を選択する必要があります。 アプリケーション ID を使用している場合、この手順は不要です。
* マルチテナント: まず、アプリケーションが機能するために必要なアクセス許可を示すように、Web アプリケーションを構成します。 必要なアクセス許可のこのリストは、アプリケーションを組織で使用できるように、発行先ディレクトリ内のユーザーまたは管理者がアプリケーションに同意するときにダイアログに表示されます。 組織内の任意のユーザーが同意できる、ユーザーレベルのアクセス許可だけを必要とするアプリケーションもあれば、 組織内のユーザーは同意できない、管理者レベルのアクセス許可を必要とするアプリケーションもあります。 このレベルのアクセスを必要とするアプリケーションに同意できるのはディレクトリ管理者だけです。 ユーザーまたは管理者が同意すると、Web アプリケーションと Web API の両方がディレクトリに登録されます。

## <a name="token-expiration"></a>トークンの有効期限

Web アプリケーションは、認証コードを使用して JWT アクセス トークンを取得するときに、JWT 更新トークンも受信します。 アクセス トークンの有効期限が切れたときは、ユーザーに再度サインインを要求しなくても、更新トークンを使用してユーザーを再認証できます。 この更新トークンがユーザーの認証に使用されると、新しいアクセス トークンと更新トークンが取得されます。

## <a name="next-steps"></a>次のステップ

- その他の[アプリケーションの種類とシナリオ](app-types.md)について学習する
- Azure AD [認証の基本](v1-authentication-scenarios.md)について学習する
