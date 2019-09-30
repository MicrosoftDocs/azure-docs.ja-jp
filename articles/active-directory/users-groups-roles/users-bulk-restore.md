---
title: Azure Active Directory ポータルで削除済みユーザーを一括復元する (プレビュー) | Microsoft Docs
description: Azure Active Directory の Azure AD 管理センターで削除済みユーザーを一括復元する
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: f53ade09c5e2e7db0499122526a1de482af9378f
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901351"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Azure Active Directory で削除済みユーザーを一括復元する (プレビュー)

Azure Active Directory (Azure AD) では、ユーザーの一括作成および削除操作、ゲストの一括招待がサポートされています。また、ユーザー、グループ、グループ メンバーのリストのダウンロードがサポートされています。

## <a name="to-bulk-restore-users"></a>ユーザーを一括復元するには

1. 組織のユーザー管理者アカウントで、[ご自身の Azure AD 組織にサインイン](https://aad.portal.azure.com)します。
1. Azure AD で、 **[ユーザー]**  >  **[削除済み]** の順に選択します。
1. **[削除済みのユーザー]** ページで、 **[一括復元]** を選択して、復元するユーザーのプロパティの有効な CSV ファイルをアップロードします。

   ![[削除済みのユーザー] ページで一括復元コマンドを選択する](./media/users-bulk-restore/bulk-restore.png)

1. CSV ファイルの編集が完了したら、またはアップロードするご自身のファイルの準備が既にできている場合、 **[csv ファイルをアップロードします]** の下からそのファイルを選択します。

   ![追加するユーザーをリストするローカル CSV ファイルを選択する](./media/users-bulk-restore/upload-button.png)

1. ファイルの内容が検証されたら、ファイル情報を修正し、エラーがある場合はファイルを再送信します。 有効なファイルを送信すると、データ アップロード ジョブが自動的に開始されます。
1. CSV ファイルの検証に合格した後、 **[送信]** を選択して、ユーザーを復元する Azure Batch ジョブを開始します。 エラーが発生した場合は、[一括操作の結果] ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

## <a name="check-status"></a>状態の確認

**[一括操作の結果 (プレビュー)]** ページでは、保留中のすべての一括要求の状態を確認できます。

   ![[一括操作の結果] ページでアップロードの状態を確認する](./media/users-bulk-restore/bulk-center.png)

次に、復元したユーザーが Azure AD 組織に存在するかどうかを、Azure portal または PowerShell を使用して確認できます。

## <a name="view-restored-users-in-the-azure-portal"></a>Azure portal で復元されたユーザーを表示する

1. 組織のユーザー管理者アカウントで、[Azure AD 管理センターにサインイン](https://aad.portal.azure.com)します。
1. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[表示]** で **[すべてのユーザー]** を選択し、復元したユーザーがリストされていることを確認します。

### <a name="view-users-with-powershell"></a>PowerShell でユーザーを表示する

次のコマンドを実行します。

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

復元したユーザーがリストされていることがわかります。

## <a name="next-steps"></a>次の手順

- [ユーザーを一括インポートする](users-bulk-add.md)
- [ユーザーの一括削除](users-bulk-delete.md)
- [ユーザーの一覧のダウンロード](users-bulk-download.md)
