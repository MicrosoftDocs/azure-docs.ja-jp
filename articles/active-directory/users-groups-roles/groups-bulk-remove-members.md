---
title: CSV ファイルをアップロードしてグループ メンバーを一括削除する - Azure Active Directory | Microsoft Docs
description: Azure 管理センターでユーザーを一括で追加します。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: b153db3570f10ad5ad130dedd0bd20fe22776ed6
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910737"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Azure Active Directory でのグループ メンバーの一括削除 (プレビュー)

Azure Active Directory (Azure AD) ポータルでは、コンマ区切り値 (CSV) ファイルを使用してグループ メンバーを一括削除することで、多数のメンバーをグループから削除できます。

> [!NOTE]
> Azure AD の一括操作は Azure AD のパブリック プレビュー機能であり、すべての有料 Azure AD ライセンス プランに付属します。 プレビューの使用条件について詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="bulk-removal-service-limits"></a>一括削除サービスの制限

グループ メンバーの一覧を削除する各一括アクティビティは、最大 1 時間実行できます。 これにより、少なくとも 4 万人のメンバーの一覧を削除できます。

## <a name="to-bulk-remove-group-members"></a>グループ メンバーを一括削除するには

1. 組織のユーザー管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。 グループの所有者も、所有しているグループのメンバーを一括削除できます。
1. Azure AD で、 **[グループ]**  >  **[すべてのグループ]** の順に選択します。
1. メンバーを削除するグループを開き、 **[メンバー]** を選択します。
1. **[メンバー]** ページで、 **[メンバーの削除]** を選択して、グループから削除するメンバーの一覧を含む CSV ファイルをダウンロード、更新、およびアップロードします。

   ![[メンバーの削除] コマンドは、グループの [プロファイル] ページにあります](./media/groups-bulk-remove-members/remove-panel.png)

## <a name="check-removal-status"></a>削除の状態を確認する

保留中のすべての一括要求の状態は、 **[一括操作の結果 (プレビュー)]** ページで確認できます。

   ![[一括操作の結果] ページに、一括要求の状態が表示されます](./media/groups-bulk-remove-members/bulk-center.png)

## <a name="next-steps"></a>次の手順

- [グループ メンバーを一括インポートする](groups-bulk-import-members.md)
- [グループ メンバーをダウンロードする](groups-bulk-download-members.md)
