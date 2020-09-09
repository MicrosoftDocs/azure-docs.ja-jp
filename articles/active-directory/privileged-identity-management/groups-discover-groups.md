---
title: ロールを割り当て可能なグループを特権アクセス グループとして管理する - Azure AD | Microsoft Docs
description: Privileged Identity Management (PIM) で特権アクセス グループとして管理するために、ロールを割り当て可能なグループのオンボードに同意します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/17/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: a707c66f2562fe442c58ce6292e51c4e67a20ae2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869535"
---
# <a name="manage-privileged-access-groups-preview-in-privileged-identity-management"></a>Privileged Identity Management で特権アクセス グループ (プレビュー) を管理する

Azure Active Directory (Azure AD) では、Azure AD 組み込みロールをクラウド グループに割り当てて、ロールの割り当てを管理する方法を簡素化することができます。 Azure AD のロールを保護し、アクセスをセキュリティで保護するために、Privileged Identity Management (PIM) を使用して、これらのグループのメンバーまたは所有者の Just-In-Time アクセスを管理できるようになりました。 Azure AD のロールを割り当て可能なグループを特権アクセス グループとして Privileged Identity Management で管理するには、これを PIM で管理下に置く必要があります。

## <a name="identify-groups-to-manage"></a>管理するグループを識別する

「[Azure Active Directory でロールを割り当て可能なグループを作成する](../users-groups-roles/roles-groups-create-eligible.md)」の説明に従って、Azure AD でロールを割り当て可能なグループを作成できます。 グループを Privileged Identity Management で管理下に置くには、そのグループの所有者である必要があります。

1. 特権ロール管理者のアクセス許可で [Azure AD にサインインします](https://aad.portal.azure.com)。
1. **[グループ]** を選択し、管理対象とする、ロールを割り当て可能なグループを選択します。 リストを検索またはフィルター処理することができます。

    ![PIM で管理する、ロールを割り当て可能なグループを見つける](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. グループを開き、 **[特権アクセス (プレビュー)]** を選択します。

    ![Privileged Identity Management エクスペリエンスを開く](./media/groups-discover-groups/groups-discover-groups.png)

1. グループが PIM で管理下に置かれていない場合は、 **[特権アクセスの有効化]** を選択して、管理に同意します。 この同意は、全体管理者またはグループの所有者のみが与えることができるものです。

    ![必要に応じて、Privileged Identity Management でグループを管理することに同意します](./media/groups-discover-groups/consent-page.png)

1. PIM での割り当ての管理を開始します。

    ![Privileged Identity Management で割り当てを管理する](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> 特権アクセス グループは、いったん管理されると、これを管理から除外することはできません。 これにより、別の管理者は、Privileged Identity Management 設定を削除できなくなります。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で特権アクセス グループの割り当てを構成する](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management で特権アクセス グループを割り当てる](pim-resource-roles-assign-roles.md)
