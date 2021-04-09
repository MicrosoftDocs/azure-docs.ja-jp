---
title: VHD を Azure にアップロードするか、リージョン間でディスクをコピーする - Azure CLI
description: Azure CLI を使用して、直接アップロードによって Azure マネージド ディスクに VHD をアップロードし、リージョン間でマネージド ディスクをコピーする方法を説明します。
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 473e87904742395eca6b7eeba0875cd93789104d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978987"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>VHD を Azure にアップロードするか、他のリージョンにマネージド ディスクをコピーする - Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>前提条件

- 最新[バージョンの AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy) をダウンロードします。
- [Azure CLI のインストール](/cli/azure/install-azure-cli)を実行します。
- オンプレミスから VHD をアップロードする場合: ローカルに保存され、[Azure 用に準備された](../windows/prepare-for-upload-vhd-image.md)固定サイズの VHD。
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

アップロード用に空の標準 HDD を作成するには、事前に、アップロードする VHD のファイル サイズ (バイト単位) が必要です。 これを取得するには、`wc -c <yourFileName>.vhd` または `ls -al <yourFileName>.vhd` を使用できます。 この値は、 **--upload-size-bytes** パラメーターを指定するときに使用します。

アップロード用の空の標準 HDD を作成するには、[disk create](/cli/azure/disk#az-disk-create) コマンドレットに、 **--for-upload** パラメーターと **--upload-size-bytes** パラメーターの両方を指定します。

`<yourdiskname>`、`<yourresourcegroupname>`、`<yourregion>` をご自身で選んだ値に置き換えます。 `--upload-size-bytes` パラメーターには例の値 `34359738880` が含まれており、それを適切な値に置き換えます。

> [!TIP]
> OS ディスクを作成する場合は、--hyper-v-generation <yourGeneration> を `az disk create` に追加します。

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Premium SSD または Standard SSD のいずれかをアップロードする場合は、**standard_lrs** を **premium_LRS** または **standardssd_lrs** のいずれかに置き換えます。 現時点では、Ultra ディスクはサポートされていません。

アップロード プロセス用に構成された空のマネージド ディスクを作成したので、そこに VHD をアップロードすることができます。 VHD をそのディスクにアップロードするには、書き込み可能な SAS をアップロード先として参照できるように用意する必要があります。

空のマネージド ディスクの書き込み可能な SAS を生成するには、`<yourdiskname>` と `<yourresourcegroupname>` を置き換えてから、次のコマンドを使用します。

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

戻り値のサンプル:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>VHD のアップロード

空のマネージド ディスク用の SAS が用意できたので、それを使用して、マネージド ディスクをアップロード コマンドのアップロード先として設定できます。

AzCopy v10 を使用して、生成した SAS URI を指定してローカルの VHD ファイルをマネージド ディスクにアップロードします。

このアップロードのスループットは、同等の [Standard HDD](../disks-types.md#standard-hdd) と同じです。 たとえば、S4 と同等のサイズの場合、最大 60 MiB/秒のスループットが得られます。 ただし、S70 と同等のサイズの場合、最大 500 MiB/秒のスループットが得られます。

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

アップロードが完了し、ディスクにこれ以上データを書き込む必要がなくなったら、SAS を無効にします。 SAS を無効にすると、マネージド ディスクの状態が変わり、そのディスクを VM にアタッチできるようになります。

`<yourdiskname>` と `<yourresourcegroupname>` を置き換えたうえで、次のコマンドを使ってディスクを使用可能にします。

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>マネージド ディスクをコピーする

ダイレクト アップロードを行うと、マネージド ディスクをコピーするプロセスも簡略化されます。 同じリージョン内またはリージョン間で (別のリージョンへ) コピーできます。

次のスクリプトでは、これが自動的に実行されます。このプロセスは前述の手順と似ていますが、既存のディスクを操作しているため、いくつかの違いがあります。

> [!IMPORTANT]
> Azure のマネージド ディスクのディスク サイズ (バイト単位) を指定する場合は、512 のオフセットを追加する必要があります。 これは、Azure からディスク サイズが返されるときにフッターが省略されるためです。 この操作を行わないと、コピーは失敗します。 次のスクリプトでは、この操作が自動的に行われています。

`<sourceResourceGroupHere>`、`<sourceDiskNameHere>`、`<targetDiskNameHere>`、`<targetResourceGroupHere>`、`<yourTargetLocationHere>` (場所の値の例: uswest2) を実際の値に置き換えたら、次のスクリプトを実行してマネージド ディスクをコピーします。

> [!TIP]
> OS ディスクを作成する場合は、--hyper-v-generation <yourGeneration> を `az disk create` に追加します。

```azurecli
sourceDiskName=<sourceDiskNameHere>
sourceRG=<sourceResourceGroupHere>
targetDiskName=<targetDiskNameHere>
targetRG=<targetResourceGroupHere>
targetLocation=<yourTargetLocationHere>

sourceDiskSizeBytes=$(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocation --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI=$(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>次のステップ

これでマネージド ディスクに VHD が正常にアップロードされたので、ディスクを[既存の VM にデータ ディスク](add-disk.md)として接続するか、[ディスクを OS ディスクとして VM に接続](upload-vhd.md#create-the-vm)して、新しい VM を作成することができます。