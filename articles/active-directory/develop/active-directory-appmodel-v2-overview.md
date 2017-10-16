---
title: "Azure Active Directory v2.0 エンドポイント | Microsoft Docs"
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
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 1e286044fb1a1b367fcac2dc14c47f68d5ed120d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Microsoft アカウント ユーザーおよび Azure AD ユーザーによる単一アプリでのサインイン
アプリの開発者はこれまで、個人用の Microsoft アカウントと Azure Active Directory の職場アカウントの両方をサポートするには、2 つの異なるシステムを 1 つに統合する必要がありました。  **Azure AD v2.0 エンドポイント**では、1 つのシンプルな統合環境を使用して両方の種類のアカウントにサインインできる、新しい認証 API のバージョンが導入されています。  v2.0 エンドポイントを使うアプリは、どちらの種類のアカウントを使用していても、[Microsoft Graph](https://graph.microsoft.io) の REST API を使用できます。

## <a name="getting-started"></a>Getting Started (概要)
次の一覧からプラットフォームを選択し、Microsoft のオープン ソース ライブラリとフレームワークを利用してアプリを構築します。  または、OAuth 2.0 と OpenID Connect のプロトコル ドキュメントを使用し、認証ライブラリを使用せずに直接プロトコル メッセージを送受信することもできます。

<br />

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>新機能
ここで紹介する情報は、v2.0 エンドポイントによってできること、できないことを理解するうえで役立ちます。

* [v2.0 エンドポイントを使用して構築できるアプリケーションの種類](active-directory-v2-flows.md)について詳しく学習します。
* v2.0 エンドポイントの [制限事項と制約事項](active-directory-v2-limitations.md) を確認してください。
* v2.0 エンドポイントの概要に関する次のビデオをご覧ください。

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="reference"></a>リファレンス
これらのリンクは、プラットフォームの詳細を確認する上で役立ちます。

* [v2.0 プロトコルのリファレンス](active-directory-v2-protocols.md)
* [v2.0 トークンのリファレンス](active-directory-v2-tokens.md)
* [v2.0 ライブラリのリファレンス](active-directory-v2-libraries.md)
* [v2.0 エンドポイントのスコープと同意](active-directory-v2-scopes.md)
* [Microsoft Graph](https://graph.microsoft.io)

## <a name="help--support"></a>ヘルプとサポート
これらは、Azure Active Directory での開発に関するサポートを求めるのに最適な場所です。

* [Stack Overflow の `azure-active-directory` タグと `adal` タグ](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)
* [Azure Active Directory に関するフィードバック](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)


> [!NOTE]
> Azure Active Directory から職場および学校アカウントにサインインするだけでよい場合は、最初に [Azure AD 開発者向けガイド](active-directory-developers-guide.md)を参照してください。  v2.0 エンドポイントは、Microsoft の個人用アカウントにサインインすることが明らかに必要な開発者が使用するためのものです。

