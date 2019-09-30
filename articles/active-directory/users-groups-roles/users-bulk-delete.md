---
title: Azure Active Directory ポータルでユーザーを一括削除する (プレビュー) | Microsoft Docs
description: Azure Active Directory の Azure 管理センターでユーザーを一括削除する
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
ms.openlocfilehash: 6c2204c572ca1f74f8060d0b6176df69359fe69a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901379"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Azure Active Directory でユーザーを一括削除する (プレビュー)

Azure Active Directory (Azure AD) では、ユーザーの一括作成および削除操作、ゲストの一括招待がサポートされています。また、ユーザー、グループ、グループ メンバーのリストのダウンロードがサポートされています。

## <a name="to-bulk-delete-users"></a>ユーザーを一括削除するには

1. 組織のユーザー管理者アカウントで、ご自身の Azure AD 組織にサインインします。
1. Azure AD で、 **[ユーザー]**  >  **[一括削除]** の順に選択します。
1. **[ユーザーの一括削除]** ページで **[ダウンロード]** を選択し、ユーザー プロパティの有効な CSV ファイルを受信し、削除するユーザーを追加します。
1. CSV ファイルの編集が完了したら、またはアップロードするご自身のファイルの準備が既にできている場合、 **[csv ファイルをアップロードします]** の下からそのファイルを選択します。

   ![削除するユーザーをリストするローカル CSV ファイルを選択する](./media/users-bulk-delete/bulk-delete.png)

1. ファイルの内容が検証されたら、ジョブを送信する前にエラーをすべて修正する必要があります。
1. ファイルの検証に合格したら、 **[送信]** を選択して、ユーザーを削除する Azure Batch ジョブを開始します。 エラーが発生した場合は、[一括操作の結果] ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

## <a name="check-status"></a>状態の確認

**[一括操作の結果 (プレビュー)]** ページでは、保留中のすべての一括要求の状態を確認できます。

   ![[一括操作の結果] ページでアップロードの状態を確認する](./media/users-bulk-delete/bulk-center.png)

次に、削除したユーザーが Azure AD 組織に存在するかどうかを、Azure portal または PowerShell を使用して確認できます。

## <a name="verify-deleted-users-in-the-azure-portal"></a>Azure portal で削除済みのユーザーを確認する

1. 組織のユーザー管理者アカウントで、Azure portal にサインインします。
1. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[表示]** で **[すべてのユーザー]** のみを選択し、削除したユーザーがリストされなくなったことを確認します。

### <a name="verify-deleted-users-with-powershell"></a>PowerShell で削除済みユーザーを確認する

次のコマンドを実行します。

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

削除したユーザーがリストされなくなったことを確認します。

## <a name="next-steps"></a>次の手順

- [ユーザーの一括追加](users-bulk-add.md)
- [ユーザーの一覧のダウンロード](users-bulk-download.md)
- [ユーザーの一括復元](users-bulk-restore.md)
