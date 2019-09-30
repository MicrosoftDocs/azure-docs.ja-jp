---
title: グループにメンバーを追加するための一括インポート アップロード - Azure Active Directory |Microsoft Docs
description: Azure Active Directory 管理センターでグループ メンバーを一括して追加します。
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
ms.openlocfilehash: 0335522b398c22fb395305b33bac5a56ba565ee2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910679"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Azure Active Directory でのグループ メンバーの一括インポート (プレビュー)

Azure Active Directory (Azure AD) ポータルでは、コンマ区切り値 (CSV) ファイルを使用してグループ メンバーを一括インポートすることで、多数のメンバーをグループに追加できます。

> [!NOTE]
> Azure AD 一括操作は Azure AD のパブリック プレビュー機能であり、すべての有料 Azure AD ライセンス プランに付属します。 プレビューの使用条件について詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="bulk-import-service-limits"></a>一括インポート サービスの制限

グループ メンバーの一覧をインポートする各一括アクティビティは、最大 1 時間実行できます。 これにより、少なくとも 4 万人のメンバーの一覧をインポートできます。

## <a name="to-bulk-import-group-members"></a>グループ メンバーを一括インポートするには

1. 組織のユーザー管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。 グループの所有者も、所有しているグループのメンバーを一括インポートできます。
1. Azure AD で、 **[グループ]**  >  **[すべてのグループ]** の順に選択します。
1. メンバーを追加するグループを開き、 **[メンバー]** を選択します。
1. **[メンバー]** ページで、 **[メンバーのインポート]** を選択して、グループにインポートするメンバーの一覧を含む CSV ファイルをダウンロード、更新、およびアップロードします。

   ![[メンバーのインポート] コマンドは、グループの [プロファイル] ページにあります](./media/groups-bulk-import-members/import-panel.png)

## <a name="check-import-status"></a>インポートの状態を確認する

保留中のすべての一括要求の状態は、 **[一括操作の結果 (プレビュー)]** ページで確認できます。

   ![[一括操作の結果] ページに、一括要求の状態が表示されます](./media/groups-bulk-import-members/bulk-center.png)

## <a name="next-steps"></a>次の手順

- [グループ メンバーを一括削除する](groups-bulk-remove-members.md)
- [グループ メンバーをダウンロードする](groups-bulk-download-members.md)
