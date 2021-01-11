---
title: Azure Active Directory での B2B コラボレーションとは
description: Azure Active Directory B2B コラボレーションでは、リソースを外部パートナーと安全に共有してコラボレーションできるように、ゲスト ユーザー アクセスをサポートしています。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfce0031d912b1611b6810310e56241857821579
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926724"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Azure Active Directory B2B のゲスト ユーザー アクセスとは

Azure Active Directory (Azure AD) の企業間 (B2B) コラボレーションは、自分の組織にゲスト ユーザーを招待して共同作業を行うことができる External Identities の機能です。 B2B コラボレーションによって、貴社のデータに対するコントロールを維持した状態で、他の組織からアクセスするゲスト ユーザーとアプリケーションとサービスを安全に共有できます。 外部パートナーの規模に関係なく、Azure AD を所有していない場合や IT 部門が存在していない場合でも、外部パートナーとセキュリティで保護された安全な状態で作業できます。 単純な招待と受諾プロセスによって、パートナーは各自の資格情報を使用して、貴社のリソースにアクセスできます。 開発者は、Azure AD の B2B API を使用して、招待プロセスをカスタマイズしたり、セルフサービス サインアップ ポータルなどのアプリケーションを作成ししたりできます。 ゲスト ユーザーに関連したライセンスと価格情報については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。  

   > [!IMPORTANT]
   > **2021 年 3 月 31 日以降**、Microsoft は、B2B コラボレーションのシナリオで管理されていない Azure AD アカウントとテナントを作成することによる、招待の引き換えをサポートしなくなります。 準備として、お客様は、[電子メール ワンタイム パスコード認証](one-time-passcode.md)をオプトインすることをお勧めします。 さらに多くの方法で共同作業を行うことができるように、このパブリック プレビュー機能についてフィードバックをお待ちしております。

## <a name="collaborate-with-any-partner-using-their-identities"></a>パートナーの ID を使用してパートナーとコラボレーションする

Azure AD B2B では、パートナーが各自の ID 管理ソリューションを使用するため、組織には外部管理を行うためのオーバーヘッドがありません。 ゲスト ユーザーは、各自の職場、学校、またはソーシャルの ID を使用して、アプリとサービスにサインインします。

- パートナーは、各自の ID と資格情報を使用するため、Azure AD は必要ありません。
- 外部アカウントまたはパスワードを管理する必要はありません。
- アカウントの同期もアカウントのライフ サイクルの管理も必要ありません。  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Azure AD ポータルからゲスト ユーザーを簡単に招待する

管理者は、Azure portal でゲスト ユーザーを組織に簡単に追加できます。

- 新しいユーザーを追加するときと同様の方法で Azure AD に[新しいゲスト ユーザーを作成](b2b-quickstart-add-guest-users-portal.md)する。
- ゲスト ユーザーをアプリまたはグループに割り当てる。
- 引き換えリンクを含む招待メールを送信するか、共有するアプリへの直接リンクを送信する。

![[新しいゲスト ユーザー] 招待入力ページを示すスクリーンショット](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- ゲスト ユーザーは、いくつかの簡単な[引き換え手順](redemption-experience.md)に従ってサインインします。

![[アクセス許可の確認] ページのスクリーンショット](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>ポリシーを使用してアプリとサービスを安全に共有する

承認ポリシーを使用して、会社のコンテンツを保護できます。 多要素認証などの条件付きアクセス ポリシーを次のように適用できます。

- テナント レベルで。
- アプリケーション レベルで。
- 会社のアプリケーションとデータを保護する特定のユーザーに対して。

![[条件付きアクセス] オプションを示すスクリーンショット](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>アプリケーションとグループの所有者が自分のゲスト ユーザーを管理できるようにする

ゲスト ユーザーの管理をアプリケーションの所有者に委任できます。これにより、所有者は、共有したいアプリケーションにゲストユーザーを直接追加できるようになります。アプリケーションは Microsoft のapplicationでもそれ以外でもかまいません。

- 管理者は、セルフサービス アプリとグループ管理を設定します。
- 管理者以外のユーザーは、自分の[アクセス パネル](https://myapps.microsoft.com)を使用して、アプリケーションまたはグループにゲスト ユーザーを追加します。

![ゲスト ユーザーのアクセス パネルを示すスクリーンショット](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>B2B ゲスト ユーザーのオンボード エクスペリエンスをカスタマイズする

貴社のニーズに応じて、外部のパートナーの受け入れ方法をカスタマイズすることができます。

- [Azure AD のエンタイトルメント管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)を使用して、[外部ユーザーのアクセスを管理するポリシー](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users)を構成します。
- [B2B コラボレーションの招待 API シリーズ](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)を使用して、オンボード エクスペリエンスをカスタマイズします。

## <a name="integrate-with-identity-providers"></a>ID プロバイダーと統合する

Azure AD は、Facebook、Microsoft アカウント、Google、エンタープライズ ID プロバイダーなどの外部 ID プロバイダーをサポートしています。 ID プロバイダーとのフェデレーションを設定すると、外部ユーザーは既存のソーシャル アカウントまたはエンタープライズ アカウントを使用してサインインでき、アプリケーション専用の新しいアカウントを作成せずにすみます。 さらに外部 ID の ID プロバイダーについて説明します。

![ID プロバイダーのページを示すスクリーンショット](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow-preview"></a>セルフサービス サインアップのユーザー フローを作成する (プレビュー)

セルフサービス サインアップ ユーザー フローを使用すると、アプリにアクセスしようとする外部ユーザーのためにサインアップ エクスペリエンスを作成できます。 サインアップ フローの一部として、さまざまなソーシャル ID プロバイダーまたはエンタープライズ ID プロバイダーのオプションを提供したり、ユーザーに関する情報を収集したりすることができます。 セルフサービス サインアップとその設定方法については、[こちら](self-service-sign-up-overview.md)を参照してください。

また、[API コネクタ](api-connectors-overview.md)を使用して、セルフサービス サインアップ ユーザー フローを外部クラウド システムと統合することもできます。 カスタム承認ワークフローを使用して接続したり、本人確認を実行したり、ユーザー指定の情報を検証したりできます。

![ユーザー フロー ページを示すスクリーンショット](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>次のステップ

- [Azure AD B2B コラボレーションのライセンスに関するガイダンス](licensing-guidance.md)
- [ポータルで B2B コラボレーションのゲスト ユーザーを追加する](add-users-administrator.md)
- [招待の受諾プロセスを理解する](redemption-experience.md)
