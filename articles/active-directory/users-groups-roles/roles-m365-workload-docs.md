---
title: Microsoft 365 ワークロードの管理者ロール コンテンツ - Azure AD | Microsoft Docs
description: Azure Active Directory で Microsoft 365 ワークロードの管理者ロールのコンテンツおよび API リファレンスを検索する
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7460c3a7d957634d14186d313994c65d89fa4a32
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217872"
---
# <a name="administrator-roles-for-microsoft-365-workloads"></a>Microsoft 365 ワークロードの管理者ロール

Microsoft 365 のすべての製品は、Azure AD の管理者ロールで管理できます。 一部の製品では、その製品に固有の追加のロールも提供されます。 各製品でサポートされるロールについては、次の表を参照してください。 委任の問題に関する一般的な説明については、[Azure Active Directory でのロールの委任計画](roles-concept-delegation.md)に関するページを参照してください。

## <a name="where-to-find-content"></a>コンテンツの参照先

Microsoft 365 ワークロード | ロール コンテンツ | API コンテンツ
---------------------- | ------------------ | -----------------
Office 365 と Microsoft 365 のビジネス プランでの管理者ロール | [Office 365 の管理者ロール](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | 使用できません。
Azure Active Directory (Azure AD) および Azure AD Identity Protection| [Azure AD 管理者ロール](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Exchange のロール ベースのアクセス制御](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell for Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[ロールの割り当てのフェッチ](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Azure AD 管理者ロール](directory-assign-admin-roles.md)<br>[Office 365 での SharePoint 管理者ロールについて](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
チーム/Skype for Business | [Azure AD 管理者ロール](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
セキュリティ/コンプライアンス センター (Office 365 の Advanced Threat Protection、Exchange Online Protection、Information Protection) | [Office 365 の管理者ロール](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[ロールの割り当てのフェッチ](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
セキュリティ スコア | [Azure AD 管理者ロール](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance Manager | [Compliance Manager ロール](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | 使用できません。
Azure Information Protection | [Azure AD 管理者ロール](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [ロールベースのアクセス制御](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API リファレンス](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP ロール グループ](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | 使用できません。
Windows Defender Advanced Threat Protection | [Windows Defender ATP のロール ベースのアクセス制御](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | 使用できません。
Privileged Identity Management | [Azure AD 管理者ロール](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune のロール ベースのアクセス制御](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[ロールの割り当てのフェッチ](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
管理対象デスクトップ | [Azure AD 管理者ロール](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>次の手順

* [Azure AD 管理者ロールの割り当てと削除の方法](directory-manage-roles-portal.md)
* [Azure AD 管理者ロールのリファレンス](directory-assign-admin-roles.md)
