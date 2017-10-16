---
title: "PowerShell を使用する Azure Blob Storage (オブジェクトの記憶域) での操作の実行 | Microsoft Docs"
description: "チュートリアル - PowerShell を使用する Azure Blob Storage (オブジェクトの記憶域) での操作の実行"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 565bcba848de1c518b25ff4c55a9a47aaa45bfb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>Azure PowerShell を使用して Azure Blob Storage の操作を実行する

Azure Blob Storage は、HTTP または HTTPS 経由で世界中のどこからでもアクセスできるテキストやバイナリ データなど、大量の非構造化オブジェクト データを格納するためのサービスです。 この記事では、BLOB のアップロード、ダウンロード、および削除など、Azure Blob Storage での基本的な操作について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * コンテナーを作成する 
> * BLOB をアップロードする
> * コンテナー内の BLOB を一覧表示する 
> * BLOB をダウンロードする
> * BLOB をコピーする
> * BLOB を削除する
> * BLOB のメタデータとプロパティを表示および設定する
> * Shared Access Signature を使用してセキュリティを管理する

このチュートリアルには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>コンテナーを作成する

BLOB は常にコンテナーにアップロードされます。 コンテナーはコンピューター上のディレクトリに似ており、コンピューター上のフォルダーでファイルを整理するように、コンテナーで BLOB のグループを整理することができます。 ストレージ アカウントには任意の数のコンテナーを含めることができます。ストレージ アカウントでの占有量によってのみ制限されます (最大 500 TB)。 

コンテナーを作成するときに、アクセス レベルを設定できます。これは、そのコンテナー内の BLOB にアクセスできるユーザーを定義するのに役立ちます。 たとえば、プライベート (アクセス レベル = `Off`) に設定できます。これは、Shared Access Signature、またはストレージ アカウントのアクセス キーがないと誰もアクセスできないことを意味します。 コンテナーを作成するときにアクセス レベルを指定しない場合は、既定でプライベートに設定されます。

コンテナー内のイメージにパブリックにアクセスできるようにすることができます。 たとえば、Web サイトに表示するイメージを格納する場合は、パブリックにする必要があります。 この場合、コンテナーのアクセス レベルを `blob` に設定します。そうすれば、そのコンテナーの BLOB を指す URL を使用して、誰でも BLOB にアクセスできます。

