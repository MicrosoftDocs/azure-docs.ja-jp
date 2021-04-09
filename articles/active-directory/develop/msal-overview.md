---
title: MSAL の概要 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) を使用すると、アプリケーション開発者はセキュリティで保護された Web API を呼び出すためにトークンを取得できます。 これらの Web API には、Microsoft Graph、その他の Microsoft API、サード パーティの Web API、または、独自の Web API が含まれます。 MSAL は、複数のアプリケーション アーキテクチャとプラットフォームをサポートします。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 55cecc658b11b7a09665af7128df25fbbff800ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559516"
---
# <a name="overview-of-the-microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL) の概要
Microsoft Authentication Library (MSAL) を使用すると、ユーザーを認証し、セキュリティで保護された Web API にアクセスする目的で、開発者は Microsoft ID プラットフォームから[トークン](developer-glossary.md#security-token)を取得できます。 これは、Microsoft Graph、その他の Microsoft API、サード パーティの Web API、または、独自の Web API へのセキュリティで保護されたアクセスを提供するために使用できます。 MSAL は、.NET、JavaScript、Java、Python、Android、iOS などの、さまざまなアプリケーション アーキテクチャとプラットフォームをサポートします。

MSAL では、多くのプラットフォームで API に一貫性があり、さまざまな方法でトークンを取得できます。 MSAL の使用には次のような利点があります。

* アプリケーションのプロトコルに対して OAuth ライブラリまたはコードを直接使用する必要がありません。
* ユーザーやアプリケーション (プラットフォームに適用できるとき) の代わりにトークンを取得します。
* トークン キャッシュを保持し、有効期限が近づくとユーザーの代わりにトークンを更新します。 トークンの期限切れを自分で処理する必要はありません。
* アプリケーションにサインインさせる対象ユーザー (自分の組織、複数の組織、職場、学校、Microsoft の個人アカウント、Azure AD B2C でのソーシャル ID、ソブリン クラウドのユーザー、国のクラウドのユーザー) の指定を支援します。
* 構成ファイルからアプリケーションを設定する作業を支援します。
* アクション可能な例外、ログ記録、テレメトリを公開することでアプリの問題解決を支援します。

## <a name="application-types-and-scenarios"></a>アプリケーションの種類とシナリオ
MSAL を使用し、Web アプリケーション、Web API、シングルページ アプリ (JavaScript)、モバイル アプリケーション、ネイティブ アプリケーション、デーモン、サーバー側アプリケーションといった多くの種類のアプリケーションからトークンを取得できます。

MSAL は次のような多くのアプリケーション シナリオで使用できます。

* [シングルページ アプリケーション (JavaScript)](scenario-spa-overview.md)
* [Web アプリのユーザーのサインイン](scenario-web-app-sign-user-overview.md)
* [Web アプリケーションのユーザーのサインインとユーザーの代理としての Web API の呼び出し](scenario-web-app-call-api-overview.md)
* [認証されたユーザーのみアクセスできるようにする Web API の保護](scenario-protected-web-api-overview.md)
* [Web API がサインイン ユーザーの代わりに別のダウンストリーム Web API を呼び出す](scenario-web-api-call-api-overview.md)
* [デスクトップ アプリケーションがサインイン ユーザーの代わりに Web API を呼び出す](scenario-desktop-overview.md)
* [モバイル アプリケーションが、対話形式でサインインしたユーザーの代わりに Web API を呼び出す](scenario-mobile-overview.md)。
* [デスクトップ/サービス デーモン アプリケーションがそれ自体の代理として Web API を呼び出す](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>言語とフレームワーク

| ライブラリ | サポートされているプラットフォームとフレームワーク|
| --- | --- |
| [MSAL for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)| Angular と Angular.js のフレームワークを使用したシングルページ アプリ|
| [iOS および macOS 用の MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS と macOS|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows、macOS、Linux|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)| Vue.js、Ember.js、Durandal.js など、JavaScript と TypeScript のフレームワーク|
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework、.NET Core、Xamarin Android、Xamarin iOS、ユニバーサル Windows プラットフォーム|
| [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)|Express を使用した Web アプリ、Electron を使用したデスクトップ アプリ、クロスプラットフォーム コンソール アプリ|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows、macOS、Linux|
| [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| React と React ベースのライブラリ (Next.js、Gatsby.js) を使用したシングルページ アプリ|

## <a name="differences-between-adal-and-msal"></a>ADAL と MSAL の違い

Active Directory Authentication Library (ADAL) は開発者向け Azure AD (v1.0) エンドポイントと統合されます。そこでは、MSAL が Microsoft ID プラットフォームと統合されます。 v1.0 エンドポイントでは職場アカウントがサポートされますが、個人アカウントはサポートされません。 v2.0 エンドポイントは、Microsoft の個人アカウントと職場アカウントを 1 つの認証システムに統合します。 また、MSAL を使用するとき、Azure AD B2C の認証も取得できます。

詳細については、「[migrating to MSAL.NET from ADAL.NET](msal-net-migration.md)」 (ADAL.NET から MSAL.NET に移行する) と「[migrating to MSAL.js from ADAL.js](msal-compare-msal-js-and-adal-js.md)」 (ADAL.js から MSAL.js に移行する) をお読みください。
