---
title: Azure Active Directory ポータルでユーザーを一括作成する (プレビュー) | Microsoft Docs
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
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174307"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Azure Active Directory でユーザーを一括作成する (プレビュー)

Azure Active Directory (Azure AD) では、ユーザーの一括作成および削除操作、ゲストの一括招待がサポートされています。また、ユーザー、グループ、グループ メンバーのリストのダウンロードがサポートされています。

## <a name="required-permissions"></a>必要なアクセス許可

管理ポータルでユーザーを一括作成するには、グローバル管理者またはユーザー管理者としてサインインしている必要があります。

## <a name="to-create-users-in-bulk"></a>ユーザーを一括で作成する手順

1. 組織のユーザー管理者アカウントで、[ご自身の Azure AD 組織にサインイン](https://aad.portal.azure.com)します。
1. Azure AD で、 **[ユーザー]**  > [一括作成] **の順に選択します。**
1. **[ユーザーの一括作成]** ページで **[ダウンロード]** を選択し、ユーザー プロパティの有効な CSV (コンマ区切り値) ファイルを取得し、作成するユーザーを追加します。

   ![追加するユーザーをリストするローカル CSV ファイルを選択する](./media/users-bulk-add/upload-button.png)

1. CSV ファイルを開いて、作成するユーザーごとに 1 行を追加します。 必須値は、 **[名前]** 、 **[ユーザー プリンシパル名]** 、 **[初期パスワード]** 、および **[サインインのブロック (はい/いいえ)]** のみです。 そのうえでファイルを保存します。

   ![CSV ファイルには、作成するユーザーの名前と ID が含まれています。](./media/users-bulk-add/add-csv-file.png)

1. **[ユーザーの一括作成 (プレビュー)]** ページの [CSV ファイルをアップロード] で、そのファイルを参照します。 ファイルを選択して **[送信]** をクリックすると、CSV ファイルの検証が開始されます。
1. ファイルの内容が検証された後、"**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
1. ファイルが検証に合格したら、 **[送信]** を選択して、新しいユーザーをインポートする Azure の一括操作を開始します。
1. インポート操作が完了すると、一括操作ジョブの状況に関する通知が表示されます。

エラーがある場合は、 **[一括操作の結果]** ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

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

## <a name="bulk-import-service-limits"></a>一括インポート サービスの制限

ユーザー作成の一括操作は、それぞれ最大 1 時間かかる場合があります。 これにより、最小で 5 万ユーザーを一括作成できます。

## <a name="next-steps"></a>次の手順

- [ユーザーの一括削除](users-bulk-delete.md)
- [ユーザーの一覧のダウンロード](users-bulk-download.md)
- [ユーザーの一括復元](users-bulk-restore.md)
