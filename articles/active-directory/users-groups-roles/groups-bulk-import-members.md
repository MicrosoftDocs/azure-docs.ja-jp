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
ms.openlocfilehash: 8ff4234d9fd28e655e868108e37b09c5afe2767c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517131"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Azure Active Directory でのグループ メンバーの一括インポート (プレビュー)

Azure Active Directory (Azure AD) ポータルでは、コンマ区切り値 (CSV) ファイルを使用してグループ メンバーを一括インポートすることで、多数のメンバーをグループに追加できます。

## <a name="to-bulk-import-group-members"></a>グループ メンバーを一括インポートするには

1. 組織のユーザー管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。 グループの所有者も、所有しているグループのメンバーを一括インポートできます。
1. Azure AD で、 **[グループ]**  >  **[すべてのグループ]** の順に選択します。
1. メンバーを追加するグループを開き、 **[メンバー]** を選択します。
1. **[メンバー]** ページで、 **[メンバーのインポート]** を選択します。
1. **[一括インポートのグループ メンバー (プレビュー)]** ページで、 **[ダウンロード]** を選択して、必要なグループ メンバー プロパティを含む CSV ファイル テンプレートを取得します。

    ![[メンバーのインポート] コマンドは、グループの [プロファイル] ページにあります](./media/groups-bulk-import-members/import-panel.png)

1. CSV ファイルを開き、グループにインポートする各グループ メンバーの行を追加します (必要な値は、**メンバー オブジェクト ID** または**ユーザー プリンシパル名**です)。 そのうえでファイルを保存します。

   ![CSV ファイルには、インポートするメンバーの名前と ID が含まれています](./media/groups-bulk-import-members/csv-file.png)

1. **[一括インポートのグループ メンバー (プレビュー)]** ページの **[csv ファイルをアップロードします]** で、そのファイルを参照します。 ファイルを選択すると、CSV ファイルの検証が開始されます。
1. ファイルの内容が検証されると、一括インポート ページに "**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
1. ファイルが検証に合格したら、 **[送信]** を選択して、グループ メンバーをグループにインポートする Azure の一括操作を開始します。
1. インポート操作が完了すると、一括操作が成功したという通知が表示されます。

## <a name="check-import-status"></a>インポートの状態を確認する

保留中のすべての一括要求の状態は、 **[一括操作の結果 (プレビュー)]** ページで確認できます。

   ![[一括操作の結果] ページに、一括要求の状態が表示されます](./media/groups-bulk-import-members/bulk-center.png)

一括操作に含まれる各行の項目の詳細については、 **[成功数]** 、 **[失敗数]** 、 **[要求数合計]** の各列の値を選択してください。 エラーが発生した場合、その理由が表示されます。

## <a name="bulk-import-service-limits"></a>一括インポート サービスの制限

グループ メンバーの一覧をインポートする各一括アクティビティは、最大 1 時間実行できます。 これにより、少なくとも 4 万人のメンバーの一覧をインポートできます。

## <a name="next-steps"></a>次の手順

- [グループ メンバーを一括削除する](groups-bulk-remove-members.md)
- [グループ メンバーをダウンロードする](groups-bulk-download-members.md)
- [全グループの一覧をダウンロードする](groups-bulk-download.md)
