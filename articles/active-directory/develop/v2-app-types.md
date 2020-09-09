---
title: Microsoft ID プラットフォームのアプリケーションの種類 | Azure
description: Microsoft ID プラットフォーム (v2.0) エンドポイントでサポートされているアプリの種類とシナリオです。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7b89add55a060c7ba0ef9488f1f6438090b8d3d2
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121172"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Microsoft ID プラットフォームのアプリケーションの種類

Microsoft ID プラットフォーム (v2.0) エンドポイントでは、さまざまな最新アプリ アーキテクチャ向けの認証がサポートされています。そのいずれも、業界標準のプロトコルである [OAuth 2.0 または OpenID Connect](active-directory-v2-protocols.md) に基づいています。 この記事では、使用する言語やプラットフォームを問わず、Microsoft ID プラットフォームを使用して作成できるアプリの種類について説明します。 情報は、[コードを詳しく確認する](v2-overview.md#getting-started)前に大まかなシナリオを理解するうえで役立ちます。

## <a name="the-basics"></a>基本

Microsoft ID プラットフォーム エンドポイントを使う各アプリを、Azure portal の[アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)で登録する必要があります。 アプリの登録プロセスでは、次の値が収集され、対象のアプリに割り当てられます。

* アプリを一意に識別する **アプリケーション (クライアント) ID**
* 応答をアプリにリダイレクトして戻すために使用できる**リダイレクト URI**。
* 他にいくつかのシナリオ固有の値 (サポートされているアカウントの種類など)

詳細については、[アプリの登録](quickstart-register-app.md)方法を参照してください。

登録の済んだアプリは、エンドポイントに要求を送ることによって、Microsoft ID プラットフォームと通信します。 これらの要求の詳細に対処するオープン ソース フレームワークとライブラリをご用意しています。 これらのエンドポイントへの要求を作成して、自分で認証ロジックを実装してもかまいません。

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>シングル ページ アプリ (JavaScript)

最新アプリの多くには、主に JavaScript で記述されたシングル ページ アプリのフロントエンドがあり、Angular、React、Vue などのフレームワークと共に使用されることもあります。 Microsoft ID プラットフォーム エンドポイントでは、[OAuth 2.0 認証コード フロー](v2-oauth2-auth-code-flow.md)を使用してこれらのアプリがサポートされます。

このフローでは、アプリは Microsoft ID プラットフォームの `authorize` エンドポイントからコードを受け取り、クロス サイト Web 要求を使用してトークンと更新トークンに引き換えます。 更新トークンは 24 時間ごとに期限切れになるため、アプリは別のコードを要求する必要があります。

![SPA アプリのコード フロー](media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.png)

実際のシナリオについては、[認証コード フローを使用して、ユーザーをサインインし、JavaScript SPA から Microsoft Graph API を呼び出すチュートリアル](tutorial-v2-javascript-auth-code.md)を参照してください。

### <a name="authorization-code-flow-vs-implicit-flow"></a>認証コード フローと暗黙的なフロー

これまでの OAuth 2.0 のほとんどで、シングルページ アプリを作成するために推奨される方法は[暗黙的なフロー](v2-oauth2-implicit-grant-flow.md)でした。 [サード パーティの Cookie](reference-third-party-cookies-spas.md) が削除され、暗黙的なフローに関するセキュリティ上の懸念が[高まってきた](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)ことから、シングルページ アプリの認証コード フローに移行しました。

Safari およびその他のプライバシーを意識したブラウザーでアプリの互換性を確保するために、暗黙的なフローを使用することは推奨しなくなりました。代わりに、認証コード フローを推奨します。

## <a name="web-apps"></a>Web Apps

ブラウザーからアクセスされる Web アプリ (.NET、PHP、Java、Ruby、Python、Node) の場合、ユーザーのサインインに [OpenID Connect](active-directory-v2-protocols.md) を使うことができます。 OpenID Connect では、Web アプリは ID トークンを受け取ります。 ID トークンは、ユーザーの ID を検証し、要求の形でユーザーに関する情報を提供するセキュリティ トークンです。

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Microsoft ID プラットフォーム エンドポイントで使われている各種トークンについて詳しくは、[アクセス トークン](access-tokens.md)および [id_token](id-tokens.md) のリファレンス記事をご覧ください

Web サーバー アプリにおけるサインイン認証フローは、主に次のステップで構成されます。

![Web アプリの認証フロー](./media/v2-app-types/convergence-scenarios-webapp.svg)

Microsoft ID プラットフォーム エンドポイントから受け取った公開署名キーを使用して ID トークンを検証することにより、ユーザーの ID を確認することができます。 以降のページ要求でユーザーを識別するために使用できるセッション Cookie が設定されます。

このシナリオを実際に確認するには、[Microsoft ID プラットフォームの使用の開始](v2-overview.md#getting-started)に関するセクションの、いずれかの Web アプリ サインインのコード サンプルを試してください。

Web サーバー アプリは、単純なサインインを実行するだけでなく、REST API をはじめとする他の Web サービスにアクセスすることが必要な場合があります。 この場合、Web サーバー アプリは、OpenID Connect と OAuth 2.0 を組み合わせたフローに関与します。その際使用されるのが [OAuth 2.0 承認コード フロー](v2-oauth2-auth-code-flow.md)です。 このシナリオの詳細については、[Web アプリと Web API の使用の開始](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet)に関する記事を参照してください。


## <a name="web-apis"></a>Web API

Microsoft ID プラットフォーム エンドポイントを使用すると、アプリの RESTful Web API などの Web サービスをセキュリティで保護できます。 Web API は、さまざまなプラットフォームや言語で実装できます。 また、Azure Functions で HTTP トリガーを使用して実装することもできます。 Web API では、ID トークンとセッション Cookie の代わりに OAuth 2.0 アクセス トークンを使って、そのデータをセキュリティで保護し、受信要求を認証します。 Web API の呼び出し元によって、次のように HTTP 要求の Authorization ヘッダーにアクセス トークンが追加されます。

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API では、そのアクセス トークンを使って API の呼び出し元の ID を検証し、アクセス トークンでエンコードされている要求から呼び出し元に関する情報を抽出します。 Microsoft ID プラットフォーム エンドポイントで使われている各種のトークンに関する詳細については、[アクセス トークン](access-tokens.md)および [id_token](id-tokens.md) のリファレンス記事を参照してください。

Web API を使用すると、アクセス許可 ([スコープ](v2-permissions-and-consent.md)とも呼ばれる) を公開することで、ユーザーが特定の機能またはデータをオプトイン/オプトアウトできるようになります。 呼び出し元のアプリがスコープに対するアクセス許可を取得するには、ユーザーがフローの途中でスコープに同意する必要があります。 Microsoft ID プラットフォーム エンドポイントでは、ユーザーにアクセス許可を求め、Web API が受信するすべてのアクセス トークンにアクセス許可を記録します。 Web API では、呼び出しごとに受信するアクセス トークンを検証し、承認チェックを実行します。

Web API では、すべての種類のアプリ (Web サーバー アプリ、デスクトップ アプリとモバイル アプリ、シングル ページ アプリ、サーバー側のデーモン、さらにそれ以外の Web API など) からアクセス トークンを受信できます。 Web API の大まかなフローは次のとおりです。

![Web API の認証フロー](./media/v2-app-types/convergence-scenarios-webapi.svg)

OAuth2 アクセス トークンを使用して Web API をセキュリティで保護する方法については、[Microsoft ID プラットフォームの使用の開始](v2-overview.md#getting-started)に関するセクションの Web API コード サンプルを確認してください。

多くの場合、Web API は Microsoft ID プラットフォームで保護されているその他のダウンストリーム Web API に、送信要求を行う必要もあります。 そのために、Web API では**代理**フローを利用できます。それにより、Web API は受信アクセス トークンを、送信要求で使用される別のアクセス トークンに交換できます。 詳しくは、「[Microsoft ID プラットフォームと OAuth 2.0 On-Behalf-Of フロー](v2-oauth2-on-behalf-of-flow.md)」をご覧ください。

## <a name="mobile-and-native-apps"></a>モバイル アプリとネイティブ アプリ

多くの場合、モバイル アプリやデスクトップ アプリなど、デバイスにインストールされているアプリは、データを格納し、ユーザーの代わりにさまざまな機能を実行するバックエンド サービスや Web API にアクセスする必要があります。 これらのアプリは、[OAuth 2.0 承認コード フロー](v2-oauth2-auth-code-flow.md)を使ってバックエンド サービスにサインインと承認を追加します。

このフローでは、ユーザーがサインインすると、アプリは Microsoft ID プラットフォーム エンドポイントから承認コードを受け取ります。 承認コードは、サインインしているユーザーに代わってバックエンド サービスを呼び出すためのアプリのアクセス許可を表します。 アプリはバックグラウンドで承認コードを OAuth 2.0 のアクセス トークンおよび更新トークンと交換します。 アプリではそのアクセス トークンを使って HTTP 要求で Web API を認証できます。また、古いアクセス トークンの有効期限が切れた場合は、更新トークンを使って新しいアクセス トークンを取得できます。

![ネイティブ アプリの認証フロー](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>デーモンおよびサーバー側のアプリ

長時間実行されるプロセスを含むアプリや、ユーザーとのやりとりはなく動作するアプリにも、セキュリティで保護されたリソース (Web API など) にアクセスする方法が必要です。 これらのアプリは、OAuth 2.0 クライアント資格情報フローで (ユーザーの委任 ID ではなく) アプリの ID を使用して認証を行い、トークンを取得することができます。 アプリの ID は、クライアント シークレットまたは証明書を使用して証明することができます。 詳細については、[Microsoft ID プラットフォームを使用する .NET Core デーモン コンソール アプリケーション](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)に関する記事を参照してください。

このフローでは、アプリは `/token` エンドポイントと直接対話してアクセスを取得します。

![デーモン アプリの認証フロー](./media/v2-app-types/convergence-scenarios-daemon.svg)

デーモン アプリを作成するには、[クライアントの資格情報に関する記述](v2-oauth2-client-creds-grant-flow.md)を参照するか、[.NET サンプル アプリ](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)をお試しください。

## <a name="next-steps"></a>次のステップ

Microsoft ID プラットフォームでサポートされているアプリケーションの種類について説明しました。さまざまなシナリオで使用されるプロトコル コンポーネントについて理解を深めるため、[OAuth 2.0 と OpenID Connect](active-directory-v2-protocols.md) の詳細についてご覧ください。