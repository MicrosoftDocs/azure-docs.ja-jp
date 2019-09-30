---
title: グループ メンバーシップの一覧を一括でダウンロードする - Azure Active Directory ポータル | Microsoft Docs
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
ms.openlocfilehash: 120b5606dd6b6f4451b563843f112bd614341eac
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914788"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Azure Active Directory のグループのメンバーの一括ダウンロード (プレビュー)

Azure Active Directory (Azure AD) ポータルを利用し、組織のグループ メンバーをコンマ区切り値 (CSV) ファイルに一括ダウンロードできます。

> [!NOTE]
> Azure AD の一括操作は Azure AD のパブリック プレビュー機能であり、すべての有料 Azure AD ライセンス プランに付属します。 プレビューの使用条件について詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="bulk-download-service-limits"></a>一括ダウンロード サービスの制限

グループ メンバーの一覧をダウンロードする各一括アクティビティは、最大 1 時間実行できます。 これにより、少なくとも 50 万人のメンバーの一覧をダウンロードできます。

## <a name="to-bulk-download-group-membership"></a>グループ メンバーを一括ダウンロードするには

1. 組織のユーザー管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。 グループの所有者は、所有しているグループのメンバーを一括ダウンロードすることもできます。
1. Azure AD で、 **[グループ]**  >  **[すべてのグループ]** の順に選択します。
1. メンバーシップをダウンロードするグループを開き、 **[メンバー]** を選択します。
1. **[メンバー]** ページで **[メンバーのダウンロード]** を選択し、グループ メンバーの一覧を含む CSV ファイルをダウンロードします。

   ![[メンバーのダウンロード] コマンドは、グループの [プロファイル] ページにあります](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>ダウンロードの状態を確認する

**[一括操作の結果 (プレビュー)]** ページでは、保留中のすべての一括要求の状態を確認できます。

   ![[一括操作の結果] ページに、一括要求の状態が表示されます](./media/groups-bulk-download-members/bulk-center.png)

## <a name="next-steps"></a>次の手順

- [グループ メンバーを一括インポートする](groups-bulk-import-members.md)
- [グループ メンバーの一括削除](groups-bulk-download-members.md)
