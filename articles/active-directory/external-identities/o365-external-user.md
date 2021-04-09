---
title: Microsoft 365 の外部共有と B2B コラボレーション - Azure AD
description: Microsoft 365 および Azure Active Directory B2B コラボレーションを使用する外部パートナーとのリソースの共有について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: d003008ea5b0d2591574f6f488b0145ee6f08a5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100008130"
---
# <a name="microsoft-365-external-sharing-and-azure-active-directory-azure-ad-b2b-collaboration"></a>Microsoft 365 の外部共有と Azure Active Directory (Azure AD) B2B コラボレーション

Azure AD B2B コラボレーションと Microsoft 365 の外部共有 (OneDrive、SharePoint Online、統合グループなど) では、外部ユーザーは Azure AD B2B を使用して認証されます。

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Azure AD B2B と SharePoint Online の外部共有との違い

OneDrive/SharePoint Online には、独自の招待マネージャーがあります。 OneDrive/SharePoint Online での外部共有のサポートは、Azure AD でのサポートより前に開始されました。 時間の経過と共に、OneDrive/SharePoint Online の外部共有にはいくつかの機能が追加され、製品の組み込み共有パターンを使用するユーザーが数百万人に増加しました。 ただし、OneDrive/SharePoint Online の外部共有の動作と Azure AD B2B コラボレーションの動作の間に、次のようないくつかの微妙な違いがあります。 OneDrive/SharePoint Online の外部共有については、「[外部共有の概要](/sharepoint/external-sharing-overview)」で詳しく説明されています。 通常、Azure AD B2B とは以下の点でプロセスが異なります。

- OneDrive/SharePoint Online は、ユーザーが招待に応じた後で、ユーザーをディレクトリに追加します。 そのため、応じる前は Azure AD ポータルにユーザーが表示されません。 その間に別のサイトがユーザーを招待すると、新しい招待が生成されます。 一方、Azure AD B2B コラボレーションを使用している場合は、ユーザーがすべての場所に表示されるように、招待後すぐにユーザーが追加されます。

- OneDrive/SharePoint Online で招待に応じるためのエクスペリエンスは、Azure AD B2B コラボレーションでのエクスペリエンスとは外観が異なります。 ユーザーが招待に応じた後のエクスペリエンスは似ています。

- Azure AD B2B コラボレーションで招待されたユーザーは、OneDrive/SharePoint Online 共有の各ダイアログ ボックスで選択できます。 OneDrive/SharePoint Online で招待されたユーザーも、招待に応じた後は、Azure AD に表示されます。

- ライセンスの要件が異なります。 ライセンスについて詳しくは、[Azure AD External Identities のライセンス](./external-identities-pricing.md)と、[SharePoint Online の外部共有の概要](/sharepoint/external-sharing-overview)に関するページをご覧ください。
OneDrive/SharePoint Online と Azure AD B2B コラボレーションの外部共有を管理するには、OneDrive/SharePoint Online の外部共有の設定を **[Allow sharing only with the external users that already exist in your organization's directory]\(既にディレクトリに存在する外部ユーザーのみに共有を許可する\)** に設定します。 ユーザーは外部の共有サイトに移動し、管理者が追加した外部コラボレーターを選択できます。 管理者は、B2B コラボレーションの招待 API を通じて、外部コラボレーターを追加できます。


![OneDrive/SharePoint の外部共有の設定](media/o365-external-user/odsp-sharing-setting.png)

外部共有を有効にした後、SharePoint Online (SPO) のユーザー選択ウィンドウで既存のゲスト ユーザーを検索する機能は、従来の動作と一致させるために、既定ではオフになっています。

この機能は、テナントとサイト コレクション レベルで 'ShowPeoplePickerSuggestionsForGuestUsers' 設定を使用することで有効にできます。 この機能は、Set-SPOTenant コマンドレットと Set-SPOSite コマンドレットを使用して設定できます。これにより、メンバーは、ディレクトリ内のすべての既存のゲスト ユーザーを検索することができます。 テナントのスコープの変更は、既にプロビジョニングされている SPO サイトには影響しません。

## <a name="next-steps"></a>次のステップ

* [Azure AD B2B コラボレーションとは](what-is-b2b.md)
* [B2B コラボレーション ユーザーのロールへの追加](add-guest-to-role.md)
* [B2B コラボレーションの招待の委任](delegate-invitations.md)
* [動的グループと B2B コラボレーション](use-dynamic-groups.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](troubleshoot.md)
