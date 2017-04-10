---
title: "Azure Active Directory v2.0 エンドポイントのアプリの種類 | Microsoft Docs"
description: "Azure Active Directory v2.0 エンドポイントでサポートされているアプリの種類とシナリオ。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 3e0bb32a6c60011d71606c896cc506f430bc3c27
ms.openlocfilehash: 86055605be6fe264fcb53b26d87a36bcba568a53


---
# <a name="app-types-for-the-azure-active-directory-v20-endpoint"></a>Azure Active Directory v2.0 エンドポイントのアプリの種類
Azure Active Directory (Azure AD) v2.0 エンドポイントは、さまざまな最新アプリ アーキテクチャ向けの認証をサポートしています。そのいずれも、業界標準のプロトコルである [OAuth 2.0 または OpenID Connect](active-directory-v2-protocols.md) に基づいています。 この記事では、使用する言語やプラットフォームを問わず、Azure AD v2.0 を使用して作成できるアプリの種類について説明します。 この記事の情報は、[コードを詳しく確認する](active-directory-appmodel-v2-overview.md#getting-started)前に大まかなシナリオを理解するうえで役立ちます。

> [!NOTE]
> Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。 v2.0 エンドポイントを使用する必要があるかどうかを判断するには、[v2.0 の制限事項](active-directory-v2-limitations.md)に関する記事を参照してください。
> 
> 

## <a name="the-basics"></a>基本
v2.0 エンドポイントを使うアプリは、すべて [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com)で登録する必要があります。 アプリの登録プロセスでは、次の値が収集され、対象のアプリに割り当てられます。

* アプリを一意に識別する**アプリケーション ID**。
* 応答をアプリにリダイレクトして戻すために使用できる**リダイレクト URI**。
* その他シナリオに応じた値。

詳細については、[アプリの登録](active-directory-v2-app-registration.md)方法を参照してください。

登録の済んだアプリは、Azure AD v2.0 エンドポイントに要求を送ることによって、Azure AD と通信します。 これらの要求の詳細に対処するオープン ソース フレームワークとライブラリをご用意しています。 これらのエンドポイントへの要求を作成して、自分で認証ロジックを実装してもかまいません。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

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

アプリで利用できるすべての種類のトークンと要求については、[v2.0 トークン リファレンス](active-directory-v2-tokens.md)を参照してください。

Web サーバー アプリにおけるサインイン認証フローは、主に次のステップで構成されます。

![Web アプリの認証フロー](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

v2.0 エンドポイントから受け取った公開署名キーを使用して ID トークンを検証することにより、ユーザーの ID を確認することができます。 以降のページ要求でユーザーを識別するために使用できるセッション Cookie が設定されます。

このシナリオを実際に確認するには、v2.0 の「[Getting Started (概要)](active-directory-appmodel-v2-overview.md#getting-started)」セクションのいずれかの Web アプリ サインイン コード サンプルを試してください。

Web サーバー アプリは、単純なサインインを実行するだけでなく、REST API をはじめとする他の Web サービスにアクセスすることが必要な場合があります。 この場合、Web サーバー アプリは、OpenID Connect と OAuth 2.0 を組み合わせたフローに関与します。その際使用されるのが [OAuth 2.0 承認コード フロー](active-directory-v2-protocols.md)です。 このシナリオの詳細については、[Web アプリと Web API の使用の開始](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)に関する記事を参照してください。

## <a name="web-apis"></a>Web API
v2.0 エンドポイントを使用すると、アプリの RESTful Web API などの Web サービスをセキュリティで保護できます。 Web API では、ID トークンとセッション Cookie の代わりに OAuth 2.0 アクセス トークンを使ってデータをセキュリティ保護し、受信要求を認証します。 Web API の呼び出し元は、HTTP 要求の承認ヘッダーの末尾にアクセス トークンを次のように追加します。

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API はそのアクセス トークンを使って API の呼び出し元の ID を検証し、アクセス トークンでエンコードされている要求から呼び出し元に関する情報を抽出します。 アプリで利用できるすべての種類のトークンと要求については、[v2.0 トークン リファレンス](active-directory-v2-tokens.md)を参照してください。

Web API は、アクセス許可 ([スコープ](active-directory-v2-scopes.md)とも呼ばれる) を公開することで、特定の機能またはデータをオプトイン/オプトアウトできるようにします。 呼び出し元のアプリがスコープに対するアクセス許可を取得するには、ユーザーがフローの途中でスコープに同意する必要があります。 v2.0 エンドポイントはユーザーにアクセス許可を求め、Web API が受け取るすべてのアクセス トークンにアクセス許可を記録します。 Web API は、呼び出しごとに受け取るアクセス トークンを検証し、承認チェックを行います。

Web API は、すべての種類のアプリ (Web サーバー アプリ、デスクトップ アプリとモバイル アプリ、シングル ページ アプリ、サーバー側のデーモン、さらにそれ以外の Web API など) からアクセス トークンを受信できます。 Web API 認証の大まかな流れは次のとおりです。

![Web API 認証フロー](../../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

承認コード、更新トークン、およびアクセス トークンの取得手順については、[OAuth 2.0 プロトコル](active-directory-v2-protocols-oauth-code.md)に関する記事を参照してください。

OAuth2 アクセス トークンを使用して Web API をセキュリティ保護する方法については、「[Getting Started (概要)](active-directory-appmodel-v2-overview.md#getting-started)」セクションで Web API コード サンプルを確認してください。

## <a name="mobile-and-native-apps"></a>モバイル アプリとネイティブ アプリ
多くの場合、モバイル アプリやデスクトップ アプリなど、デバイスにインストールされているアプリは、データを格納し、ユーザーの代わりにさまざまな処理を実行するバックエンド サービスや Web API にアクセスする必要があります。 これらのアプリは、[OAuth 2.0 承認コード フロー](active-directory-v2-protocols-oauth-code.md)を使ってバックエンド サービスにサインインと承認を追加します。

このフローでは、ユーザーがサインインすると、アプリは v2.0 エンドポイントから承認コードを受け取ります。 承認コードは、サインインしているユーザーに代わってバックエンド サービスを呼び出すためのアプリのアクセス許可を表します。 アプリはバックグラウンドで承認コードを OAuth 2.0 のアクセス トークンおよび更新トークンと交換します。 アプリはそのアクセス トークンを使って HTTP 要求で Web API に対して認証を実行します。また、アクセス トークンの有効期限が切れた場合は、更新トークンを使って新しいアクセス トークンを取得します。

![ネイティブ アプリの認証フロー](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>シングル ページ アプリ (JavaScript)
最新アプリの多くには、主に JavaScript で記述されたシングル ページ アプリのフロントエンドがあります。 AngularJS、Ember.js、または Durandal.js などのフレームワークを使用して記述されていることも少なくありません。 Azure AD v2.0 エンドポイントは、[OAuth 2.0 暗黙的フロー](active-directory-v2-protocols-implicit.md)を使用してこれらのアプリをサポートします。

このフローでは、アプリは v2.0 認証エンドポイントから直接トークンを受け取るため、サーバー間の交換は実行されません。 すべての認証ロジックとセッション処理は、余分なページのリダイレクトなしに、JavaScript クライアント内ですべて行うことができます。

![暗黙的な認証フロー](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

このシナリオを実際に確認するには、「[Getting Started (概要)](active-directory-appmodel-v2-overview.md#getting-started)」セクションのいずれかのシングル ページ アプリのコード サンプルを試してください。

### <a name="daemons-and-server-side-apps"></a>デーモンおよびサーバー側のアプリ
長時間実行されるプロセスを含んだアプリや、ユーザーの介入なしで動作するアプリも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。 これらのアプリは、OAuth 2.0 クライアント資格情報フローで (ユーザーの委任 ID ではなく) アプリの ID を使用して認証を行い、トークンを取得することができます。

このフローでは、アプリは `/token` エンドポイントと直接対話してトークンを取得します。

![デーモン アプリの認証フロー](../../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

デーモン アプリを作成するには、「[Getting Started (概要)](active-directory-appmodel-v2-overview.md#getting-started)」セクションのクライアントの資格情報に関する記述を参照するか、または [.NET サンプル アプリ](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)をお試しください。

## <a name="current-limitations"></a>現時点での制限事項
現時点では、このセクションのアプリの種類は v2.0 エンドポイントでサポートされていませんが、今後の開発で対応が予定されています。 v2.0 エンドポイントのその他の制限事項については、[v2.0 の制限事項](active-directory-v2-limitations.md)に関する記事を参照してください。

### <a name="chained-web-apis-on-behalf-of"></a>Web API の連鎖 (On-Behalf-Of)
v2.0 エンドポイントによって保護された Web API から、同じように保護された別の下流の Web API を呼び出す手法は、多くのアーキテクチャで使われています。 このシナリオは、バックエンドの Web API から Microsoft Online サービス (Office 365、Graph API など) のインスタンスを呼び出すネイティブ クライアントでよく見られます。

このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 JSON Web Token (JWT) Bearer Credential Grant ([On-Behalf-Of フロー](active-directory-v2-protocols.md)) を使用してサポートできます。 現時点では、v2.0 エンドポイントに On-Behalf-Of フローは実装されていません。 一般提供版 Azure AD サービスにおけるこのフローの動作については、[GitHub の On-Behalf-Of コード サンプル](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)を参照してください。




<!--HONumber=Jan17_HO3-->


