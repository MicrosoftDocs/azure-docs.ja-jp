---
title: Azure PowerShell を使用して Azure ファイル共有を管理するためのクイック スタート
description: このクイック スタートを使用して、Azure PowerShell を使用して Azure ファイル共有を管理する方法を学習します。
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: f353c9952d9ccec0f279d6bd98bf5db215758559
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524546"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>クイック スタート: Azure PowerShell を使用した Azure ファイル共有の作成および管理 
このガイドでは、PowerShell を使用して [Azure ファイル共有](storage-files-introduction.md)を操作する方法の基本について説明します。 Azure ファイル共有は他のファイル共有と似ていますが、クラウドに格納され、Azure プラットフォームによって支えられています。 Azure ファイル共有は、業界標準の SMB プロトコルをサポートし、複数のマシン、アプリケーション、およびインスタンス間にわたってファイル共有を可能にします。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このガイドでは Azure PowerShell モジュール Az バージョン 0.7 以降が必要になります。 実行中の Azure PowerShell モジュールのバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合は、`Login-AzAccount` を実行して Azure アカウントにログインする必要もあります。

## <a name="create-a-resource-group"></a>リソース グループの作成
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 まだ Azure リソース グループがない場合は、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して新しいものを作成できます。 

次の例では、*myResourceGroup* という名前のリソース グループを米国東部リージョンに作成します。

```azurepowershell-interactive
New-AzResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
ストレージ アカウントは、Azure ファイル共有またはその他のストレージ リソース (BLOB やキューなど) のデプロイに使用できるストレージの共有プールです。 ストレージ アカウントに含まれる共有の数と、共有に格納できるファイル数には制限がなく、ストレージ アカウントの容量の上限まで増やすことができます。

この例では、[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドレットを使用してストレージ アカウントを作成します。 ストレージ アカウントの名前は *mystorageaccount\<random number>* で、そのストレージ アカウントへの参照は変数 **$storageAcct** に格納されます。 ストレージ アカウント名は一意である必要があります。そのため、`Get-Random` を使用し、名前に数を追加して一意にします。 

```azurepowershell-interactive 
$storageAcct = New-AzStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する
これで、最初の Azure ファイル共有を作成できます。 ファイル共有は、[New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) コマンドレットを使用して作成できます。 この例では、`myshare` という名前の共有を作成します。

```azurepowershell-interactive
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

共有名は、すべて小文字の英字、数字、単一ハイフンにする必要があります。ただし、最初にハイフンを使用することはできません。 ファイル共有とファイルの名前付けの詳細については、「 [共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)」を参照してください。

## <a name="use-your-azure-file-share"></a>Azure ファイル共有を使用する
Azure Files では、業界標準の [Server Message Block (SMB) プロトコル](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)と[ファイル REST プロトコル](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api)の 2 つの方法で Azure ファイル共有内のファイルとフォルダーを操作できます。 

SMB を使用してファイル共有をマウントするには、お使いの OS に基づいて次のドキュメントを参照してください。
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>ファイル REST プロトコルで Azure ファイル共有を使用する 
ファイル REST プロトコルは直接使用する (つまり手動で REST HTTP 呼び出しを作成する) こともできますが、ファイル REST プロトコルを使用する最も一般的な方法は、Azure PowerShell モジュール、[Azure CLI](storage-how-to-use-files-cli.md)、または Azure Storage SDK を使用することです。いずれの方法でも、任意のスクリプト/プログラミング言語でファイル REST プロトコルの便利なラッパーが提供されます。  

ほとんどの場合、ユーザーは、SMB プロトコルを介して Azure ファイル共有を使用することが予想されます。その方が、使用できることを期待している既存のアプリケーションやツールを使用できるためです。しかし、SMB よりもファイル REST API を使用する方が有益な理由がいくつかあります。その理由を次に示します。

- (SMB 経由でファイル共有をマウントできない) PowerShell Cloud Shell からファイル共有を参照している。
- ポート 445 がブロックされていないオンプレミスのクライアントなど、SMB 共有をマウントできないクライアントからスクリプトまたはアプリケーションを実行する必要がある。
- [Azure Functions](../../azure-functions/functions-overview.md) のようなサーバーレス リソースを利用している。 

次の例は、Azure PowerShell モジュールを使用してファイル REST プロトコルで Azure ファイル共有を操作する方法を示しています。 

#### <a name="create-directory"></a>ディレクトリを作成する
Azure ファイル共有のルートで *myDirectory* という名前の新しいディレクトリを作成するには、[New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) コマンドレットを使用します。

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>ファイルをアップロードする
[Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) コマンドレットを使用してファイルをアップロードする方法を示すには、最初に PowerShell Cloud Shell のスクラッチ ドライブ内に、アップロードするファイルを作成する必要があります。 

この例では、スクラッチ ドライブ上の新しいファイルに現在の日付と時間を挿入してから、そのファイルをファイル共有にアップロードします。

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

PowerShell をローカルで実行している場合、`C:\Users\ContainerAdministrator\CloudDrive\` を実際のマシンに存在するパスに置き換える必要があります。

ファイルのアップロード後、[Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) コマンドレットを使用して、ファイルが Azure ファイル共有にアップロードされたことを確認できます。 

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>ファイルをダウンロードする
[Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) コマンドレットを使用して、アップロードしたばかりのファイルのコピーを Cloud Shell のスクラッチ ドライブにダウンロードできます。

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
     -Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

ファイルのダウンロード後、`Get-ChildItem` を使用して、ファイルが PowerShell Cloud Shell のスクラッチ ドライブにダウンロードされたことを確認できます。

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

#### <a name="copy-files"></a>ファイルのコピー
よくあるタスクの 1 つとして、1 つのファイル共有から別のファイル共有へのファイルのコピー、または Azure BLOB ストレージ コンテナーとの間でのコピーがあります。 この機能を実行するために、新しい共有を作成し、[Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) コマンドレットを使用して先ほどアップロードしたファイルをその新しい共有にコピーできます。 

```azurepowershell-interactive
New-AzStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

