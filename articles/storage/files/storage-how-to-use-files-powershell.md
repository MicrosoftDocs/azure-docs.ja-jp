---
title: Azure PowerShell を使用して Azure ファイル共有を管理するためのクイック スタート
description: このクイック スタートを使用して、Azure PowerShell を使用して Azure ファイル共有を管理する方法を学習します。
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8b4bd9ece5f010f1294356ad4673543834e5076a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94626913"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>クイック スタート: Azure PowerShell を使用した Azure ファイル共有の作成および管理 
このガイドでは、PowerShell を使用して [Azure ファイル共有](storage-files-introduction.md)を操作する方法の基本について説明します。 Azure ファイル共有は他のファイル共有と似ていますが、クラウドに格納され、Azure プラットフォームによって支えられています。 Azure ファイル共有は、業界標準の サーバー メッセージ ブロック (SMB) プロトコル、ネットワーク ファイル システム (NFS) プロトコル (プレビュー) をサポートし、複数のマシン、アプリケーション、およびインスタンス間にわたってファイル共有を可能にします。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このガイドでは Azure PowerShell モジュール Az バージョン 0.7 以降が必要になります。 実行中の Azure PowerShell モジュールのバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合は、`Login-AzAccount` を実行して Azure アカウントにログインする必要もあります。

## <a name="create-a-resource-group"></a>リソース グループを作成する
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 まだ Azure リソース グループがない場合は、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して新しいものを作成できます。 

次の例では、*myResourceGroup* という名前のリソース グループを米国西部 2 リージョンに作成します。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
ストレージ アカウントは、Azure ファイル共有のデプロイに使用できるストレージの共有プールです。 ストレージ アカウントに含まれる共有の数と、共有に格納できるファイル数には制限がなく、ストレージ アカウントの容量の上限まで増やすことができます。 この例では、General Purpose バージョン 2 (GPv2 ストレージ アカウント) を作成します。このアカウントは、Standard Azure ファイル共有やその他のストレージ リソース (BLOB、キューなど) をハードディスク ドライブ (HDD) の回転式メディアに格納できます。 Azure Files は、Premium のソリッドステート ディスク ドライブ (SSD) もサポートしており、FileStorage ストレージ アカウントにプレミアムな Azure ファイル共有を作成できます。

