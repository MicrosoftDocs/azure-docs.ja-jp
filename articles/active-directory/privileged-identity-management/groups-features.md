---
title: Privileged Identity Management (PIM) での特権 Azure AD グループの管理 | Microsoft Docs
description: Privileged Identity Management (PIM) で特権アクセス グループのメンバーと所有者を管理する方法
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759781fd61cd42d05f2823d390e99d128dd2fcac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512394"
---
# <a name="management-capabilities-for-privileged-access-azure-ad-groups-preview"></a>特権アクセス Azure AD グループの管理機能 (プレビュー)

Privileged Identity Management (PIM) では、特権アクセス グループのメンバーシップまたは所有権の資格を割り当てることができるようになりました。 このプレビュー以降、Azure Active Directory (Azure AD) の組み込みロールをクラウド グループに割り当て、PIM を使用してグループのメンバーと所有者の資格とアクティブ化を管理できます。 Azure AD でのロール割り当て可能グループの詳細については、「[Azure Active Directory でクラウド グループを使用してロールの割り当てを管理する (プレビュー)](../roles/groups-concept.md)」を参照してください。

>[!Important]
> Exchange、セキュリティ/コンプライアンス センター、または SharePoint への管理アクセス用のロールに特権アクセス グループを割り当てるには、[特権アクセス グループ] エクスペリエンスではなく、Azure AD ポータルの **[ロールと管理者]** エクスペリエンスを使用して、そのグループに、アクティブ化の候補となるユーザーまたはグループを含めるようにします。

## <a name="require-different-policies-for-each-role-assignable-group"></a>ロール割り当て可能グループごとに異なるポリシーを要求する

組織によっては、Azure AD 企業間 (B2B) コラボレーションなどのツールを使用して、パートナーを Azure AD 組織にゲストとして招待することがあります。 特権ロールへのすべての割り当てに対して単一の Just-In-Time ポリシーを使用するのではなく、独自のポリシーを持つ 2 つの異なる特権アクセス グループを作成できます。 信頼できる従業員に対してはそれほど厳格でない要件を適用し、パートナーが割り当てられたグループでのアクティブ化を要求するときは、承認ワークフローのような厳しい要件を適用できます。

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>1 つの要求で複数のロール割り当てをアクティブ化する

特権アクセス グループ (プレビュー) を使用すると、ワークロード固有の管理者に対して、単一の Just-In-Time 要求で複数のロールへのクイック アクセスを付与できます。 たとえば、階層 3 の Office 管理者が、毎日インシデントを徹底的に調査するために、Exchange 管理者、Office アプリ管理者、Teams 管理者、および Search 管理者のロールに対する Just-In-Time アクセスを必要とするものとします。 これまでであれば、4 つの連続した要求が必要であり、これは時間がかかるプロセスです。 代わりに、"Tier 3 Office Admins" という名前のロール割り当て可能グループを作成し、前述の 4 つのロール (または任意の Azure AD 組み込みロール) にそれを割り当てて、グループの [アクティビティ] セクションで特権アクセスを有効にすることができます。 特権アクセスを有効にすると、グループのメンバーに対して Just-In-Time の設定を構成し、管理者と所有者を適格として割り当てることができます。 管理者がグループに昇格すると、4 つの Azure AD ロールすべてのメンバーになります。

## <a name="extend-and-renew-group-assignments"></a>グループの割り当てを延長および更新する

所有者またはメンバーの期限付き割り当てを設定するときにまず疑問に思うのは、割り当ての期限が切れるとどうなるかということです。 この新しいバージョンでは、このシナリオに対して 2 つの選択肢があります。

- 延長 - ロールの割り当ての期限が近付くと、ユーザーは Privileged Identity Management を使用してそのロールの割り当ての延長を要求できます
- 更新 - ロールの割り当ての期限が既に切れた場合、ユーザーは Privileged Identity Management を使用してそのロールの割り当ての更新を要求できます

ユーザーが行うこれらの操作には、グローバル管理者または特権ロール管理者からの承認が必要です。 管理者は、これらの有効期限を管理する仕事から解放されます。 延長または更新の要求を待ち、その要求が有効であれば承認するだけで済みます。

## <a name="next-steps"></a>次のステップ

- [特権アクセス グループの所有者またはメンバーを割り当てる](groups-assign-member-owner.md)
- [特権アクセス グループのメンバーと所有者のアクティブ化要求を承認または拒否する](groups-approval-workflow.md)
