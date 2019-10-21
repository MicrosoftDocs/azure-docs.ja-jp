---
title: Azure PowerShell を使用して Azure に vhd をアップロードする
description: Azure PowerShell を使用して Azure マネージド ディスクに vhd をアップロードし、リージョン間でマネージド ディスクをコピーする方法を説明します。
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 88b5cacf432e467c893dac6fc5839c468b2eafbd
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828658"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Azure PowerShell を使用して Azure に vhd をアップロードする

この記事では、ご利用のローカル コンピューターから Azure マネージド ディスクに vhd をアップロードする方法について説明します。 以前は、データをストレージ アカウントにステージングし、そのストレージ アカウントを管理するなど、より複雑なプロセスを実行する必要がありました。 現在では、vhd をアップロードするためにストレージ アカウントを管理したり、データをステージングしたりする必要はなくなりました。 代わりに、空のマネージド ディスクを作成し、それに vhd を直接アップロードします。 これにより、オンプレミスの VM を Azure にアップロードする操作が簡単になり、最大 32 TiB の vhd を大きなマネージド ディスクに直接アップロードできます。

Azure の IaaS VM 向けにバックアップ ソリューションを提供している場合は、直接アップロードを使用して顧客のバックアップをマネージド ディスクに復元することをお勧めします。 Azure の外部のマシンから VHD をアップロードする場合、速度はご利用のローカル帯域幅によって異なります。 Azure VM を使用している場合、ご利用の帯域幅は標準 HDD と同じになります。

現在、直接アップロードは、Standard HDD、Standard SSD、および Premium SSD マネージド ディスクでサポートされています。 Ultra SSD ではまだサポートされていません。

## <a name="prerequisites"></a>前提条件

- 最新[バージョンの AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy) をダウンロードします。
- [Azure PowerShell モジュールをインストールします](/powershell/azure/install-Az-ps)。
- オンプレミスから vhd をアップロードする場合: ローカルに保存され、[Azure 用に準備された](prepare-for-upload-vhd-image.md) vhd。
- または、コピー操作を実行する場合は、Azure のマネージド ディスク。

## <a name="create-an-empty-managed-disk"></a>空のマネージド ディスクを作成する

ご利用の vhd を Azure にアップロードするには、このアップロード プロセス用に構成された空のマネージド ディスクを作成する必要があります。 作成する前に、それらのディスクについて把握しておく必要がある追加情報があります。

この種類のマネージド ディスクには、2 つの固有の状態があります。

- ReadToUpload。ディスクはアップロードを受け取る準備ができていますが、[Secure Access Signature](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) が生成されていないことを意味します。
- ActiveUpload。ディスクはアップロードを受け取る準備ができており、SAS が生成済みであることを意味します。

これらのいずれかの状態にある間は、実際のディスクの種類に関係なく、マネージド ディスクには [Standard HDD 料金](https://azure.microsoft.com/pricing/details/managed-disks/)が課金されます。 たとえば、P10 は S10 として課金されます。 これは、`revoke-access` がマネージド ディスク上で呼び出される (ディスクを VM にアタッチするために必要な処理) まで当てはまります。

アップロード用に空の標準 HDD を作成するには、アップロードする vhd ファイルのサイズ (バイト単位) が必要です。 このコード例ではこのサイズを自動で取得できますが、この操作を自分で行うには、`$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` を使用します。 この値は、 **-UploadSizeInBytes** パラメーターを指定するときに使用します。

次に、ご利用のローカル シェル上に、アップロード用の空の標準 HDD を作成するには、[New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) コマンドレットにおいて、 **-CreateOption** パラメーターに **Upload** 設定を指定すると共に、 **-UploadSizeInBytes** パラメーターを指定します。 次に、[New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) を呼び出してディスクを作成します。

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Premium SSD または Standard SSD をアップロードする場合は、**Standard_LRS** を **Premium_LRS** または **StandardSSD_LRS** のいずれかに置き換えます。 Ultra SSD はまだサポートされていません。

これで、アップロード プロセス用に構成された空のマネージド ディスクが作成されました。 vhd をそのディスクにアップロードするには、書き込み可能な SAS をアップロード先とし参照できるように、用意する必要があります。

空のマネージド ディスクの書き込み可能な SAS を生成するには、次のコマンドを使用します。

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>vhd をアップロードする

空のマネージド ディスク用の SAS が用意できたので、それを使用して、マネージド ディスクをアップロード コマンドのアップロード先として設定できます。

AzCopy v10 を使用して、生成した SAS URI を指定してローカルの VHD ファイルをマネージド ディスクにアップロードします。

このアップロードのスループットは、同等の [Standard HDD](disks-types.md#standard-hdd) と同じです。 たとえば、S4 と同等のサイズの場合、最大 60 MiB/秒のスループットが得られます。 ただし、S70 と同等のサイズの場合、最大 500 MiB/秒のスループットが得られます。

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

アップロード中に SAS の有効期限が切れ、まだ `revoke-access` を呼び出していない場合は、再度 `grant-access` を使用して新しい SAS を取得し、アップロードを続行できます。

アップロードが完了し、ディスクにこれ以上データを書き込む必要がなくなったら、SAS を無効にします。 SAS を無効にすると、マネージド ディスクの状態が変わり、そのディスクを VM にアタッチできるようになります。

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>マネージド ディスクをコピーする

ダイレクト アップロードを行うと、マネージド ディスクをコピーするプロセスも簡略化されます。 同じリージョン内またはリージョン間 (別のリージョン) でコピーできます。

次のスクリプトでは、これが自動で実行されます。このプロセスは前述の手順と似ていますが、既存のディスクを操作しているため、いくつかの違いがあります。

> [!IMPORTANT]
> Azure のマネージド ディスクのディスク サイズ (バイト単位) を指定する場合は、512 のオフセットを追加する必要があります。 これは、Azure からディスク サイズが返されるときにフッターが省略されるためです。 この操作を行わないと、コピーは失敗します。 次のスクリプトでは、この操作が自動的に行われています。

`<sourceResourceGroupHere>`、`<sourceDiskNameHere>`、`<targetDiskNameHere>`、`<targetResourceGroupHere>`、`<yourOSTypeHere>`、および `<yourTargetLocationHere>` (場所の値の例: uswest2) を実際の値に置き換えたら、次のスクリプトを実行してマネージド ディスクをコピーします。

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>次の手順

vhd をマネージド ディスクに正常にアップロードしたので、ディスクを VM にアタッチして使用を開始できます。

ディスクを VM にアタッチする方法については、この主題に関する記事を参照してください。[PowerShell を使用してデータ ディスクを Windows VM にアタッチする](attach-disk-ps.md)。
