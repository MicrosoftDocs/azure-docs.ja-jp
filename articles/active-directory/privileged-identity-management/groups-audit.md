---
title: Privileged Identity Management (PIM) で特権アクセス グループの割り当ての監査レポートを表示する - Azure AD |Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で特権アクセス グループの割り当てのアクティビティおよび監査履歴を表示します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94dd5c2579eb7ce96dea70c6354df8ec84125bd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88141671"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Privileged Identity Management における特権アクセス グループの割り当て (プレビュー) の監査アクティビティ履歴

Azure Active Directory (Azure AD) 組織内の Azure 特権アクセス グループのメンバーと所有者のアクティビティ、アクティブ化、および監査履歴を Privileged Identity Management (PIM) で表示できます。

> [!NOTE]
> 組織で、[Azure の委任されたリソース管理](../../lighthouse/concepts/azure-delegated-resource-management.md)を使用するサービス プロバイダーに管理機能を外部委託している場合、そのサービス プロバイダーによって承認されているロールの割り当てはここに表示されません。

特権アクセス グループの監査履歴を表示するには、これらの手順に従います。

## <a name="view-resource-audit-history"></a>リソースの監査履歴を表示する

**[リソースの監査]** では、自分の特権アクセス グループに関連付けられているすべてのアクティビティを確認できます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Privileged access groups (Preview)]\(特権アクセス グループ (プレビュー)\)** を選択します。

1. 監査履歴を表示する特権アクセス グループを選択します。

1. **[アクティビティ]** で、 **[リソースの監査]** を選択します。

1. 定義済みの日付またはカスタムの範囲を使用して履歴をフィルターします。

    ![フィルターを使用したリソースの監査の一覧](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>自分の監査を表示する

**[自分の監査]** を使用すると、特権アクセス グループの個人のロール アクティビティを表示できます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Privileged access groups (Preview)]\(特権アクセス グループ (プレビュー)\)** を選択します。

1. 監査履歴を表示する特権アクセス グループを選択します。

1. **[アクティビティ]** で、 **[自分の監査]** を選択します。

1. 定義済みの日付またはカスタムの範囲を使用して履歴をフィルターします。

    ![現在のユーザーに対する監査の一覧](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>次の手順

- [Privileged Identity Management でグループのメンバーシップと所有権を割り当てる](groups-assign-member-owner.md)
- [PIM で特権アクセス グループの要求を承認または拒否する](groups-approval-workflow.md)
- [PIM で Azure AD ロールの監査履歴を表示する](groups-audit.md)
