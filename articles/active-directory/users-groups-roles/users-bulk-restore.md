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
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174240"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Azure Active Directory で削除済みユーザーを一括復元する (プレビュー)

Azure Active Directory (Azure AD) では、ユーザーの一括作成および削除操作、ゲストの一括招待がサポートされています。また、ユーザー、グループ、グループ メンバーのリストのダウンロードがサポートされています。

## <a name="to-bulk-restore-users"></a>ユーザーを一括復元するには

1. Azure AD 組織のユーザー管理者アカウントで、[ご自身の Azure AD 組織にサインイン](https://aad.portal.azure.com)します。
1. Azure AD で、 **[ユーザー]**  >  **[削除済み]** の順に選択します。
1. **[削除済みのユーザー]** ページで、 **[一括復元]** を選択して、復元するユーザーのプロパティの有効な CSV ファイルをアップロードします。

   ![[削除済みのユーザー] ページで一括復元コマンドを選択する](./media/users-bulk-restore/bulk-restore.png)

1. この CSV ファイルを開いて、復元するユーザーごとに 1 行を追加します。 唯一の必須値は **ObjectID** です。 そのうえでファイルを保存します。

   ![追加するユーザーをリストするローカル CSV ファイルを選択する](./media/users-bulk-restore/upload-button.png)

1. **[ユーザーの一括復元 (プレビュー)]** ページの **[csv ファイルをアップロードします]** で、そのファイルを参照します。 ファイルを選択して **[送信]** をクリックすると、CSV ファイルの検証が開始されます。
1. ファイルの内容が検証されると、"**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
1. ファイルの検証に合格したら、 **[送信]** を選択して、ユーザーを復元する Azure 一括操作を開始します。
1. 復元操作が完了すると、一括操作が成功したという通知が表示されます。

エラーが発生した場合は、 **[一括操作の結果]** ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

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
