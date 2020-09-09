---
title: Microsoft 365 サービス全体の管理者ロール ドキュメント - Azure AD | Microsoft Docs
description: Azure Active Directory で Microsoft 365 サービスの管理者ロールのコンテンツおよび API リファレンスを検索する
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14d5b458aeccd2587f24b30548c4b5e76912bce1
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798280"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Microsoft 365 サービスの管理者ロール

Microsoft 365 のすべての製品は、Azure AD の管理者ロールで管理できます。 一部の製品では、その製品に固有の追加のロールも提供されます。 各製品でサポートされるロールについては、次の表を参照してください。 委任の問題に関する一般的な説明については、[Azure Active Directory でのロールの委任計画](roles-concept-delegation.md)に関するページを参照してください。

## <a name="where-to-find-content"></a>コンテンツの参照先

Microsoft 365 サービス | ロール コンテンツ | API コンテンツ
---------------------- | ------------------ | -----------------
Office 365 と Microsoft 365 のビジネス プランでの管理者ロール | [Office 365 の管理者ロール](/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | 使用不可
Azure Active Directory (Azure AD) および Azure AD Identity Protection| [Azure AD 管理者ロール](directory-assign-admin-roles.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Exchange のロール ベースのアクセス制御](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell for Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[ロールの割り当てのフェッチ](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Azure AD 管理者ロール](directory-assign-admin-roles.md)<br>[Office 365 での SharePoint 管理者ロールについて](/sharepoint/sharepoint-admin-role) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0)
チーム/Skype for Business | [Azure AD 管理者ロール](directory-assign-admin-roles.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0)
セキュリティ/コンプライアンス センター (Office 365 の Advanced Threat Protection、Exchange Online Protection、Information Protection) | [Office 365 の管理者ロール](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[ロールの割り当てのフェッチ](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
セキュリティ スコア | [Azure AD 管理者ロール](directory-assign-admin-roles.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance Manager | [Compliance Manager ロール](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | 使用不可
Azure Information Protection | [Azure AD 管理者ロール](directory-assign-admin-roles.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [ロールベースのアクセス制御](/cloud-app-security/manage-admins) | [API リファレンス](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP ロール グループ](/azure-advanced-threat-protection/atp-role-groups) | 使用不可
Windows Defender Advanced Threat Protection | [Windows Defender ATP のロール ベースのアクセス制御](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | 使用不可
Privileged Identity Management | [Azure AD 管理者ロール](directory-assign-admin-roles.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune のロール ベースのアクセス制御](/intune/role-based-access-control) | [Graph API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[ロールの割り当てのフェッチ](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
管理対象デスクトップ | [Azure AD 管理者ロール](directory-assign-admin-roles.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[ロールの割り当てのフェッチ](/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>次のステップ

* [Azure AD 管理者ロールの割り当てと削除の方法](directory-manage-roles-portal.md)
* [Azure AD 管理者ロールのリファレンス](directory-assign-admin-roles.md)