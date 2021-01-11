---
title: B2B コラボレーションのためのセルフサービス サインアップ ポータル - Azure AD
description: Azure Active Directory B2B コラボレーションは、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートします
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff18d3d9cae6e887abbd9fb1845de62386062b2b
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87907396"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Azure AD B2B コラボレーションのサインアップのためのセルフサービス

お客様は、[Azure portal](https://portal.azure.com) とエンド ユーザー向けの[アプリケーション アクセス パネル](https://myapps.microsoft.com)を通して公開される組み込み機能を使用して、さまざまな操作を実行できます。 ただし、ユーザーの組織のニーズに合わせて、B2B ユーザー向けのオンボード ワークフローをカスタマイズすることが必要な場合があります。

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>B2B ゲスト ユーザーのサインアップのための Azure AD のエンタイトルメント管理

招待する側の組織は、だれが社外のコラボレーターであり、だれ企業のリソースへのアクセスを必要としているのが事前にわからないことがあります。 招待側の組織には、組織で制御するポリシーに従ってパートナー企業のユーザーがサインアップできるようにする方法が必要です。 他の組織のユーザーがアクセスを要求し、それが承認されたらゲスト アカウントが提供され、グループ、アプリ、および SharePoint Online サイトに割り当てられるようにする場合、[Azure AD のエンタイトルメント管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)を使用して、[外部ユーザーのアクセスを管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users)するポリシーを構成できます。

## <a name="azure-active-directory-b2b-invitation-api"></a>Azure Active Directory B2B の招待 API

組織は、[Microsoft Graph 招待マネージャー API](https://docs.microsoft.com/graph/api/resources/invitation?view=graph-rest-1.0) を使用して、B2B ゲスト ユーザー向けの独自のオンボード エクスペリエンスを構築できます。 セルフサービス B2B ゲスト ユーザーのサインアップを提供する場合は、[Azure AD のエンタイトルメント管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)を使用することをお勧めします。 ただし、独自のエクスペリエンスを構築する場合は、[招待状の作成 API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0&tabs=http) を使用して、たとえば、カスタマイズされた招待メールを自動的に B2B ユーザーに直接送信できます。 また、アプリは、作成応答で返された inviteRedeemUrl を使用して、(選択した通信メカニズムを介して) 招待するユーザーに対して独自の招待状を作成できます。

## <a name="next-steps"></a>次のステップ

* [Azure AD B2B コラボレーションとは](what-is-b2b.md)
* [Azure AD B2B コラボレーションのライセンス](licensing-guidance.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](faq.md)
