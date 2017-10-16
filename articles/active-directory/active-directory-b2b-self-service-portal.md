---
title: "Azure Active Directory B2B コラボレーションのためのセルフサービス サインアップ ポータル | Microsoft ドキュメント"
description: "Azure Active Directory B2B コラボレーションは、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートします"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/24/2017
ms.author: sasubram
ms.openlocfilehash: 307373c75bbb87cec683f7a3097f8f159c9d5e61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Azure AD B2B コラボレーションのサインアップ用のセルフ サービス ポータル

お客様は、当社の IT 管理 [Azure ポータル](https://portal.azure.com)とエンド ユーザー向けの[アプリケーション アクセス パネル](https://myapps.microsoft.com)を通して公開される組み込み機能を使用して、さまざまな操作を実行できます。 しかし、当社では、企業が B2B ユーザーのために配布準備ワークフローをカスタマイズして、ユーザーの組織のニーズを満たす必要があることについても認識しています。 これは[当社の API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) を使用することで実現します。

お客様との話し合いの中で、当社は、他のすべてに抜きんでて求められている共通する 1 つのニーズがあることを認識しました。 招待側組織は、誰が社外のコラボレーターであり、誰が企業のリソースへのアクセスを必要としているのが事前にわからないことがあります。 招待側組織は、パートナー企業のユーザーが、招待側組織によって制御されるポリシーに従ってセルフサインアップする方法を求めていました。 このシナリオは当社の API を通じて実現できるため、当社では、それを行うための[サンプル Github プロジェクト](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web)を Github に発行しました。

当社の Github プロジェクトでは、組織による当社の API の使用方法を説明しています。さらに、信頼できるパートナー向けのポリシー ベースのセルフサービス サインアップ機能と、パートナーがアクセスできるアプリを決定するルールを提示しています。 パートナーのユーザーは必要なときにリソースに安全にアクセスすることができ、招待側組織はパートナーを手動で招待する必要はありません。 プロジェクトは、選択した Azure サブスクリプションに簡単にデプロイできます。

## <a name="as-is-code"></a>現況コード

このコードは、Azure Active Directory B2B 招待 API の使用法を示すサンプルとして提供されているコードであることに注意してください。 開発チームまたはパートナーによってカスタマイズする必要があり、実稼働のシナリオにデプロイする前に確認する必要があります。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。
* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)