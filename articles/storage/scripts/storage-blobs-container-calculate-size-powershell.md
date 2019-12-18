---
title: PowerShell を使用して BLOB コンテナーのサイズを計算する
titleSuffix: Azure Storage
description: コンテナー内の BLOB のサイズを合計して、Azure Blob ストレージ内のコンテナーのサイズを計算します。
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 890da96ac1d97a2eb84f5296c74a02824d657273
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895055"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>PowerShell を使用して BLOB コンテナーのサイズを計算する

このスクリプトでは、コンテナー内のブロブのサイズを合計して、Azure Blob ストレージ内のコンテナーのサイズを計算します。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> この PowerShell スクリプトは、コンテナーの推定サイズを算出しますが、課金の計算には使用できません。 課金目的のコンテナー サイズを計算するスクリプトについては、「[Calculate the size of a Blob storage container for billing purposes](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)」 (課金を目的に Blob ストレージ コンテナーのサイズを計算する) を参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、コンテナー、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用して、Blob ストレージ コンテナーのサイズを計算します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | リソース グループまたはサブスクリプション内の指定された Storage アカウントまたはすべての Storage アカウントを取得します。 |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | コンテナー内の BLOB を一覧表示します。 |

## <a name="next-steps"></a>次の手順

課金目的のコンテナー サイズを計算するスクリプトについては、「[Calculate the size of a Blob storage container for billing purposes](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)」 (課金を目的に Blob ストレージ コンテナーのサイズを計算する) を参照してください。

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他のストレージ PowerShell サンプル スクリプトは、[Azure Storage 用 PowerShell サンプル](../blobs/storage-samples-blobs-powershell.md)のページにあります。
