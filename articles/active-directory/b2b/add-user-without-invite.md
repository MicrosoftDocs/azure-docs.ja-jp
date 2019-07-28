---
title: 招待リンクまたはメールを使用せずに B2B ゲストを追加する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションの招待を利用しないでゲスト ユーザーが他のゲスト ユーザーを Azure AD に追加することができます。
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.subservice: b2b
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81aad3ef9a4a53532d19fdb81bc48fc50931d49c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056067"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>招待リンクまたはメールを使用せずに B2B コラボレーション ゲスト ユーザーを追加する

共有アプリに直接リンクを送信してゲスト ユーザーを招待できるようになりました。 この方法を使用すると、特殊な場合を除き、ゲスト ユーザーは招待メールを使用する必要がありません。 ゲスト ユーザーがアプリのリンクをクリックしてプライバシー条項を確認し同意すると、アプリにシームレスにアクセスできます。 詳細については、「[B2B コラボレーションの招待の利用](redemption-experience.md)」を参照してください。   

この新しい方法が使用できるようになる前は、招待メールを使用せずゲスト ユーザーを招待するには、(自分の組織または取引先組織からの) 招待元を**ゲストの招待元**ディレクトリ ロールに追加し、UI または PowerShell を通して招待元がゲスト ユーザーをディレクトリ、グループまたはアプリケーションに追加していました。 (PowerShell を使用する場合は、招待メールを使用する必要はありません)。 例:

1. ホスト組織のユーザー (たとえば、WoodGrove) が、パートナー組織の 1 人のユーザー (たとえば、Sam@litware.com) をゲストとして招待します。
2. ホスト組織の管理者は、Sam が取引先組織 (Litware) からの他のユーザーを特定し追加できるよう[ポリシーを設定](delegate-invitations.md)します。 (Sam を**ゲストの招待元**ロールに追加する必要があります。)
3. これで、Sam は Litware の他のユーザーを WoodGrove のディレクトリ、グループ、またはアプリケーションに追加できます。招待に応じる操作は必要ありません。 Sam が Litware での適切な列挙権限を持っていれば、自動的に処理されます。
 
この元の方法は引き続き使用できます。 ただし、動作に小さな違いがあります。 PowerShell を使用した場合、招待されたゲスト アカウントですぐに **Accepted** と表示されず、**PendingAcceptance** と表示されます。 状態は保留中になりますが、ゲスト ユーザーは、招待メールの中のリンクをクリックしなくても、引き続きサインインし、アプリケーションにアクセスできます。 保留中の状態は、ユーザーが、まだ、[同意操作](redemption-experience.md#consent-experience-for-the-guest)を実行し招待元の組織のプライバシー条項に同意していないことを意味します。 ゲスト ユーザーが最初にサインインしたときに、この同意画面が表示されます。 

ディレクトリにユーザーを招待した場合、ゲスト ユーザーは、リソース テナント固有の Azure Portal URL (https://portal.azure.com/*resourcetenant* onmicrosoft.com など) に直接アクセスしてプライバシー条項を読み、これに同意する必要があります。

## <a name="next-steps"></a>次の手順

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [B2B コラボレーションの招待の利用](redemption-experience.md)
- [Azure Active Directory B2B コラボレーションの招待の委任](delegate-invitations.md)
- [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](add-users-information-worker.md)

