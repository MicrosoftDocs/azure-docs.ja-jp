---
title: オーストラリアとニュージーランドのお客様の識別データの保存 - Azure AD
description: Azure Active Directory によってオーストラリアとニュージーランド国内のお客様の識別関連データがどこに保存されるかについて説明します。
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 498b75a6541da97e57e23465ae6eb23a6c854727
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836956"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure Active Directory でのオーストラリアとニュージーランドのお客様の識別データの保存

Microsoft 365 や Azure などの Microsoft Online サービスをサブスクライブしている場合、識別データは Azure AD により、組織によって提供されたアドレスに基づく地理的な場所に格納されます。 ご自身の顧客識別データがどこに保存されるかについては、Microsoft セキュリティ センターの「[お客様のデータの場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)」セクションを参照してください。

> [!NOTE]
> 顧客識別データは、Azure AD と統合されるサービスとアプリケーションによってアクセスされます。 使用する各サービスとアプリケーションを評価して、その特定のサービスまたはアプリケーションによる顧客識別データの処理方法と、それらが会社のデータ ストレージ要件を満たしているかどうかを確認してください。 Microsoft サービスのデータ保存場所の詳細については、Microsoft セキュリティ センターのデータの「お客様のデータの場所保存」セクションを参照してください。

オーストラリアまたはニュージーランドの住所を提供したお客様の場合、Azure AD では、次のサービスの識別データはオーストラリアのデータセンターに保持されます。 
- Azure AD Directory の管理 
- 認証

その他のすべての Azure AD サービスでは、お客様のデータは、グローバル データセンターに格納されます。 サービスのデータセンターを特定するには、「[Azure Active Directory – お客様のデータの場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)」を参照してください。

## <a name="microsoft-azure-ad-multi-factor-authentication-mfa"></a>Microsoft Azure AD Multi-Factor Authentication (MFA)

MFA では、顧客識別データは、グローバル データセンターに格納されます。 クラウドベースの Azure AD MFA と Azure MFA Server によって収集されて保存されるユーザー情報の詳細については、[Azure Multi-Factor Authentication でのユーザー データの収集](../authentication/concept-mfa-data-residency.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
上記の機能の詳細については、以下の記事を参照してください。
- [Multi-Factor Authentication とは](../authentication/concept-mfa-howitworks.md)