---
title: Azure Active Directory ポータルでのユーザー追加のための一括インポート (プレビュー) | Microsoft Docs
description: Azure Active Directory の Azure AD 管理センターでユーザーを一括追加します
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22052117448cdb71bbc16e1df7899e6d3eb764e
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901311"
---
# <a name="bulk-import-users-preview-in-azure-active-directory"></a>Azure Active Directory でのユーザーの一括インポート (プレビュー)

Azure Active Directory (Azure AD) では、ユーザーの一括作成および削除操作、ゲストの一括招待がサポートされています。また、ユーザー、グループ、グループ メンバーのリストのダウンロードがサポートされています。

## <a name="bulk-import-service-limits"></a>一括インポート サービスの制限

ユーザー作成の一括操作は、それぞれ最大 1 時間かかる場合があります。 これにより、最小で 5 万ユーザーを一括作成できます。

## <a name="required-permissions"></a>必要なアクセス許可

管理ポータルでユーザーを一括作成するには、グローバル管理者またはユーザー管理者としてサインインしている必要があります。

## <a name="to-bulk-import-users"></a>ユーザーを一括インポートするには

1. 組織のユーザー管理者アカウントで、[ご自身の Azure AD 組織にサインイン](https://aad.portal.azure.com)します。
1. Azure AD で、 **[ユーザー]**  > [一括作成] **の順に選択します。**
1. **[ユーザーの一括作成]** ページで **[ダウンロード]** を選択し、ユーザー プロパティの有効な CSV (コンマ区切りの値) ファイルを取得し、ご自分の新規ユーザーを追加します。
1. CSV ファイルの編集が完了したら、またはアップロードするご自身のファイルの準備が既にできている場合、 **[csv ファイルをアップロードします]** の下からそのファイルを選択します。

   ![追加するユーザーをリストするローカル CSV ファイルを選択する](./media/users-bulk-add/upload-button.png)

1. ファイルの内容を検証したら、アップロード作業を開始する前に、エラーをすべて修正する必要があります。
1. ご自身のファイルが検証されたら、 **[送信]** を選択して、新しいユーザー情報が追加される Azure の一括ジョブを開始します。 エラーが発生した場合は、[一括操作の結果] ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

## <a name="check-status"></a>状態の確認

**[一括操作の結果 (プレビュー)]** ページでは、保留中のすべての一括要求の状態を確認できます。

   ![[一括操作の結果] ページでアップロードの状態を確認する](./media/users-bulk-add/bulk-center.png)

次に、作成したユーザーが Azure AD 組織に存在するかどうかを、Azure portal または PowerShell を使用して確認します。

## <a name="verify-users-in-the-azure-portal"></a>Azure portal でユーザーを確認する

1. 組織のユーザー管理者アカウントで、[Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[表示]** で **[すべてのユーザー]** を選択し、作成したユーザーが一覧に表示されていることを確認します。

### <a name="verify-users-with-powershell"></a>PowerShell でユーザーを確認する

次のコマンドを実行します。

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

作成したユーザーがリストされているのを確認できます。

## <a name="next-steps"></a>次の手順

- [ユーザーの一括削除](users-bulk-delete.md)
- [ユーザーの一覧のダウンロード](users-bulk-download.md)
- [ユーザーの一括復元](users-bulk-restore.md)
