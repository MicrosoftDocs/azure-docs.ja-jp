---
title: PowerShell を使用してストレージ アカウントのアクセス キーをローテーションする
titleSuffix: Azure Storage
description: Azure Storage アカウントを作成し、そのアカウント アクセス キーの 1 つを取得してローテーションします。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7d8585b5d05012ab2aff2580d41fecf6423b509c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89070430"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>PowerShell を使用してストレージ アカウントのアクセス キーをローテーションする

このスクリプトは、Azure Storage アカウントを作成し、新しいストレージ アカウントのプライマリ アクセス キーを表示した後、キーの更新 (ローテーション) を行います。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループ、ストレージ アカウント、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使って、ストレージ アカウントを作成し、そのアクセス キーの 1 つを取得してローテーションを行います。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | すべての場所と、各場所でサポートされているリソース プロバイダーを取得します。 |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Azure リソース グループを作成します。 |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | ストレージ アカウントを作成します。 |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Azure ストレージ アカウントのアクセス キーを取得します。 |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Azure Storage アカウントのアクセス キーを再生成します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他のストレージ PowerShell サンプル スクリプトは、[Azure Blob Storage 用 PowerShell サンプル](../blobs/storage-samples-blobs-powershell.md)のページにあります。
