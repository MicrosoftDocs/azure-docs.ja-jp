---
title: Microsoft ID プラットフォームのアプリケーションの種類 | Azure
description: Microsoft ID プラットフォーム (v2.0) エンドポイントでサポートされているアプリの種類とシナリオです。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac3dbd3f2148c14780c380cc0c7f2fab2a41e165
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482398"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Microsoft ID プラットフォームのアプリケーションの種類

Microsoft ID プラットフォーム (v2.0) エンドポイントでは、さまざまな最新アプリ アーキテクチャ向けの認証がサポートされています。そのいずれも、業界標準のプロトコルである [OAuth 2.0 または OpenID Connect](active-directory-v2-protocols.md) に基づいています。 この記事では、使用する言語やプラットフォームを問わず、Microsoft ID プラットフォームを使用して作成できるアプリの種類について説明します。 情報は、[コードを詳しく確認する](v2-overview.md#getting-started)前に大まかなシナリオを理解するうえで役立ちます。

> [!NOTE]
> Microsoft ID プラットフォームのエンドポイントでは、すべての Azure Active Directory (Azure AD) シナリオや機能がサポートされているわけではありません。 Microsoft ID プラットフォームのエンドポイントを使用する必要があるかどうかを判断するには、[MicrosoftID プラットフォームの制限事項](active-directory-v2-limitations.md)に関する記事を参照してください。

## <a name="the-basics"></a>基本

Microsoft ID プラットフォーム エンドポイントを使う各アプリを、新しい[アプリ登録ポータル](https://go.microsoft.com/fwlink/?linkid=2083908)で登録する必要があります。 アプリの登録プロセスでは、次の値が収集され、対象のアプリに割り当てられます。

* アプリを一意に識別する **アプリケーション (クライアント) ID**
* 応答をアプリにリダイレクトして戻すために使用できる**リダイレクト URI**。
* 他にいくつかのシナリオ固有の値 (サポートされているアカウントの種類など)

詳細については、[アプリの登録](quickstart-register-app.md)方法を参照してください。

登録の済んだアプリは、エンドポイントに要求を送ることによって、Microsoft ID プラットフォームと通信します。 これらの要求の詳細に対処するオープン ソース フレームワークとライブラリをご用意しています。 これらのエンドポイントへの要求を作成して、自分で認証ロジックを実装してもかまいません。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>シングル ページ アプリ (JavaScript)

最新アプリの多くには、主に JavaScript で記述されたシングル ページ アプリのフロントエンドがあります。 Angular、React、または Vue などのフレームワークを使用して記述されていることも、少なくありません。 Microsoft ID プラットフォーム エンドポイントでは、[OAuth 2.0 暗黙的フロー](v2-oauth2-implicit-grant-flow.md)を使用してこれらのアプリがサポートされます。

このフローでは、アプリは Microsoft ID プラットフォーム承認エンドポイントから直接トークンを受け取るため、サーバー間の交換は実行されません。 すべての認証ロジックとセッション処理は、余分なページのリダイレクトなしに、JavaScript クライアント内ですべて行うことができます。

![暗黙的な認証フロー](./media/v2-app-types/convergence-scenarios-implicit.svg)

このシナリオを実際に確認するには、[Microsoft ID プラットフォームの使用の開始](v2-overview.md#getting-started)に関するセクションの、いずれかのシングル ページ アプリのコード サンプルを試してください。

## <a name="web-apps"></a>Web Apps

ブラウザーからアクセスされる Web アプリ (.NET、PHP、Java、Ruby、Python、Node) の場合、ユーザーのサインインに [OpenID Connect](active-directory-v2-protocols.md) を使うことができます。 OpenID Connect では、Web アプリは ID トークンを受け取ります。 ID トークンは、ユーザーの ID を検証し、要求の形でユーザーに関する情報を提供するセキュリティ トークンです。

```
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

Web サーバー アプリは、単純なサインインを実行するだけでなく、REST API をはじめとする他の Web サービスにアクセスすることが必要な場合があります。 この場合、Web サーバー アプリは、OpenID Connect と OAuth 2.0 を組み合わせたフローに関与します。その際使用されるのが [OAuth 2.0 承認コード フロー](active-directory-v2-protocols.md)です。 このシナリオの詳細については、[Web アプリと Web API の使用の開始](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)に関する記事を参照してください。

## <a name="web-apis"></a>Web API

Microsoft ID プラットフォーム エンドポイントを使用すると、アプリの RESTful Web API などの Web サービスをセキュリティで保護できます。 Web API では、ID トークンとセッション Cookie の代わりに OAuth 2.0 アクセス トークンを使ってデータをセキュリティ保護し、受信要求を認証します。 Web API の呼び出し元は、HTTP 要求の承認ヘッダーの末尾にアクセス トークンを次のように追加します。

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API はそのアクセス トークンを使って API の呼び出し元の ID を検証し、アクセス トークンでエンコードされている要求から呼び出し元に関する情報を抽出します。 Microsoft ID プラットフォーム エンドポイントで使われている各種トークンについて詳しくは、[アクセス トークン](access-tokens.md)および [id_token](id-tokens.md) のリファレンス記事をご覧ください

Web API は、アクセス許可 ([スコープ](v2-permissions-and-consent.md)とも呼ばれる) を公開することで、特定の機能またはデータをオプトイン/オプトアウトできるようにします。 呼び出し元のアプリがスコープに対するアクセス許可を取得するには、ユーザーがフローの途中でスコープに同意する必要があります。 Microsoft ID プラットフォーム エンドポイントはユーザーにアクセス許可を求め、Web API が受け取るすべてのアクセス トークンにアクセス許可を記録します。 Web API は、呼び出しごとに受け取るアクセス トークンを検証し、承認チェックを行います。

Web API は、すべての種類のアプリ (Web サーバー アプリ、デスクトップ アプリとモバイル アプリ、シングル ページ アプリ、サーバー側のデーモン、さらにそれ以外の Web API など) からアクセス トークンを受信できます。 Web API 認証の大まかな流れは次のとおりです。

![Web API の認証フロー](./media/v2-app-types/convergence-scenarios-webapi.svg)

OAuth2 アクセス トークンを使用して Web API をセキュリティ保護する方法については、[Microsoft ID プラットフォームの使用の開始](v2-overview.md#getting-started)に関するセクションの Web API コード サンプルを確認してください。

多くの場合、Web API は Microsoft ID プラットフォームで保護されているその他のダウンストリーム Web API に、送信要求を行う必要もあります。 そのために、Web API では**代理**フローを利用できます。それにより、Web API は受信アクセス トークンを、送信要求で使用される別のアクセス トークンに交換できます。 詳しくは、「[Microsoft ID プラットフォームと OAuth 2.0 On-Behalf-Of フロー](v2-oauth2-on-behalf-of-flow.md)」をご覧ください。

## <a name="mobile-and-native-apps"></a>モバイル アプリとネイティブ アプリ

多くの場合、モバイル アプリやデスクトップ アプリなど、デバイスにインストールされているアプリは、データを格納し、ユーザーの代わりにさまざまな処理を実行するバックエンド サービスや Web API にアクセスする必要があります。 これらのアプリは、[OAuth 2.0 承認コード フロー](v2-oauth2-auth-code-flow.md)を使ってバックエンド サービスにサインインと承認を追加します。

このフローでは、ユーザーがサインインすると、アプリは Microsoft ID プラットフォーム エンドポイントから承認コードを受け取ります。 承認コードは、サインインしているユーザーに代わってバックエンド サービスを呼び出すためのアプリのアクセス許可を表します。 アプリはバックグラウンドで承認コードを OAuth 2.0 のアクセス トークンおよび更新トークンと交換します。 アプリはそのアクセス トークンを使って HTTP 要求で Web API に対して認証を実行します。また、アクセス トークンの有効期限が切れた場合は、更新トークンを使って新しいアクセス トークンを取得します。

![ネイティブ アプリの認証フロー](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>デーモンおよびサーバー側のアプリ

長時間実行されるプロセスを含んだアプリや、ユーザーの介入なしで動作するアプリも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。 これらのアプリは、OAuth 2.0 クライアント資格情報フローで (ユーザーの委任 ID ではなく) アプリの ID を使用して認証を行い、トークンを取得することができます。 アプリの ID は、クライアント シークレットまたは証明書を使用して証明することができます。 詳しくは、「[Authenticating to Microsoft identity platform in daemon apps with certificates (証明書を使用したデーモン アプリでの Microsoft ID プラットフォームへの認証)](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/)」をご覧ください。

このフローでは、アプリは `/token` エンドポイントと直接対話してアクセスを取得します。

![デーモン アプリの認証フロー](./media/v2-app-types/convergence-scenarios-daemon.svg)

デーモン アプリを作成するには、[クライアントの資格情報に関する記述](v2-oauth2-client-creds-grant-flow.md)を参照するか、[.NET サンプル アプリ](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)をお試しください。
