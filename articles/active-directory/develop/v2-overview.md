---
title: v2.0 について | Azure
description: v2.0 エンドポイントおよびプラットフォームについて説明します。
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb269df035bcc11583ebb7cff7d1ee2c3f6d8bca
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208301"
---
# <a name="about-v20"></a>v2.0 について

v2.0 エンドポイントおよびプラットフォームはプレビュー段階であり、継続的に強化されています。 JavaScript シングルページ アプリケーション (SPA) のシナリオは現時点で機能が完備していますので、プレビューから一般提供 (GA) にステータスを更新できるよう、MSAL.js を使用してブラウザー ベースのアプリケーションを構築し、フィードバックをお寄せください。

> [!NOTE]
> MSAL Android、iOS、および .NET には、開発中の機能が残っています。 それらを使用してアプリケーションを構築し、フィードバックをお寄せいただくことができます。

Azure portal の [[アプリの登録 (プレビュー)]](quickstart-register-app.md) エクスペリエンスが大きく改善され、ADAL または MSAL で構築されたすべてのアプリケーションが含まれるようになり、使いやすさが向上しました。

アプリケーションの開発者はこれまで、個人用の Microsoft アカウントと Azure Active Directory (Azure AD) の職場アカウントの両方をサポートするには、2 つの異なるシステムを 1 つに統合する必要がありました。 v2.0 エンドポイントおよびプラットフォームは、このプロセスを簡素化する認証 API のバージョンを提供します。 単一の統合を使用してどちらの種類のアカウントからでもサインインできます。 v2.0 エンドポイントを使うアプリケーションは、どちらの種類のアカウントを使用していても、[Microsoft Graph API](https://developer.microsoft.com/graph) の REST API を使用できます。

## <a name="getting-started"></a>使用の開始

次の一覧からお好みのプラットフォームを選択し、Microsoft のオープン ソース ライブラリとフレームワークを利用してアプリケーションを構築します。

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>v2.0 エンドポイントおよびプラットフォームの詳細

Azure AD v2.0 エンドポイントで実現できることについては以下をご覧ください。

* [Azure AD v2.0 エンドポイントで構築できるアプリケーションの種類](v2-app-types.md)を知る。
* Azure AD v2.0 エンドポイントの [制限事項と制約事項](active-directory-v2-limitations.md)を知る。

## <a name="additional-resources"></a>その他のリソース

v2.0 についての詳細な情報:

* [Microsoft ID プラットフォームについて](about-microsoft-identity-platform.md)
* [v2.0 プロトコル リファレンス](active-directory-v2-protocols.md)
* [アクセス トークン リファレンス](access-tokens.md)
* [ID トークン リファレンス](id-tokens.md)
* [v2.0 認証ライブラリ リファレンス](reference-v2-libraries.md)
* [v2.0 におけるアクセス許可と同意](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)

> [!NOTE]
> Azure Active Directory から職場および学校アカウントにサインインするだけでよい場合は、最初に [Azure AD 開発者向けガイド](v1-overview.md)をご覧ください。 v2.0 エンドポイントは、Microsoft の個人用アカウントにサインインすることが明らかに必要な開発者が使用するためのものです。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
