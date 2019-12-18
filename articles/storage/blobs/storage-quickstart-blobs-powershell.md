---
title: クイックスタート - PowerShell を使用して BLOB を作成する
titleSuffix: Azure Storage
description: このクイック スタートでは、オブジェクト (BLOB) ストレージで Azure PowerShell を使用します。 その後、PowerShell を使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c5d32b91043d310e51143357ad51631463f7e991
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892500"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>クイック スタート:PowerShell を使用して BLOB をアップロード、ダウンロード、および一覧表示する

Azure PowerShell モジュールを使用して Azure リソースを作成および管理します。 Azure リソースの作成または管理は、PowerShell のコマンド ラインまたはスクリプトから行うことができます。 このガイドでは、PowerShell を使用してローカル ディスクと Azure Blob Storage の間でファイルを転送する方法について説明します。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>前提条件

Azure Storage にアクセスするには、Azure サブスクリプションが必要です。 サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

このクイック スタートでは、Azure PowerShell モジュール Az バージョン 0.7 以降が必要です。 バージョンを確認するには、`Get-InstalledModule -Name Az -AllVersions | select Name,Version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>コンテナーを作成する

BLOB は常にコンテナーにアップロードされます。 コンピューター上のファイルをフォルダーで整理するように、BLOB のグループを整理できます。

コンテナー名を設定し、[New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) を使用してコンテナーを作成します。 `blob` に対するアクセス許可を設定して、ファイルのパブリック アクセスを許可します。 この例でのコンテナー名は *quickstartblobs* です。

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 IaaS VM をバックアップするための VHD ファイルはページ BLOB です。 追加 BLOB は、ファイルに書き込んでからも情報を追加し続ける場合などの、ログ記録に使用します。 BLOB ストレージに格納されているほとんどのファイルはブロック BLOB です。 

ファイルをブロック BLOB にアップロードするには、コンテナー参照を取得してから、そのコンテナー内のブロック BLOB への参照を取得します。 BLOB 参照を取得したら、[Set-AzStorageBlobContent](/powershell/module/az.storage/set-AzStorageblobcontent) を使用して、それにデータをアップロードできます。 この処理により、BLOB が存在しない場合は作成され、存在する場合は上書きされます。

次の例では、ローカル ディスク上の *D:\\_TestImages* フォルダーの *Image001.jpg* と *Image002.png* を作成したコンテナーにアップロードします。

```powershell
# upload a file
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

続行する前に、希望する数のファイルをアップロードします。

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

[Get-AzStorageBlob](/powershell/module/az.storage/get-AzStorageblob) を使用して、コンテナー内の BLOB の一覧を取得します。 この例では、アップロードされた BLOB の名前だけを示しています。

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>BLOB をダウンロードする

BLOB をローカル ディスクにダウンロードします。 ダウンロードする BLOB ごとに名前を設定し、[Get-AzStorageBlobContent](/powershell/module/az.storage/get-AzStorageblobcontent) を呼び出して BLOB をダウンロードします。

この例では、BLOB をローカル ディスク上の *D:\\_TestImages\Downloads* にダウンロードします。 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>AzCopy でのデータ転送

[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ユーティリティは、Azure Storage 用のスクリプト可能な高性能データ転送のためのもう 1 つのオプションです。 AzCopy を使用して、Blob Storage、File Storage、および Table Storage との間でデータを転送します。

簡単な例として、PowerShell ウィンドウ内から、*myfile.txt* という名前のファイルを *mystoragecontainer* コンテナーにアップロードするための AzCopy コマンドを次に示します。

```powershell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成したすべての資産を削除します。 アセットを削除する最も簡単な方法は、リソース グループを削除することです。 リソース グループを削除すると、そのグループに含まれるすべてのリソースも削除されます。 次の例では、リソース グループを削除して、ストレージ アカウントとリソース グループ自体を削除しています。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、ローカル ディスクと Azure Blob Storage の間でファイルを転送しました。 PowerShell を使用した BLOB ストレージの操作については、Azure Storage で Azure PowerShell を使用する方法に関するページに進みます。

> [!div class="nextstepaction"]
> [Azure Storage での Azure PowerShell の使用](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Microsoft Azure PowerShell Storage コマンドレット リファレンス

* [Storage PowerShell コマンドレット](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure ストレージ エクスプローラー

* [Microsoft Azure ストレージ エクスプローラー](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