この例では、[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドレットを使用してストレージ アカウントを作成します。 ストレージ アカウントの名前は *mystorageaccount\<random number>* で、そのストレージ アカウントへの参照は変数 **$storageAcct** に格納されます。 ストレージ アカウント名は一意である必要があります。そのため、`Get-Random` を使用し、名前に数を追加して一意にします。 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> 5 TiB (共有あたり最大 100 TiB) を超える共有が利用できるのは、ローカル冗長 (LRS) およびゾーン冗長 (ZRS) のストレージ アカウントだけです。 geo 冗長 (GRS) または geo ゾーン冗長 (GZRS) ストレージ アカウントを作成する場合は、`-EnableLargeFileShare` パラメーターを削除してください。

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する
これで、最初の Azure ファイル共有を作成できます。 ファイル共有は、[New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare) コマンドレットを使用して作成できます。 この例では、`myshare` という名前の共有を作成します。

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

共有名は、すべて小文字の英字、数字、単一ハイフンにする必要があります。ただし、最初にハイフンを使用することはできません。 ファイル共有とファイルの名前付けの詳細については、「 [共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)」を参照してください。

## <a name="use-your-azure-file-share"></a>Azure ファイル共有を使用する
Azure Files では、業界標準の [Server Message Block (SMB) プロトコル](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)と[ファイル REST プロトコル](/rest/api/storageservices/file-service-rest-api)の 2 つの方法で Azure ファイル共有内のファイルとフォルダーを操作できます。 

SMB を使用してファイル共有をマウントするには、お使いの OS に基づいて次のドキュメントを参照してください。
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>ファイル REST プロトコルで Azure ファイル共有を使用する 
ファイル REST プロトコルは使用する (つまり手動で REST HTTP 呼び出しを作成する) こともできますが、ファイル REST プロトコルを使用する最も一般的な方法は、Azure PowerShell モジュール、[Azure CLI](storage-how-to-use-files-cli.md)、または Azure Storage SDK を使用することです。いずれの方法でも、任意のスクリプトまたはプログラミング言語でファイル REST プロトコルの便利なラッパーが提供されます。  

ほとんどの場合、ユーザーは、SMB プロトコルを介して Azure ファイル共有を使用することが予想されます。その方が、使用できることを期待している既存のアプリケーションやツールを使用できるためです。しかし、SMB よりもファイル REST API を使用する方が有益な理由がいくつかあります。その理由を次に示します。

- (SMB 経由でファイル共有をマウントできない) PowerShell Cloud Shell からファイル共有を参照している。
- [Azure Functions](../../azure-functions/functions-overview.md) のようなサーバーレス リソースを利用している。 
- 多数の Azure ファイル共有を対話的に操作する付加価値サービスを作成している (バックアップやウイルス対策スキャンの実行など)。

次の例は、Azure PowerShell モジュールを使用してファイル REST プロトコルで Azure ファイル共有を操作する方法を示しています。 指定されたアクションをファイル共有に対して実行するためのストレージ アカウント キーは、`-Context` パラメーターを使用して取得します。 ストレージ アカウント キーを取得するには、ストレージ アカウントにおける `Owner` の Azure ロールが必要です。

#### <a name="create-directory"></a>ディレクトリの作成
Azure ファイル共有のルートで *myDirectory* という名前の新しいディレクトリを作成するには、[New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) コマンドレットを使用します。

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>ファイルをアップロードする
[Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) コマンドレットを使用してファイルをアップロードする方法を示すには、最初に PowerShell Cloud Shell のスクラッチ ドライブ内に、アップロードするファイルを作成する必要があります。 

この例では、スクラッチ ドライブ上の新しいファイルに現在の日付と時間を挿入してから、そのファイルをファイル共有にアップロードします。

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

PowerShell をローカルで実行している場合、`~/CloudDrive/` を実際のマシンに存在するパスに置き換える必要があります。

ファイルのアップロード後、[Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) コマンドレットを使用して、ファイルが Azure ファイル共有にアップロードされたことを確認できます。 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>ファイルをダウンロードする
[Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) コマンドレットを使用して、アップロードしたばかりのファイルのコピーを Cloud Shell のスクラッチ ドライブにダウンロードできます。

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

ファイルのダウンロード後、`Get-ChildItem` を使用して、ファイルが PowerShell Cloud Shell のスクラッチ ドライブにダウンロードされたことを確認できます。

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>ファイルのコピー
よくあるタスクの 1 つとして、ファイル共有間でのファイルのコピーがあります。 この機能を実行するために、新しい共有を作成し、[Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) コマンドレットを使用して先ほどアップロードしたファイルをその新しい共有にコピーできます。 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

次に、新しい共有のファイルを一覧表示すると、コピーしたファイルが表示されます。

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

`Start-AzStorageFileCopy` コマンドレットは、Azure ファイル共有間でのアドホック ファイル移動には便利ですが、大きなデータの移動や移行には、`robocopy` (Windows) や `rsync` (macOS および Linux) をお勧めします。 `robocopy` と `rsync` では、FileREST API ではなく SMB を使用して、データ移動が実行されます。

## <a name="create-and-manage-share-snapshots"></a>共有スナップショットの作成と管理
さらに、Azure ファイル共有で実行できる便利なタスクの 1 つとして、共有スナップショットの作成があります。 スナップショットでは、特定の時点の Azure ファイル共有が保存されます。 共有スナップショットは、場合によっては既に使い慣れている、次のようなオペレーティング システム テクノロジに類似しています。

- NTFS や ReFS などの Windows ファイル システム用の[ボリューム シャドウ コピー サービス (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal)。
- Linux システム用の[論理ボリューム マネージャー (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) スナップショット。
- macOS 用の [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) スナップショット。 

共有の共有スナップショットは、ファイル共有の PowerShell オブジェクトに `Snapshot` メソッドを使用して作成できます。ファイル共有は、[Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) コマンドレットを使用して取得します。 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.CloudFileShare.Snapshot()
```

### <a name="browse-share-snapshots"></a>共有スナップショットの参照
スナップショットの参照 (`$snapshot`) を `Get-AzStorageFile` コマンドレットの `-Share` パラメーターに渡すことで、共有スナップショットの内容を参照できます。

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>共有スナップショットの一覧表示
次のコマンドで、共有に関して取得したスナップショットの一覧を表示できます。

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>共有スナップショットからの復元
先ほど使った `Start-AzStorageFileCopy` コマンドを使用して、ファイルを復元できます。 このクイック スタートでは、先ほどアップロードした `SampleUpload.txt` ファイルを最初に削除します。そうすることで、スナップショットからそれを復元できます。

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>共有スナップショットの削除
`$snapshot` 参照を `-Share` パラメーターに含めた変数で [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) コマンドレットを使用すると、共有スナップショットを削除できます。

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする
完了したら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドレットを使用して、リソース グループとすべての関連リソースを削除できます。 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

また、リソースを 1 つずつ削除することもできます。

- このクイック スタートで作成した Azure ファイル共有を削除する場合。

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > 作成した Azure ファイル共有の共有スナップショットは、Azure ファイル共有を削除する前にすべて削除してください。

- ストレージ アカウント自体を削除する場合 (この場合、作成した Azure ファイル共有が暗黙的に削除されます。また、Azure Blob Storage コンテナーなど、作成されたその他のストレージ リソースも暗黙的に削除されます)。

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure Files とは何ですか。](storage-files-introduction.md)