次に、新しい共有のファイルを一覧表示すると、コピーしたファイルが表示されます。

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

`Start-AzStorageFileCopy` コマンドレットは、Azure ファイル共有と Azure BLOB ストレージ コンテナーの間でのアドホック ファイル移動に便利です。ただし、(移動するファイルの数やサイズの点で) 移動の規模が大きい場合は AzCopy の使用をお勧めします。 [Windows 用の AzCopy](../common/storage-use-azcopy.md) と [Linux 用の AzCopy](../common/storage-use-azcopy-linux.md) についてご確認ください。 AzCopy はローカルにインストールする必要があります。Cloud Shell では使用できません。 

## <a name="create-and-manage-share-snapshots"></a>共有スナップショットの作成と管理
さらに、Azure ファイル共有で実行できる便利なタスクの 1 つとして、共有スナップショットの作成があります。 スナップショットでは、特定の時点の Azure ファイル共有が保存されます。 共有スナップショットは、場合によっては既に使い慣れている、次のようなオペレーティング システム テクノロジに類似しています。
- NTFS や ReFS などの Windows ファイル システム用の[ボリューム シャドウ コピー サービス (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal)
- Linux システム用の[論理ボリューム マネージャー (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) スナップショット
- macOS 用の [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) スナップショット。 
 共有の共有スナップショットは、ファイル共有の PowerShell オブジェクトに `Snapshot` メソッドを使用して作成できます。ファイル共有は、[Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) コマンドレットを使用して取得します。 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>共有スナップショットの参照
スナップショットの参照 (`$snapshot`) を `Get-AzStorageFile` コマンドレットの `-Share` パラメーターに渡すことで、共有スナップショットの内容を参照できます。

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>共有スナップショットの一覧表示
次のコマンドで、共有に関して取得したスナップショットの一覧を表示できます。

```azurepowershell-interactive
Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>共有スナップショットからの復元
先ほど使った `Start-AzStorageFileCopy` コマンドを使用して、ファイルを復元できます。 このクイック スタートでは、先ほどアップロードした `SampleUpload.txt` ファイルを最初に削除します。そうすることで、スナップショットからそれを復元できます。

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"
 # Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>共有スナップショットの削除
`$snapshot` 参照を `-Share` パラメーターに含めた変数で [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) コマンドレットを使用すると、共有スナップショットを削除できます。

```azurepowershell-interactive
Remove-AzStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
完了したら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドレットを使用して、リソース グループとすべての関連リソースを削除できます。 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

また、リソースを 1 つずつ削除することもできます。

- このクイック スタートで作成した Azure ファイル共有を削除する場合。

    ```azurepowershell-interactive
    Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
        Remove-AzStorageShare -Context $storageAcct.Context -Name $_.Name
    }
    ```

- ストレージ アカウント自体を削除する場合 (この場合、作成した Azure ファイル共有が暗黙的に削除されます。また、Azure BLOB ストレージ コンテナーなど、作成されたその他のストレージ リソースも暗黙的に削除されます)。

    ```azurepowershell-interactive
    Remove-AzStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Files とは何ですか。](storage-files-introduction.md)
