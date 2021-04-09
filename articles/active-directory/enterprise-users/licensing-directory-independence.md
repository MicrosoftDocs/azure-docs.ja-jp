---
title: 複数のテナント間の対話の特性 - Azure AD | Microsoft Docs
description: Azure Active Directory 組織のデータの独立性を理解する
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce3e4c6a7708fba15560564577c9b01722c8aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96548054"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>複数の Azure Active Directory 組織が対話する方法を理解する

Azure Active Directory (Azure AD) では、各 Azure AD 組織は完全に独立しています。つまり、管理されている他の Azure AD 組織から論理的に独立したピアです。 この組織間の独立性には、リソースの独立性、管理の独立性、同期の独立性が含まれます。 組織間に親子の関係はありません。

## <a name="resource-independence"></a>リソースの独立

* ある組織で Azure AD リソースを作成または削除しても、外部ユーザーの一部の例外を除き、別の組織内のどのリソースにも影響を与えません。
* ある組織にドメイン名のいずれかを登録しても、それは他のどの組織でも使用できません。

## <a name="administrative-independence"></a>管理上の独立

組織 'Contoso' の管理者以外のユーザーがテスト組織 'Test' を作成した場合は、次のようになります。

* 既定では、組織を作成したユーザーはその新しい組織に外部ユーザーとして追加され、その組織内のグローバル管理者ロールが割り当てられます。
* 'Test' の管理者が管理者特権を明示的に付与しない限り、組織 'Contoso' の管理者には組織 'Test' に対する直接の管理者特権が与えられません。 ただし、'Contoso' の管理者は、'Test' を作成したユーザー アカウントを制御しているは組織 'Test' へのアクセスを制御できます。
* ある組織でユーザーの Azure AD ロールを追加または削除した場合、その変更は、他の Azure AD 組織でそのユーザーに割り当てられているロールには影響を与えません。

## <a name="synchronization-independence"></a>同期の独立

各 Azure AD 組織を、次のいずれか 1 つの単一インスタンスからデータが同期されるように独立に構成できます。

* Azure AD Connect ツール。データを 1 つの AD フォレストと同期する場合。
* もう 1 つは、1 つ以上のオンプレミスのフォレストと Azure AD 以外のデータ ソースのいずれか、または両方とデータを同期する Forefront Identity Manager 用 Azure Active Directory コネクタです。

## <a name="add-an-azure-ad-organization"></a>Azure AD 組織を追加する

Azure portal で Azure AD 組織を追加するには、Azure AD グローバル管理者であるアカウントを使用して [ Azure portal](https://portal.azure.com) にサインインし、 **[新規]** を選択します。

> [!NOTE]
> 他の Azure リソースとは異なり、Azure AD 組織は Azure サブスクリプションの子リソースではありません。 Azure サブスクリプションが取り消されたり、期限切れになったりした場合でも、Azure PowerShell、Microsoft Graph API、または Microsoft 365 管理センターを使用して Azure AD 組織のデータに引き続きアクセスできます。 また、[組織に別のサブスクリプションを関連付ける](../fundamentals/active-directory-how-subscriptions-associated-directory.md)こともできます。
>

## <a name="next-steps"></a>次のステップ

Azure AD のライセンスに関する考慮事項とベスト プラクティスについては、[Azure Active Directory のライセンスの概要](../fundamentals/active-directory-licensing-whatis-azure-portal.md)に関するページを参照してください。
