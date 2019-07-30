---
title: 認証と認可 - Azure App Service | Microsoft Docs
description: Azure App Service の認証/認可の機能の概念リファレンスと概要
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/24/2018
ms.author: cephalin
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: 1e8bd60eab5853b121b9c08622914b9c62449f8f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618767"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Azure App Service での認証および認可

> [!NOTE]
> 現時点では、Azure App Service および Azure Functions で AAD V2 (MSAL を含む) はサポートされていません。 更新プログラムがないかどうか確認してください。
>

Azure App Service は組み込みの認証と認可のサポートを提供するので、Web アプリ、RESTful API、モバイル バックエンド、さらには [Azure Functions](../azure-functions/functions-overview.md) でも、最小限のコードを記述するだけで、またはまったく記述せずに、ユーザーのサインインとデータへのアクセスを可能にできます。 この記事では、App Service によりアプリの認証と認可を簡略化する方法について説明します。 

安全な認証と認可には、フェデレーション、暗号化、[JSON Web トークン (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) 管理、[付与タイプ](https://oauth.net/2/grant-types/)など、セキュリティについての深い理解が必要です。 App Service ではこれらのユーティリティが提供されているので、ビジネス価値を顧客に提供することにいっそうの時間と労力を費やすことができます。

> [!NOTE]
> 認証と認可に App Service を必ず使う必要はありません。 多くの Web フレームワークにセキュリティ機能がバンドルされており、必要に応じてそれらを使うことができます。 App Service より高い柔軟性が必要な場合は、独自のユーティリティを記述することもできます。  
>

ネイティブ モバイル アプリに固有の情報については、[Azure App Service でのモバイル アプリ用のユーザー認証と認可](../app-service-mobile/app-service-mobile-auth.md)に関する記事をご覧ください。

## <a name="how-it-works"></a>動作のしくみ

認証と認可のモジュールは、アプリケーションのコードと同じサンドボックスで実行します。 有効になっている場合、すべての受信 HTTP 要求は、アプリケーション コードによって処理される前に、認証と認可のモジュールを通過します。

![](media/app-service-authentication-overview/architecture.png)

このモジュールは、アプリのためにいくつかの処理を行います。

- 指定されたプロバイダーでユーザーを認証します
- トークンを検証、格納、更新します
- 認証されたセッションを管理します
- 要求ヘッダーに ID 情報を挿入します

このモジュールはアプリケーションのコードとは別に実行され、アプリの設定を使って構成されます。 SDK、特定の言語、またはアプリケーションのコードの変更は必要ありません。 

### <a name="user-claims"></a>ユーザーの要求

すべての言語フレームワークで、App Service はユーザーの要求を要求ヘッダーに挿入することによってコードで要求を使用できるようにします。 ASP.NET 4.6 アプリの場合、App Service は認証されたユーザーの要求で [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) を設定するので、標準の .NET コード パターン (`[Authorize]` 属性など) に従うことができます。 同様に、PHP アプリの場合、App Service は `_SERVER['REMOTE_USER']` 変数を設定します。

[Azure Functions](../azure-functions/functions-overview.md) では、`ClaimsPrincipal.Current` は .NET コードに対してハイドレートされませんが、それでも要求ヘッダー内でユーザーの要求を見つけることができます。

詳しくは、「[ユーザー要求へのアクセス](app-service-authentication-how-to.md#access-user-claims)」をご覧ください。

### <a name="token-store"></a>トークン ストア

App Service が提供する組み込みのトークン ストアは、Web アプリ、API、またはネイティブ モバイル アプリのユーザーに関連付けられているトークンのリポジトリです。 いずれかのプロバイダーで認証を有効にすると、このトークン ストアはアプリですぐに使用できるようになります。 次のような場合、アプリケーション コードはユーザーに代わってこれらのプロバイダーのデータにアクセスする必要があります。 

- 認証されたユーザーの Facebook タイムラインに投稿する
- Azure Active Directory Graph API や Microsoft Graph などからユーザーの会社データを読み取る

通常、アプリケーションでこれらのトークンを収集、格納、更新するには、コードを記述する必要があります。 トークン ストアに関しては、トークンが必要になったら[トークンを取得](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)し、トークンが無効になったら[トークンを更新するよう App Service に指示する](app-service-authentication-how-to.md#refresh-identity-provider-tokens)だけです。 

ID トークン、アクセス トークン、更新トークンは認証されたセッションに対してキャッシュされ、関連付けられているユーザーだけがアクセスできます。  

アプリでトークンを使う必要がない場合は、トークン ストアを無効にしてもかまいません。

### <a name="logging-and-tracing"></a>ログとトレース

[アプリケーション ログを有効にする](troubleshoot-diagnostic-logs.md)と、認証と認可のトレースをログ ファイルで直接見ることができます。 予期しない認証エラーが発生した場合は、既存のアプリケーション ログを参照して、すべての詳細を簡単に確認できます。 [失敗した要求トレース](troubleshoot-diagnostic-logs.md)を有効にしてある場合は、失敗した要求で認証および認可モジュールが演じていた役割を正確に確認できます。 トレース ログでは、`EasyAuthModule_32/64` という名前のモジュールへの参照を探します。 

## <a name="identity-providers"></a>ID プロバイダー

App Service が使用する[フェデレーション ID](https://en.wikipedia.org/wiki/Federated_identity) では、サード パーティの ID プロバイダーが代わりにユーザー ID と認証フローを管理します。 既定では 5 つの ID プロバイダーを利用できます。 

| プロバイダー | サインイン エンドポイント |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft アカウント](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

これらのプロバイダーのいずれかで認証と認可を有効にすると、そのプロバイダーのサインイン エンドポイントが、ユーザー認証と、プロバイダーからの認証トークンの検証に使用できるようになります。 任意の数のサインイン オプションを、ユーザーに対して簡単に提供できます。 別の ID プロバイダーや[独自のカスタム ID ソリューション][custom-auth]を統合することもできます。

## <a name="authentication-flow"></a>Authentication flow

認証フローは、プロバイダーによる違いはありませんが、プロバイダーの SDK でサインインするかどうかによって異なります。

- プロバイダーの SDK を使わない場合:アプリケーションは、フェデレーション サインインを App Service に委任します。 これはブラウザー アプリで通常のケースであり、プロバイダーのログイン ページをユーザーに表示することができます。 サーバーのコードがサインイン プロセスを管理するので、"_サーバー主導のフロー_" または "_サーバー フロー_" とも呼ばれます。 このケースはブラウザー アプリに適用されます。 また、Mobile Apps クライアント SDK を使ってユーザーをサインインさせるネイティブ アプリにも適用されます。その場合は、SDK が Web ビューを開いて App Service 認証でユーザーをサインインさせます。 
- プロバイダーの SDK を使う場合:アプリケーションは、ユーザーを手動でプロバイダーにサインインさせてから、検証のために App Service に認証トークンを送信します。 これはブラウザーレス アプリで通常のケースであり、プロバイダーのサインイン ページをユーザーに表示することはできません。 アプリケーションのコードがサインイン プロセスを管理するので、"_クライアント主導のフロー_" または "_クライアント フロー_" とも呼ばれます。 このケースは、REST API、[Azure Function](../azure-functions/functions-overview.md)、JavaScript ブラウザー クライアント、およびいっそう柔軟なサインイン プロセスを必要とするブラウザー アプリに適用されます。 また、プロバイダーの SDK を使ってユーザーをサインインさせるネイティブ モバイル アプリにも適用されます。

> [!NOTE]
> App Service または [Azure Functions](../azure-functions/functions-overview.md) の別の REST API を呼び出す App Service 内の信頼されたブラウザー アプリからの呼び出しは、サーバー主導のフローを使って認証することができます。 詳細については、「[Azure App Service での認証と認可のカスタマイズ](app-service-authentication-how-to.md)」を参照してください。
>

次の表では、認証フローの手順を示します。

| 手順 | プロバイダーの SDK を使わない場合 | プロバイダーの SDK を使う場合 |
| - | - | - |
| 1.ユーザーをサインインさせる | クライアントを `/.auth/login/<provider>` にリダイレクトします。 | クライアント コードはプロバイダーの SDK でユーザーを直接サインインさせ、認証トークンを受け取ります。 詳しくは、プロバイダーのドキュメントをご覧ください。 |
| 2.認証をポストする | プロバイダーはクライアントを `/.auth/login/<provider>/callback` にリダイレクトします。 | クライアント コードは検証のために[プロバイダーからのトークンを `/.auth/login/<provider>` にポスト](app-service-authentication-how-to.md#validate-tokens-from-providers)します。 |
| 手順 3.認証済みのセッションを確立する | App Service は認証された Cookie を応答に追加します。 | App Service は独自の認証トークンをクライアント コードに返します。 |
| 4.認証済みのコンテンツを提供する | クライアントは以降の要求に認証クッキーを含めます (ブラウザーによって自動的に処理されます)。 | クライアント コードは `X-ZUMO-AUTH` ヘッダーで認証トークンを提示します (Mobile Apps クライアント SDK によって自動的に処理されます)。 |

クライアント ブラウザーの場合、App Service は認証されていないすべてのユーザーを `/.auth/login/<provider>` に自動的に送ることができます。 また、ユーザーが選んだプロバイダーを使ってアプリにサインインするための 1 つまたは複数の `/.auth/login/<provider>` リンクをユーザーに表示することもできます。

<a name="authorization"></a>

## <a name="authorization-behavior"></a>認可の動作

[Azure Portal](https://portal.azure.com) では、複数の動作で App Service の認可を構成することができます。

![](media/app-service-authentication-overview/authorization-flow.png)

以下の見出しではそれらのオプションを説明します。

### <a name="allow-all-requests-default"></a>すべての要求を許可する (既定値)

認証と認可は App Service によって管理されません (オフ)。 

認証と認可を必要としない場合、または認証と認可のコードを独自に記述する場合は、このオプションを選びます。

### <a name="allow-only-authenticated-requests"></a>認証された要求のみを許可する

オプションは **[\<プロバイダー> でのログイン]** です。 App Service は、すべての匿名要求を、選ばれたプロバイダーの `/.auth/login/<provider>` にリダイレクトします。 匿名要求がネイティブ モバイル アプリからのものである場合、返される応答は `HTTP 401 Unauthorized` です。

このオプションを使用すると、アプリで認証コードを記述する必要はまったくありません。 役割固有の認可などのさらに細かい認可は、ユーザーの要求を調べることで処理できます (「[ユーザー要求へのアクセス](app-service-authentication-how-to.md#access-user-claims)」をご覧ください)。

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>すべての要求を許可するが、認証された要求を検証する

オプションは **[匿名要求を許可する]** です。 このオプションは、App Service での認証と認可を有効にしますが、認可の決定をアプリケーション コードまで延期します。 認証された要求について、App Service は HTTP ヘッダーで認証情報も渡します。 

このオプションでは、匿名要求をいっそう柔軟に処理できます。 たとえば、ユーザーに[複数のサインイン プロバイダーを提示する](app-service-authentication-how-to.md#use-multiple-sign-in-providers)ことができます。 ただし、コードを記述する必要があります。 

## <a name="more-resources"></a>その他のリソース

[チュートリアル:Azure App Service (Windows) でユーザーをエンド ツー エンドで認証および認可する](app-service-web-tutorial-auth-aad.md)  
[チュートリアル: Linux 用 Azure App Service でユーザーをエンド ツー エンドで認証および認可する](containers/tutorial-auth-aad.md)  
[App Service での認証と認可のカスタマイズ](app-service-authentication-how-to.md)

プロバイダー固有の手順ガイド:

* [Azure Active Directory ログインを使用するようにアプリを構成する方法][AAD]
* [Facebook ログインを使用するようにアプリを構成する方法][Facebook]
* [Google ログインを使用するようにアプリを構成する方法][Google]
* [Microsoft アカウント ログインを使用するようにアプリを構成する方法][MSA]
* [Twitter ログインを使用するようにアプリを構成する方法][Twitter]
* [方法: アプリケーションにカスタム認証を使用する][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
