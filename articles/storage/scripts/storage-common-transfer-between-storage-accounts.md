---
title: Azure PowerShell スクリプト サンプル - Windows での AzCopy を使用したストレージ アカウント間での BLOB の移行 | Microsoft Docs
description: AzCopy を使用して、Azure Storage アカウントの BLOB の内容を別のアカウントにコピーします。
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: rogarana
ms.openlocfilehash: 88eb97a206bc75bc9635da90eb9d48818ceef0f0
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487134"
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Windows での AzCopy を使用したストレージ アカウント間での BLOB の移行

このサンプルでは、すべての BLOB オブジェクトを、ユーザー指定のソース ストレージ アカウントからユーザー指定のターゲット ストレージ アカウントにコピーします。 

これは、ストレージ アカウント内のすべてのコンテナーを一覧表示する `Get-AzStorageContainer` コマンドを使用して実現します。 サンプルは、AzCopy コマンドを発行して、ソース ストレージ アカウントからターゲット ストレージ アカウントに各コンテナーをコピーします。 エラーが発生した場合、サンプルは、再試行を $retryTimes 回実行します ($retryTimes の既定値は 3 であり、`-RetryTimes` パラメーターで変更できます)。 再試行でエラーが発生した場合は、正常にコピーされた最後のコンテナーを `-LastSuccessContainerName` パラメーターを使用してサンプルに指定することで、スクリプトを再実行することができます。 サンプルは、コンテナーのコピーをその時点から続行します。

このサンプルでは、Azure PowerShell Storage モジュール バージョン **0.7** 以降が必要です。 `Get-Module -ListAvailable Az.storage` を使用して、インストールされているバージョンをチェックできます。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

サンプルでは、[AzCopy on Windows](https://aka.ms/downloadazcopy) の最新バージョンも必要です。 既定のインストール ディレクトリは `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\` です。

このサンプルは、ソース ストレージ アカウント名とキー、ターゲット ストレージ アカウント名とキー、および AzCopy.exe の完全ファイルパス (既定のディレクトリにインストールされていない場合) を受け取ります。

このサンプルの入力の例を次に示します。

AzCopy が既定のディレクトリにインストールされている場合:
```powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

AzCopy が既定のディレクトリにインストールされていない場合:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

エラーが発生し、サンプルを特定のコンテナーから再実行する必要がある場合: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用して、データを 1 つのストレージ アカウントから別のストレージ アカウントにコピーします。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | このストレージ アカウントに関連付けられているストレージ コンテナーが返ります。 |
| [New-AzStorageContext](/powershell/module/az.storage/New-AzStorageContext) | Azure Storage コンテキストを作成します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他のストレージ PowerShell サンプル スクリプトは、[Azure Blob ストレージ用 PowerShell サンプル](../blobs/storage-samples-blobs-powershell.md)のページにあります。