コンテナーを作成するには、コンテナー名を設定してから、アクセス許可を 'blob' に設定してコンテナーを作成します。 コンテナー名は英文字または数字で始まる必要があり、英文字、数字、およびハイフン文字 (-) のみを含めることができます。 BLOB とコンテナーの名前付けの詳細については、「[コンテナー、BLOB、メタデータの名前付けと参照](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

[New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer) を使用して、新しいコンテナーを作成します。 アクセス レベルはパブリックに設定します。 この例のコンテナー名は *howtoblobs* です。

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>コンテナーに BLOB をアップロードする

Azure Blob Storage では、ブロック BLOB、追加 BLOB、およびページ BLOB がサポートされています。  IaaS VM のバックアップに使用される VHD ファイルはページ BLOB です。 追加 BLOB は、ファイルに書き込んでから詳細情報を追加し続ける場合などの、ログ記録に使用されます。 BLOB ストレージに格納されているほとんどのファイルはブロック BLOB です。 

ファイルをブロック BLOB にアップロードするには、コンテナー参照を取得してから、そのコンテナー内のブロック BLOB への参照を取得します。 BLOB の参照を取得したら、[Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent) を使用して、それにデータをアップロードできます。 この操作により、BLOB が存在しない場合は作成され、既に存在する場合は上書きされます。

BLOB をコンテナーにアップロードする方法を以下に示します。 まず、ファイルが配置されるローカル コンピューター上のディレクトリを指す変数を設定し、アップロードするファイルの名前に対して変数を設定します。 これは、同じ操作を繰り返し実行する場合に便利です。 コンテナー内の BLOB を一覧表示する場合に複数のエントリを表示できるように、いくつかのファイルをアップロードします。

次の例では、Image001.jpg と Image002.png をローカル ディスクの D:\\_TestImages から、作成したコンテナーに読み込みます。

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

別のファイルをアップロードします。 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

続行する前に、希望する数のファイルをアップロードします。

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

[Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) を使用し、表示する BLOB 名を選択して、コンテナー内の BLOB の一覧を取得します。

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>BLOB をダウンロードする

BLOB をローカル ディスクにダウンロードします。 まず、BLOB のダウンロード先のローカル フォルダーを指す変数を設定します。 次に、ダウンロードする BLOB ごとに、名前を設定し、[Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) を呼び出して BLOB をダウンロードします。

この例では、BLOB をローカル ディスクの D:\\_TestImages\Downloads にコピーします。 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>BLOB をコピーする

BLOB をコピーする際に考慮すべき点がいくつかあります。 単に同じ場所の新しい名前に BLOB をコピーすることができます。 別のストレージ アカウントに BLOB をコピーすることができます。 異なるストレージ アカウントに (VHD ファイルなど) 大きな BLOB をコピーすることができます。 これらはそれぞれ異なる方法で行われます。

### <a name="simple-blob-copy"></a>単純な BLOB のコピー

新しい BLOB にコピーすることで、コンテナーの BLOB のいずれかをコピーできます。 この例では同じコンテナーに別の名前でコピーしますが、同様に別のコンテナーを簡単に作成して、代わりにそこにコピーすることもできます。 この例では、[Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) を使用して、BLOB をコピーする方法を示します。 ソースとコピー先の BLOB 名とコンテナー名の両方を指定する必要があります。

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>異なるストレージ アカウントに BLOB をコピーする 

別のストレージ アカウントに BLOB をコピーすることができます。 これを行う例の 1 つは、VM のいずれかを異なるストレージ アカウントにバックアップする VHD ファイルをコピーしてバックアップを行うことです。 

2 番目のストレージ アカウントを設定し、コンテキストを取得して、そのストレージ アカウントにコンテナーを設定してからコピーを実行します。 スクリプトのこの部分は、最初のストレージ アカウントではなく 2 番目のストレージ アカウントを使用することを除き、前述のスクリプトとほとんど同じです。

```powershell
#create new storage acount, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>非常に大きな BLOB を非同期的にコピーする

数 GB の非常に大きな BLOB をコピーする場合は、非同期コピーを利用できます。その場合、コピーを開始してから作業に戻り、終了するまで状態を確認します。 このようにすれば、コピー操作の実行中に手が離せなくなることはありません。

ストレージ アカウント内にコピーする場合、コピーは短時間で済みます。 同じ領域内の 2 つのストレージ アカウント間でコピーする場合は、さらに時間を短縮できます。 ただし、ソースとコピー先が別の領域にある場合は、その距離とファイルのサイズに応じて、コピーが完了するまで数時間かかる可能性があります。 

このスクリプトでは、最後のコピー例で定義したものと同じ変数を使用します。 違いは、コピーの状態をキャプチャし、完了するまで 10 秒ごとに繰り返しクエリすることです。 完了するまで複数回繰り返されることを確認するために、ストレージ アカウントに非常に大きな BLOB をアップロードする必要がある場合があります。

コピーの状態をクエリするには、[Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate) を使用します。 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>BLOB を削除する

ストレージ アカウントから BLOB を削除するには、[Remove-AzureStorageBlob](/powershell/module/azure.storage/Remove-AzureStorageBlob) を使用します。 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>BLOB のプロパティとメタデータの読み取りと書き込みを行う

返される **IListBlobItem** のプロパティとメソッドのセットにアクセスするには、**CloudBlockBlob**、**CloudPageBlob**、**CloudBlobDirectory** のいずれかのオブジェクトにキャスト (または変換) する必要があります。 型がわからない場合は、型チェックを使うとどれにキャストすればよいかがわかります。 次のコードは、[Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) を使用して、CloudBlockBlob オブジェクトに結果をキャストし、アップロードした BLOB のいずれかのプロパティを取得して出力する方法を示しています。

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

FetchAttributes を呼び出してシステム プロパティを設定してから、それらのプロパティを表示します。 一部のプロパティは書き込み可能であるため、その値を変更できます。 この例では、MIME の種類とも呼ばれる、コンテンツの種類を変更する方法を示します。

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

各 BLOB には、設定できる独自のメタデータがあります。 たとえば、BLOB をアップロードしたユーザーの名前や、最初にアップロードされた日付/時刻を格納できます。 メタデータはキー/値ペアで構成されます。 これを、次のように PowerShell を使用して変更することができます。

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>BLOB のセキュリティの管理 

既定では、Azure Storage はアカウント所有者へのアクセスを制限することによってデータのセキュリティを維持します。アカウントの所有者は、ストレージ アカウント アクセス キーを所持している人です。 ストレージ アカウント内の BLOB データを共有する必要がある場合は、アカウント アクセス キーのセキュリティを損なわずに共有することが重要です。 そのために、Shared Access Signature URL を使用することができます。これは、一定の時間に特定のレベルのアクセスを許可するセキュリティ トークンとクエリ パラメーターを含む、資産への URL です。 たとえば、他のユーザーが表示できるように、5 分間のプライベート BLOB への読み取り権限を付与することができます。 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>コンテナーとその BLOB のアクセス レベルをプライベートに設定する

まず、コンテナーのアクセス レベルを `Off` に設定します。これは、Shared Access Signature またはアカウント キーがないとアクセスできないことを示します。 [Set-AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl) を使用します。

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>プライベート アクセスをテストする

コンテナー内の BLOB にアクセスできないことを確認するには、Shared Access Signature なしで BLOB のいずれかへの URL を作成し、BLOB を表示してみます。 HTTPS プロトコルを使用する場合、URL は次の形式になります。

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

これは、BLOB URL の作成方法を示しています。

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

BLOB URL をコピーして、プライベート ブラウザー ウィンドウに貼り付けます。この場合、BLOB はプライベートで、Shared Access Signature がないため、承認エラーが表示されます。 

### <a name="create-the-sas-uri"></a>SAS URI を作成する

SAS URI を作成します。この URI は、SAS を構成するクエリ パラメーターとセキュリティ トークンを含む、BLOB へのリンクです。 この URI をプライベート ブラウザー ウィンドウに貼り付けます。ここには画像が表示されます。 

まず、アクセスの開始日付/時刻と有効期限の日付/時刻を作成します。 ここでは 2 分に設定します。 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

[New-AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken) を使用して、SAS URI を作成します。 コンテナー名、BLOB 名、ストレージ アカウント コンテキスト、付与されるアクセス許可 (この場合、読み取り、書き込み、および削除)、およびアクセスの開始と終了時刻が必要です。 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

SAS URI をコピーして、プライベート ブラウザー ウィンドウに貼り付けます。ここには画像が表示されます。

URL の期限が切れるまで (この例では 2 分間) 十分待ってから、URL を別の InPrivate ブラウザー ウィンドウに貼り付けます。 この時点で、承認エラーが発生し、画像は表示されません。これは、SAS URI の期限が切れているためです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成した資産をすべて削除します。 これを行う場合、リソース グループを削除できます。このときに、グループ内に含まれるすべてのリソースも削除されます。 この場合、すべてのストレージ アカウントと、リソース グループ自体が削除されます。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の実行方法など、基本的な BLOB ストレージ管理について学習しました。

> [!div class="checklist"]
> * コンテナーを作成する 
> * BLOB をアップロードする
> * コンテナー内の BLOB を一覧表示する 
> * BLOB をダウンロードする
> * BLOB をコピーする
> * BLOB を削除する
> * BLOB のメタデータとプロパティの読み取りと書き込みを行う
> * Shared Access Signature を使用してセキュリティを管理する

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage コマンドレット
* [Storage PowerShell コマンドレット](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure ストレージ エクスプローラー
* [Microsoft Azure ストレージ エクスプローラー](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。