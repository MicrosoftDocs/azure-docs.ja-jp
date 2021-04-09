---
title: VHD を Azure にアップロードするか、リージョン間でディスクをコピーする - Azure PowerShell
description: Azure PowerShell を使用して、直接アップロード経由で、Azure マネージド ディスクに VHD をアップロードし、リージョン間でマネージド ディスクをコピーする方法を説明します。
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: c766c78705a1c1e40a9385360d35ac06a3db3a5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99252239"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>VHD を Azure にアップロードするか、他のリージョンにマネージド ディスクをコピーする - Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>前提条件

- 最新[バージョンの AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy) をダウンロードします。
- [Azure PowerShell モジュールをインストールします](/powershell/azure/install-Az-ps)。
- オンプレミスから VHD をアップロードする場合: ローカルに保存され、[Azure 用に準備された](prepare-for-upload-vhd-image.md)固定サイズの VHD。
- または、コピー操作を実行する場合は、Azure のマネージド ディスク。

## <a name="getting-started"></a>作業の開始

GUI を使用してディスクをアップロードする場合は、Azure Storage Explorer を使用します。 詳細については、次を参照してください。[Azure Storage Explorer を使用して Azure マネージド ディスクを管理する](../disks-use-storage-explorer-managed-disks.md)

ご利用の VHD を Azure にアップロードするには、このアップロード プロセス用に構成された空のマネージド ディスクを作成する必要があります。 作成する前に、それらのディスクについて把握しておく必要がある追加情報があります。

この種類のマネージド ディスクには、2 つの固有の状態があります。

- ReadToUpload。ディスクはアップロードを受け取る準備ができていますが、[Secure Access Signature](../../storage/common/storage-sas-overview.md) (SAS) が生成されていないことを意味します。
- ActiveUpload。ディスクはアップロードを受け取る準備ができており、SAS が生成済みであることを意味します。

> [!NOTE]
> これらのいずれかの状態にある間は、実際のディスクの種類に関係なく、マネージド ディスクには [Standard HDD 料金](https://azure.microsoft.com/pricing/details/managed-disks/)が課金されます。 たとえば、P10 は S10 として課金されます。 これは、`revoke-access` がマネージド ディスク上で呼び出される (ディスクを VM にアタッチするために必要な処理) まで当てはまります。

## <a name="create-an-empty-managed-disk"></a>空のマネージド ディスクを作成する

アップロード用に空の標準 HDD を作成するには、アップロードする vhd のファイル サイズ (バイト単位) が必要です。 このコード例ではこのサイズを自動で取得できますが、この操作を自分で行うには、`$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` を使用します。 この値は、 **-UploadSizeInBytes** パラメーターを指定するときに使用します。

次に、ご利用のローカル シェル上に、アップロード用の空の標準 HDD を作成するには、[New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) コマンドレットにおいて、 **-CreateOption** パラメーターに **Upload** 設定を指定すると共に、 **-UploadSizeInBytes** パラメーターを指定します。 次に、[New-AzDisk](/powershell/module/az.compute/new-azdisk) を呼び出してディスクを作成します。

`<yourdiskname>`、`<yourresourcegroupname>`、`<yourregion>` を置き換え、次のコマンドを実行します。

> [!TIP]
> OS ディスクを作成する場合は、`-HyperVGeneration '<yourGeneration>'` を `New-AzDiskConfig` に追加します。

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Premium SSD または Standard SSD をアップロードする場合は、**Standard_LRS** を **Premium_LRS** または **StandardSSD_LRS** のいずれかに置き換えます。 Ultra Disks はまだサポートされていません。

アップロード プロセス用に構成された空のマネージド ディスクを作成したので、VHD をアップロードすることができます。 VHD をそのディスクにアップロードするには、書き込み可能な SAS をアップロード先とし参照できるように、用意する必要があります。

空のマネージド ディスクの書き込み可能な SAS を生成するには、`<yourdiskname>` と `<yourresourcegroupname>` を置き換えてから、次のコマンドを使用します。

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>VHD のアップロード

空のマネージド ディスク用の SAS が用意できたので、それを使用して、マネージド ディスクをアップロード コマンドのアップロード先として設定できます。

AzCopy v10 を使用して、生成した SAS URI を指定してローカルの VHD ファイルをマネージド ディスクにアップロードします。

このアップロードのスループットは、同等の [Standard HDD](../disks-types.md#standard-hdd) と同じです。 たとえば、S4 と同等のサイズの場合、最大 60 MiB/秒のスループットが得られます。 ただし、S70 と同等のサイズの場合、最大 500 MiB/秒のスループットが得られます。

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

アップロードが完了し、ディスクにこれ以上データを書き込む必要がなくなったら、SAS を無効にします。 SAS を無効にすると、マネージド ディスクの状態が変わり、そのディスクを VM にアタッチできるようになります。

`<yourdiskname>` と `<yourresourcegroupname>` を置き換え、次のコマンドを実行します。

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>マネージド ディスクをコピーする

ダイレクト アップロードを行うと、マネージド ディスクをコピーするプロセスも簡略化されます。 同じリージョン内にコピーするか、マネージド ディスクを別のリージョンにコピーすることができます。

次のスクリプトでは、これが自動的に実行されます。このプロセスは前述の手順と似ていますが、既存のディスクを操作しているため、いくつかの違いがあります。

> [!IMPORTANT]
> Azure のマネージド ディスクのディスク サイズ (バイト単位) を指定する場合は、512 のオフセットを追加する必要があります。 これは、Azure からディスク サイズが返されるときにフッターが省略されるためです。 この操作を行わないと、コピーは失敗します。 次のスクリプトでは、この操作が自動的に行われています。

`<sourceResourceGroupHere>`、`<sourceDiskNameHere>`、`<targetDiskNameHere>`、`<targetResourceGroupHere>`、`<yourOSTypeHere>`、および `<yourTargetLocationHere>` (場所の値の例: uswest2) を実際の値に置き換えたら、次のスクリプトを実行してマネージド ディスクをコピーします。

> [!TIP]
> OS ディスクを作成する場合は、-HyperVGeneration '<yourGeneration>' を `New-AzDiskConfig` に追加します。

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

## <a name="next-steps"></a>次のステップ

VHD をマネージド ディスクに正常にアップロードしたので、ディスクを VM にアタッチして使用を開始できます。

データ ディスクを VM にアタッチする方法については、この主題に関する記事を参照してください。[PowerShell を使用してデータ ディスクを Windows VM にアタッチする](attach-disk-ps.md)。 ディスクを OS ディスクとして使用する方法については、「[特殊化されたディスクからの Windows VM の作成](create-vm-specialized.md#create-the-new-vm)」を参照してください。
