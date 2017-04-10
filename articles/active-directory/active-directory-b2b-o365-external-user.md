---
title: "Office 365 の外部共有と Azure Active Directory B2B コラボレーション | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションの要求マッピング リファレンス"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 6c8d7dc7d070ce65cbe637359bf7933eac68ce23
ms.openlocfilehash: ae87c3487abd194c524d067a2a9b7bb9f8df8ac7
ms.lasthandoff: 02/24/2017


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 の外部共有と Azure Active Directory B2B コラボレーション

Office 365 の外部共有 (OneDrive、SharePoint Online、統合グループなど) と Azure Active Directory (Azure AD) 企業間 (B2B) コラボレーションは、技術的には同じことです。 統合グループのゲストなど、すべての外部共有 (OneDrive/SharePoint Online を除く) は、共有のために既に Azure AD B2B コラボレーションの招待 API を使用しています。

OneDrive/SharePoint Online には、独自の招待マネージャーがあります。 OneDrive/SharePoint Online での外部共有のサポートは、Azure AD でのサポートより前に開始されました。 時間の経過と共に、OneDrive/SharePoint Online の外部共有にはいくつかの機能が追加され、製品の組み込み共有パターンを使用するユーザーが数百万人に増加しました。 すべての製品でプロセスを統一し、Azure AD のすべての革新的な技術を取り入れるために、OneDrive/SharePoint Online に Azure AD B2B の招待 API (このドキュメントで説明しています) を追加する作業を進めています。 それまでの間は、OneDrive/SharePoint Online の外部共有の動作と Azure AD B2B コラボレーションの動作の間に、次のようないくつかの微妙な違いがあります。

- OneDrive/SharePoint Online は、ユーザーが招待に応じた後で、ユーザーをディレクトリに追加します。 そのため、応じる前は Azure AD ポータルにユーザーが表示されません。 その間に別のサイトがユーザーを招待すると、新しい招待が生成されます。 一方、Azure AD B2B コラボレーションを使用している場合は、ユーザーがすべての場所に表示されるように、招待後すぐにユーザーが追加されます。

- OneDrive/SharePoint Online で招待に応じるためのエクスペリエンスは、Azure AD B2B コラボレーションでのエクスペリエンスとは外観が異なります。 ユーザーが招待に応じた後のエクスペリエンスは似ています。

- Azure AD B2B コラボレーションで招待されたユーザーは、OneDrive/SharePoint Online 共有の各ダイアログ ボックスで選択できます。 OneDrive/SharePoint Online で招待されたユーザーも、招待に応じた後は、Azure AD に表示されます。

- 管理者に制御された方法で、OneDrive/SharePoint Online の外部共有を Azure AD B2B コラボレーションと共に使用するには、OneDrive/SharePoint Online の外部共有の設定を**既にディレクトリに存在する外部ユーザーのみに共有を許可する**ように指定します。 ユーザーは外部の共有サイトに移動し、管理者が追加した外部コラボレーターを選択できます。 管理者は、B2B コラボレーションの招待 API を通じて、外部コラボレーターを追加できます。

![OneDrive/SharePoint Online の外部共有の設定](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)
* [B2B コラボレーション ユーザーのロールへの追加](active-directory-b2b-add-guest-to-role.md)
* [B2B コラボレーションの招待の委任](active-directory-b2b-delegate-invitations.md)
* [動的グループと B2B コラボレーション](active-directory-b2b-dynamic-groups.md)
* [B2B コラボレーション コードと PowerShell サンプル](active-directory-b2b-code-samples.md)
* [B2B コラボレーション用の SaaS アプリの構成](active-directory-b2b-configure-saas-apps.md)
* [B2B コラボレーション ユーザーのトークン](active-directory-b2b-user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](active-directory-b2b-claims-mapping.md)
* [B2B コラボレーションの現在の制限](active-directory-b2b-current-limitations.md)

