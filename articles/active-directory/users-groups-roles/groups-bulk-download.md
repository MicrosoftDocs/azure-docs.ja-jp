---
title: Azure Active Directory ポータルでグループの一覧をダウンロードする | Microsoft Docs
description: Azure Active Directory の Azure 管理センターでグループ プロパティを一括ダウンロードします。
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
ms.openlocfilehash: 76aafa978732b3f4bae82f8ad3c09e67caf297ae
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146297"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Azure Active Directory でグループの一覧を一括ダウンロードする (プレビュー)

Azure Active Directory (Azure AD) ポータルを利用し、組織の全グループの一覧をコンマ区切り値 (CSV) ファイルに一括ダウンロードできます。

> [!NOTE]
> Azure AD の一括操作は Azure AD のパブリック プレビュー機能であり、すべての有料 Azure AD ライセンス プランに付属します。 プレビューの使用条件について詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="to-download-a-list-of-groups"></a>グループの一覧をダウンロードするには

1. 組織の管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。
1. Azure AD で、 **[グループ]** 、 **[グループのダウンロード]** の順に選択します。
1. **[グループのダウンロード]** ページで **[開始]** を選択すると、グループの一覧を含む CSV ファイルが得られます。

   ![[グループのダウンロード] コマンドは [すべてのグループ] ページにあります。](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>ダウンロードの状態を確認する

**[一括操作の結果 (プレビュー)]** ページでは、保留中のすべての一括要求の状態を確認できます。

   ![[一括操作の結果] ページに、一括要求の状態が表示されます](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>一括ダウンロード サービスの制限

グループの一覧を一括ダウンロードするごとに、最大 1 時間かかることがあります。 少なくとも 300,000 グループの一覧を一括でダウンロードできます。

## <a name="next-steps"></a>次の手順

- [グループ メンバーを一括削除する](groups-bulk-remove-members.md)
- [グループ メンバーをダウンロードする](groups-bulk-download-members.md)
