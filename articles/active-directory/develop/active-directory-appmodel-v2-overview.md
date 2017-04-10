---
title: "Azure AD v2.0 エンドポイントのリソース | Microsoft Docs"
description: "Microsoft アカウントと Azure Active Directory の両方でサインインできるアプリケーション構築を紹介します。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: ba958d029e5bf1bc914a2dff4b6c09282d578c67
ms.openlocfilehash: d3571d3d842ac908200c7e6b437b40d3c370b38d


---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Microsoft アカウント ユーザーおよび Azure AD ユーザーによる単一アプリでのサインイン
アプリケーション開発者はこれまで、Microsoft アカウントと Azure Active Directory の両方をサポートするには、2 つの異なるシステムを統合する必要がありました。  そこで、どちらのアカウントのユーザーも Azure AD システムを使ってサインインできるようにする、新しい認証 API のバージョンを導入しました。  この集中型の認証システムを **v2.0 エンドポイント**と呼びます。  v2.0 エンドポイントを使用すれば、1 回のシンプルな統合によって、個人アカウントと職場/学校のアカウントを両方持っている何百万ものユーザーにリーチできるようになります。

v2.0 エンドポイントを使うアプリは、どちらの種類のアカウントを使用していても、[Microsoft Graph](https://graph.microsoft.io) や [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) の REST API を使用できます。

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Getting Started (概要)
>[!VIDEO https://channel9.msdn.com/Events/Build/2016/P530/player]


次の一覧からプラットフォームを選択し、Microsoft のオープン ソース ライブラリとフレームワークを利用してアプリを構築します。  または、OAuth 2.0 と OpenID Connect のプロトコル ドキュメントを使用し、認証ライブラリを使用せずに直接プロトコル メッセージを送受信することもできます。

<!-- TODO: Finalize this table  -->
[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>新機能
ここで説明する概念は、v2.0 エンドポイントでできることとできないことを理解するうえで役立ちます。

* [v2.0 エンドポイントを使用して構築できるアプリケーションの種類](active-directory-v2-flows.md)について詳しく学習します。
* v2.0 エンドポイントの [制限事項と制約事項](active-directory-v2-limitations.md) を確認してください。
* 最近、[管理者に制限されたスコープ](active-directory-v2-scopes.md)と [OAuth2 クライアント資格情報の付与](active-directory-v2-protocols-oauth-client-creds.md)のサポートが追加されました。  試してみてください。

## <a name="reference"></a>リファレンス
これらのリンクは、プラットフォームの詳細を確認する上で役立ちます。

* ビルド 2016: [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps (Microsoft ID の概要: アプリへのエンタープライズ グレードのサインイン)](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
* [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) または [adal](http://stackoverflow.com/questions/tagged/adal) タグを使用した Stack Overflow の対処法についてのヒントが得られます。
* [v2.0 プロトコルのリファレンス](active-directory-v2-protocols.md)
* [v2.0 トークンのリファレンス](active-directory-v2-tokens.md)
* [v2.0 ライブラリのリファレンス](active-directory-v2-libraries.md)
* [v2.0 エンドポイントのスコープと同意](active-directory-v2-scopes.md)
* [Microsoft アプリ登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)
* [Office 365 の REST API リファレンス](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
* [Microsoft Graph](https://graph.microsoft.io)




<!--HONumber=Jan17_HO4-->


