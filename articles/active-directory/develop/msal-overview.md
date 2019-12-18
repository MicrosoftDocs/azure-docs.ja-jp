---
title: MSAL の概要 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) を使用すると、アプリケーション開発者はセキュリティで保護された Web API を呼び出すためにトークンを取得できます。 これらの Web API には、Microsoft Graph、その他の Microsoft API、サード パーティの Web API、または、独自の Web API があります。 MSAL は、複数のアプリケーション アーキテクチャとプラットフォームをサポートします。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 718c33fc8a834a6abbbc9f1a1c33e02eb810a991
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915296"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL) の概要
Microsoft Authentication Library (MSAL) を利用すると、セキュリティで保護された Web API にアクセスする目的で、開発者は Microsoft ID プラットフォーム エンドポイントから[トークン](developer-glossary.md#security-token)を取得できます。 これらの Web API には、Microsoft Graph、その他の Microsoft API、サード パーティの Web API、または、独自の Web API があります。 MSAL は .NET、JavaScript、Android、iOS で利用でき、さまざまなアプリケーション アーキテクチャとアプリケーション プラットフォームをサポートします。

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
* [対話式でサインインしたユーザーの代わりにモバイル アプリケーションが Web API を呼び出す](scenario-mobile-overview.md)
* [デスクトップ/サービス デーモン アプリケーションがそれ自体の代理として Web API を呼び出す](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>言語とフレームワーク

| ライブラリ | サポートされているプラットフォームとフレームワーク|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework、.NET Core、Xamarin Android、Xamarin iOS、ユニバーサル Windows プラットフォーム|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| AngularJS、Ember.js、Durandal.js など、JavaScript/TypeScript フレームワーク|
| [MSAL for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [iOS および macOS 用の MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS と macOS|
| [MSAL Java (プレビュー)](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|
| [MSAL Python (プレビュー)](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Python|

## <a name="differences-between-adal-and-msal"></a>ADAL と MSAL の違い

Active Directory Authentication Library (ADAL) は開発者向け Azure AD (v1.0) エンドポイントと統合されます。このエンドポイントでは、MSAL が Microsoft ID プラットフォーム (v2.0) エンドポイントと統合されます。 v1.0 エンドポイントでは職場アカウントがサポートされますが、個人アカウントはサポートされません。 v2.0 エンドポイントは、Microsoft の個人アカウントと職場アカウントを 1 つの認証システムに統合します。 また、MSAL を使用するとき、Azure AD B2C の認証も取得できます。

詳細については、「[migrating to MSAL.NET from ADAL.NET](msal-net-migration.md)」 (ADAL.NET から MSAL.NET に移行する) と「[migrating to MSAL.js from ADAL.js](msal-compare-msal-js-and-adal-js.md)」 (ADAL.js から MSAL.js に移行する) をお読みください。
