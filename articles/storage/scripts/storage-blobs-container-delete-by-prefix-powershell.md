---
title: Azure PowerShell のサンプル スクリプト - プレフィックスでコンテナーを削除する | Microsoft Docs
description: コンテナー名のプレフィックスに基づいて Azure Storage Blob コンテナーを削除します。
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: e0c41ed1fc2cdb7559d36a7056ebb7b9a6ecc186
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242772"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>コンテナー名のプレフィックスに基づいたコンテナーの削除

このスクリプトでは、コンテナー名のプレフィックスに基づいて Azure Blob ストレージ内のコンテナーを削除します。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、残りのコンテナー、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、コンテナー名のプレフィックスに基づいてコンテナーを削除します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | リソース グループまたはサブスクリプション内の指定された Storage アカウントまたはすべての Storage アカウントを取得します。 |
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | ストレージ アカウントに関連付けられているストレージ コンテナーを一覧表示します。 |
| [Remove-AzStorageContainer](/powershell/module/az.storage/Remove-AzStorageContainer) | 指定されたストレージ コンテナーを削除します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他のストレージ PowerShell サンプル スクリプトは、[Azure Blob ストレージ用 PowerShell サンプル](../blobs/storage-samples-blobs-powershell.md)のページにあります。
