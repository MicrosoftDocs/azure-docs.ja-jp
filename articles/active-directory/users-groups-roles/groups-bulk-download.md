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
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517140"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Azure Active Directory でグループの一覧を一括ダウンロードする (プレビュー)

Azure Active Directory (Azure AD) ポータルを利用し、組織の全グループの一覧をコンマ区切り値 (CSV) ファイルに一括ダウンロードできます。

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
