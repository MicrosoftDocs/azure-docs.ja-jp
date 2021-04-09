---
title: Azure Active Directory B2C のサンプル コード | Microsoft Docs
description: Azure Active Directory B2C のモバイル、デスクトップ、Web、およびシングルページ アプリケーションのコード サンプル。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b09587d90024a8c376be8b0d93f7ef7b6cc51a1e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008486"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Azure Active Directory B2C のサンプル コード

次の表には、iOS、Android、.NET、Node.js などのアプリケーションのサンプルへのリンクがまとめてあります。

## <a name="mobile-and-desktop-apps"></a>モバイル アプリとデスクトップ アプリ

| サンプル | 説明 |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Azure AD B2C ユーザーを認証し、OAuth 2.0 を使用して API を呼び出す Swift の iOS サンプル |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | MSAL を使用して Azure Active Directory B2C 経由でユーザーを認証し、結果として得られたトークンで Web API にアクセスする方法を示す単純な Android アプリ。 |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | サード パーティのライブラリを使用して、Objective-C で iOS アプリケーションをビルドする方法を示すサンプル。このアプリケーションは、Azure AD B2C ID サービスに対して Microsoft ID ユーザーを認証します。 |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | サード パーティのライブラリを使用して、Android アプリケーションをビルドする方法を示すサンプル。このアプリケーションは、B2C ID サービスに対して Microsoft ID ユーザーを認証し、OAuth 2.0 アクセス トークンを使用して Web API を呼び出します。 |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Windows Desktop .NET (WPF) アプリケーションが Azure AD B2C を使用してユーザーのサインインを処理し、MSAL.NET を使用してアクセス トークンを取得して API を呼び出す方法を示すサンプル。 |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | MSAL を使用して Azure Active Directory B2C 経由でユーザーを認証し、結果として得られたトークンで Web API にアクセスする方法を示す単純な Xamarin Forms アプリ。 |

## <a name="web-apps-and-apis"></a>Web アプリと API

| サンプル | 説明 |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Azure AD B2C を使用してセキュリティで保護された、.NET Web API を呼び出す .NET Web アプリケーション用の結合されたサンプル。 |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | OpenID Connect を使用して Azure AD B2C でユーザーのサインイン処理を行う ASP.NET Core Web アプリケーション。 |
| [dotnetcore-webapp-msal-api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Azure AD B2C を使用してユーザーのサインインを処理し、MSAL.NET を使用してアクセス トークンを取得して API を呼び出す ASP.NET Core Web アプリケーション。 |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | OpenID Connect を使用して、Web アプリケーションを Express ですばやく簡単にセットアップできるようにする Node.js アプリ。 |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | passport.js を使用して Web API を保護し、B2C アクセス トークンを受け入れる方法を示す、Azure AD B2C 用の小さな node.js Web API。 |
| [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | Microsoft ID プラットフォームの B2C を Python Web アプリケーションに統合する方法を説明します。  |

## <a name="single-page-apps"></a>シングル ページ アプリ

| サンプル | 説明 |
|--------| ----------- |
| [ms-identity-javascript-react-tutorial](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c) | Web API を呼び出すシングルページ アプリケーション (SPA)。 認証は、MSAL React を使用して、Azure AD B2C によって行われます。 このサンプルでは、認可コード フロー (PKCE あり) を使用しています。 |
| [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | Web API を呼び出すシングルページ アプリケーション (SPA)。 認証は、MSAL.js を使用して、Azure AD B2C によって行われます。 このサンプルでは、認可コード フロー (PKCE あり) を使用しています。 |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Web API を呼び出すシングルページ アプリケーション (SPA)。 認証は、MSAL.js を使用して、Azure AD B2C によって行われます。 このサンプルでは、暗黙的なフローを使用しています。|
| [javascript-nodejs-management](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter1) | B2C ディレクトリ内のユーザーを管理するために Microsoft Graph を呼び出すシングルページ アプリケーション (SPA)。 認証は、MSAL.js を使用して、Azure AD B2C によって行われます。 このサンプルでは、認可コード フロー (PKCE あり) を使用しています。|

## <a name="consoledaemon-apps"></a>コンソールまたはデーモン アプリ

| サンプル | 説明 |
|--------| ----------- |
| [javascript-nodejs-management](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter2) | B2C ディレクトリ内のユーザーを管理するために、独自の ID で Microsoft Graph を呼び出す Node.js および Express コンソール デーモン アプリケーション。 認証は、MSAL Node を使用して、Azure AD B2C によって行われます。 このサンプルでは、認可コード フローを使用しています。|
| [dotnetcore-b2c-account-management](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) | B2C ディレクトリ内のユーザーを管理するために、独自の ID で Microsoft Graph を呼び出す .NET Core コンソール アプリケーション。 認証は、MSAL.NET を使用して、Azure AD B2C によって行われます。 このサンプルでは、認可コード フローを使用しています。|

## <a name="saml-test-application"></a>SAML テスト アプリケーション

| サンプル | 説明 |
|--------| ----------- |
| [saml-sp-tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | SAML ID プロバイダーとして動作するように構成された Azure AD B2C をテストするための SAML テスト アプリケーション。 |

## <a name="api-connectors"></a>API コネクタ

次の表に、[API コネクタ](api-connectors-overview.md)を使用したユーザー フローで Web API を活用するためのコード サンプルへのリンクを示します。

### <a name="azure-function-quickstarts"></a>Azure 関数のクイックスタート

| サンプル                                                                                                                          | 説明                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | この .NET Core Azure 関数サンプルには、特定の電子メール ドメインへのサインアップを制限し、ユーザー指定の情報を検証する方法が示されています。 |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | この Node.js Azure 関数サンプルには、特定の電子メール ドメインへのサインアップを制限し、ユーザー指定の情報を検証する方法が示されています。  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | この Python Azure 関数サンプルには、特定の電子メール ドメインへのサインアップを制限し、ユーザー指定の情報を検証する方法が示されています。    |


### <a name="automated-fraud-protection-services--captcha"></a>自動化された不正行為防止サービスと CAPTCHA
| サンプル                                                                                                            | 説明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Arkose Labs の不正行為および不正使用防止](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | このサンプルでは、Arkose Labs の不正行為および不正使用防止サービスを使用して、ユーザー サインアップを保護する方法を示します。 |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | このサンプルでは、reCAPTCHA チャレンジを使用して、自動化された不正使用を防ぐことで、ユーザー サインアップを保護する方法を示します。 |


### <a name="identity-verification"></a>本人確認

| サンプル                                                                                                            | 説明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | このサンプルでは、API コネクタを使用して IDology と統合することで、サインアップ フローの一環としてユーザー ID を確認する方法を示します。 |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | このサンプルでは、API コネクタを使用して Experian と統合することで、サインアップ フローの一環としてユーザー ID を確認する方法を示します。 |


### <a name="other"></a>その他

| サンプル                                                                                                            | 説明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [招待コード](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | このサンプルでは、招待コードを使用して、サインアップを特定の対象ユーザーに制限する方法を示します。|
| [API コネクタのコミュニティ サンプル](https://github.com/azure-ad-b2c/api-connector-samples) | このリポジトリには、API コネクタを使用することで可能になるシナリオのサンプルがコミュニティで保持されています。|
