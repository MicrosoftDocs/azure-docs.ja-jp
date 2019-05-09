---
title: PIM で管理できないロール - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で管理できないロールについて説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa5fb632ee5fd9c18bde7443e81fe2ef6e5335e4
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579548"
---
# <a name="roles-you-cannot-manage-in-pim"></a>PIM で管理できないロール

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用すると、すべての [Azure AD ロール](../users-groups-roles/directory-assign-admin-roles.md)とすべての [Azure リソース ロール](../../role-based-access-control/built-in-roles.md)を管理できます。 これらのロールには、管理グループ、サブスクリプション、リソース グループ、およびリソースに関連付けられているカスタム ロールも含まれます。 ただし、管理できないロールがいくつかあります。 この記事では、PIM で管理できないロールについて説明します。

## <a name="classic-subscription-administrator-roles"></a>従来のサブスクリプション管理者ロール

PIM では、次の従来のサブスクリプション管理者ロールを管理することはできません。

- アカウント管理者
- サービス管理者
- 共同管理者

従来のサブスクリプション管理者ロールの詳細については、「[従来のサブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。

## <a name="what-about-office-365-admin-roles"></a>Office 365 の管理者ロールの場合

Exchange 管理者と SharePoint 管理者を除く Exchange Online または SharePoint Online 内のロールは Azure AD で表示されないため、PIM で管理することはできません。 これらの Office 365 サービスの詳細については、[Office 365 の管理者ロール](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)に関するページを参照してください。

> [!NOTE]
> SharePoint 管理者は、SharePoint Online 管理センターを通じた SharePoint Online への管理アクセスが可能で、SharePoint Online でのほぼすべてのタスクを実行できます。 対象となるユーザーは、PIM でアクティブにした後に SharePoint 内でこのロールを使用すると、遅延が発生する場合があります。

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [PIM で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)
