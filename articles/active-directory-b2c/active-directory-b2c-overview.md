---
title: "概要 - Azure AD B2C | Microsoft Docs"
description: "Azure Active Directory B2C によるコンシューマー向けアプリケーションの開発"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: saeedakhter-msft
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 44d5d31d49c375c802a67511d1f962df20656559
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="azure-ad-b2c-focus-on-your-app-let-us-worry-about-sign-up-and-sign-in"></a>Azure AD B2C: アプリに集中し、サインアップとサインインについてはお任せください

Azure AD B2C は、Web およびモバイル アプリケーション用のクラウド ID 管理ソリューションです。 億単位の ID まで拡張可能な高可用性のグローバル サービスです。 エンタープライズ レベルのセキュリティ保護されたプラットフォームに構築された Azure AD B2C は、アプリケーション、ビジネス、および顧客を保護された状態に保ちます。

最小構成では、Azure AD B2C によりアプリケーションは次のものを認証できます。

* **ソーシャル アカウント** (Facebook、Google、LinkedIn など)
* **エンタープライズ アカウント** (オープン スタンダードのプロトコル、OpenID Connect または SAML を使用)
* **ローカル アカウント** (電子メール アドレスとパスワード、またはユーザー名とパスワード)

## <a name="get-started"></a>作業開始

まず、「[Azure AD B2C テナントを作成する](active-directory-b2c-get-started.md)」に概要が示されている手順を使用して、独自のテナントを取得します。

次に、アプリケーション開発シナリオを選択します。

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![モバイル アプリとデスクトップ アプリ](../active-directory/develop/media/active-directory-developers-guide/NativeApp_Icon.png)<br />モバイル アプリとデスクトップ アプリ</center> | [概要](active-directory-b2c-reference-oauth-code.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br /><br />[iOS](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)<br /><br />[Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)<br /><br />[Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) |  |
| <center>![Web Apps](../active-directory/develop/media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [概要](active-directory-b2c-reference-oidc.md)<br /><br />[ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)<br /><br />[ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | [Node.js](active-directory-b2c-devquickstarts-web-node.md) |  |
| <center>![シングル ページ アプリ](../active-directory/develop/media/active-directory-developers-guide/SPA.png)<br />シングル ページ アプリ</center> | [概要](active-directory-b2c-reference-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)<br /><br /> |  |  |
| <center>![Web API](../active-directory/develop/media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)<br /><br /> [ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)<br /><br /> [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | [.NET Web API の呼び出し](active-directory-b2c-devquickstarts-web-api-dotnet.md) |

## <a name="whats-new"></a>新機能

今後の Azure Active Directory B2C の変更点については、このページを頻繁に確認してください。 また、@AzureAD でも最新情報をツイートしていきます。

* "組み込みのポリシー" (一般公開) に加えて、["カスタム ポリシー"](active-directory-b2c-overview-custom.md) 機能がパブリック プレビューで使用できるようになりました。  カスタム ポリシーは、ID エクスペリエンスの構成を制御する必要のある ID の利点を得るためのものです。
* [アクセス トークン](https://azure.microsoft.com/en-us/blog/azure-ad-b2c-access-tokens-now-in-public-preview)機能がパブリック プレビューで使用できるようになりました。
* [ヨーロッパ ベースの Azure AD B2C ディレクトリの一般公開](https://azure.microsoft.com/en-us/blog/azuread-b2c-ga-eu/)が発表されました。
* 増加し続ける[サンプル コード ライブラリを Github](https://github.com/Azure-Samples?q=b2c) でご覧ください。

## <a name="how-to-articles"></a>ハウツー記事

以下に示す Azure Active Directory B2C の各機能の使用方法について確認してください。

* コンシューマー向けアプリケーションで使用するアカウント ([Facebook](active-directory-b2c-setup-fb-app.md)、[Google+](active-directory-b2c-setup-goog-app.md)、[Microsoft アカウント](active-directory-b2c-setup-msa-app.md)、[Amazon](active-directory-b2c-setup-amzn-app.md)、[LinkedIn](active-directory-b2c-setup-li-app.md)) を構成する。
* [カスタム属性を使用してコンシューマーに関する情報を収集する](active-directory-b2c-reference-custom-attr.md)。
* [コンシューマー向けアプリケーションで Azure Multi-Factor Authentication を有効にする](active-directory-b2c-reference-mfa.md)。
* [コンシューマー向けにセルフサービス パスワードのリセットをセットアップする](active-directory-b2c-reference-sspr.md)。
* Azure Active Directory B2C によって提供される[サインアップ、サインイン、その他のコンシューマー向けページの外観をカスタマイズする](active-directory-b2c-reference-ui-customization.md)。
* [Azure Active Directory Graph API を使用してプログラムによりコンシューマーを作成、読み取り、更新、削除する](active-directory-b2c-devquickstarts-graph-dotnet.md) 。

## <a name="next-steps"></a>次のステップ

以下のリンクは、サービスの詳細を調べるのに役立ちます。

* 「 [Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)」をご覧ください。
* Azure Active Directory B2C の[コード サンプル](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c)を確認します。 
* [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c) タグを使用したスタック オーバーフローへの対処法についてのヒントが得られます。
* [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) を利用して、サービスについての感想をお寄せください。皆様からのご意見をお待ちしております。
* [Azure AD B2C プロトコル リファレンス](active-directory-b2c-reference-protocols.md)を確認してください。
* [Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を確認してください。
* [Azure Active Directory B2C の FAQ](active-directory-b2c-faqs.md)をご覧ください。
* [Azure Active Directory B2C のサポート要求を提出する方法](active-directory-b2c-support.md)を確認してください。

## <a name="get-security-updates-for-our-products"></a>Microsoft 製品のセキュリティ更新プログラムの取得

セキュリティの問題が発生したときに通知を受け取ることをお勧めします。そのためには、[このページ](https://technet.microsoft.com/security/dd252948)にアクセスし、セキュリティ アドバイザリ通知を受信登録してください。


