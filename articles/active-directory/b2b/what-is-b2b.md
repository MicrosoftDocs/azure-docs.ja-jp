---
title: Azure Active Directory B2B コラボレーションとは - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションでは、リソースを外部パートナーと安全に共有してコラボレーションできるように、ゲスト ユーザー アクセスをサポートしています。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 09/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c5c133fe728126ea07834c8ff5b02b1203ec326
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112689"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Azure Active Directory B2B のゲスト ユーザー アクセスとは

Azure Active Directory (Azure AD) の企業間 (B2B) コラボレーションによって、貴社のデータに対するコントロールを維持した状態で、他の組織からアクセスするゲスト ユーザーとアプリケーションとサービスを安全に共有できます。 外部パートナーの規模に関係なく、Azure AD を所有していない場合や IT 部門が存在していない場合でも、外部パートナーとセキュリティで保護された安全な状態で作業できます。 単純な招待と受諾プロセスによって、パートナーは各自の資格情報を使用して、貴社のリソースにアクセスできます。 開発者は、Azure AD の B2B API を使用して、招待プロセスをカスタマイズしたり、セルフサービス サインアップ ポータルなどのアプリケーションを作成ししたりできます。

次のビデオを視聴して、ゲスト ユーザーが各自の ID を使用して貴社のアプリとサービスにサインインするように招待することで、ゲスト ユーザーと安全にコラボレーションする方法を確認してください。

次のビデオでは、役に立つ概要を提供します。

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="collaborate-with-any-partner-using-their-identities"></a>パートナーの ID を使用してパートナーとコラボレーションする
Azure AD B2B では、パートナーが各自の ID 管理ソリューションを使用するため、組織には外部管理を行うためのオーバーヘッドがありません。 
- パートナーは、各自の ID と資格情報を使用するため、Azure AD は必要ありません。 
- 外部アカウントまたはパスワードを管理する必要はありません。 
- アカウントの同期もアカウントのライフ サイクルの管理も必要ありません。  

![[メンバーの追加] ページを示すスクリーンショット](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>単純な招待と受諾プロセスを使用してゲスト ユーザーを招待する
ゲスト ユーザーは、各自の職場、学校、またはソーシャルの ID を使用して、アプリとサービスにサインインします。 ゲスト ユーザーが Microsoft アカウントも Azure AD アカウントも持っていない場合は、招待を受諾した時点でアカウントが作成されます。 
- 任意の電子メール ID を使用して、ゲスト ユーザーを招待します。
- アプリへの直接リンクを送信するか、ゲスト ユーザーのアクセス パネルに招待を送信します。 
- ゲスト ユーザーは、いくつかの簡単な受諾手順に従ってサインインします。

![[アクセス許可の確認] ページのスクリーンショット](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>ポリシーを使用してアプリとサービスを安全に共有する
貴社のコンテンツを保護する承認ポリシーを使用できます。 多要素認証などの条件付きアクセス ポリシーを次のように適用できます。
- テナント レベルで。
- アプリケーション レベルで。
- 会社のアプリケーションとデータを保護する特定のユーザーに対して。

![[条件付きアクセス] オプションを示すスクリーンショット](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>Azure AD ポータルでゲスト ユーザーを簡単に追加する

管理者は、Azure portal でゲスト ユーザーを組織に簡単に追加できます。
- 新しいユーザーを追加する方法と同じような方法で Azure AD に新しいゲスト ユーザーを追加します。
- ゲスト ユーザーは、ゲスト ユーザーのアクセス パネルにサインインできるようにするカスタマイズ可能な招待をすぐに受信します。
- ディレクトリ内のゲスト ユーザーにアプリまたはグループを割り当てることができます。  

![[新しいゲスト ユーザー] 招待入力ページを示すスクリーンショット](media/what-is-b2b/adding-b2b-users-admin.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>アプリケーションとグループの所有者が自分のゲスト ユーザーを管理できるようにする

ゲスト ユーザーの管理をアプリケーションの所有者に委任できます。これにより、所有者は、共有したいアプリケーションにゲストユーザーを直接追加できるようになります。アプリケーションは Microsoft のapplicationでもそれ以外でもかまいません。 
 - 管理者は、セルフサービス アプリとグループ管理を設定します。
 - 管理者以外のユーザーは、自分の[アクセス パネル](https://myapps.microsoft.com)を使用して、アプリケーションまたはグループにゲスト ユーザーを追加します。

![ゲスト ユーザーのアクセス パネルを示すスクリーンショット](media/what-is-b2b/access-panel-manage-app.png)

## <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>API とサンプル コードを使用した、ユーザー受け入れのためのアプリケーションの容易な構築

貴社のニーズに応じて、外部のパートナーの受け入れ方法をカスタマイズすることができます。
- [B2B コラボレーションの招待 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) を使用すると、セルフサービス サインアップ ポータルの作成など、オンボーディング エクスペリエンスをカスタマイズすることができます。 
- [GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) に提供されているセルフサービス ポータル用のサンプル コードを使用します。

![サンプルのサインアップ ポータルを示すスクリーンショット](media/what-is-b2b/sign-up-portal.png)

## <a name="next-steps"></a>次の手順

- [Azure AD B2B コラボレーションのライセンスに関するガイダンス](licensing-guidance.md)
- [ポータルで B2B コラボレーションのゲスト ユーザーを追加する](add-users-administrator.md)
- [招待の受諾プロセスを理解する](redemption-experience.md)
- また、フィードバックやご意見、ご提案があれば、[Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b) からいつでも製品チームにお寄せください。
