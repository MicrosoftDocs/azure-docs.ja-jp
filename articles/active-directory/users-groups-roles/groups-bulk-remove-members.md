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
ms.openlocfilehash: 9d384ea4749e2d0bc7edf8df7ac0508566f2f76b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517090"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Azure Active Directory でのグループ メンバーの一括削除 (プレビュー)

Azure Active Directory (Azure AD) ポータルでは、コンマ区切り値 (CSV) ファイルを使用してグループ メンバーを一括削除することで、多数のメンバーをグループから削除できます。

## <a name="to-bulk-remove-group-members"></a>グループ メンバーを一括削除するには

1. 組織のユーザー管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。 グループの所有者も、所有しているグループのメンバーを一括削除できます。
1. Azure AD で、 **[グループ]**  >  **[すべてのグループ]** の順に選択します。
1. メンバーを削除するグループを開き、 **[メンバー]** を選択します。
1. **[メンバー]** ページで、 **[メンバーの削除]** を選択します。
1. **[グループ メンバーの一括削除 (プレビュー)]** ページで、 **[ダウンロード]** を選択して、必要なグループ メンバー プロパティを含む CSV ファイル テンプレートを取得します。

   ![[メンバーの削除] コマンドは、グループの [プロファイル] ページにあります](./media/groups-bulk-remove-members/remove-panel.png)

1. CSV ファイルを開き、グループから削除する各グループ メンバーの行を追加します (必要な値は、メンバー オブジェクト ID またはユーザー プリンシパル名です)。 そのうえでファイルを保存します。

   ![CSV ファイルには、削除するメンバーの名前と ID が含まれている](./media/groups-bulk-remove-members/csv-file.png)

1. **[グループ メンバーの一括削除 (プレビュー)]** ページの **[csv ファイルをアップロードします]** で、そのファイルを参照します。 ファイルを選択すると、.csv ファイルの検証が開始されます。
1. ファイルの内容が検証されると、一括インポート ページに "**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
1. ファイルが検証に合格したら、 **[送信]** を選択して、グループ メンバーをグループから削除する Azure の一括操作を開始します。
1. 削除操作が完了すると、一括操作が成功したという通知が表示されます。

## <a name="check-removal-status"></a>削除の状態を確認する

保留中のすべての一括要求の状態は、 **[一括操作の結果 (プレビュー)]** ページで確認できます。

   ![[一括操作の結果] ページに、一括要求の状態が表示されます](./media/groups-bulk-remove-members/bulk-center.png)

一括操作に含まれる各行の項目の詳細については、 **[成功数]** 、 **[失敗数]** 、 **[要求数合計]** の各列の値を選択してください。 エラーが発生した場合、その理由が表示されます。

## <a name="bulk-removal-service-limits"></a>一括削除サービスの制限

グループ メンバーの一覧を削除する各一括アクティビティは、最大 1 時間実行できます。 これにより、少なくとも 4 万人のメンバーの一覧を削除できます。

## <a name="next-steps"></a>次の手順

- [グループ メンバーを一括インポートする](groups-bulk-import-members.md)
- [グループ メンバーをダウンロードする](groups-bulk-download-members.md)
- [全グループの一覧をダウンロードする](groups-bulk-download.md)
