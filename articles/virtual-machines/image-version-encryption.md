---
title: プレビュー - 独自のキーで暗号化されたイメージ バージョンを作成する
description: カスタマー マネージド暗号化キーを使用して、Shared Image Gallery にイメージ バージョンを作成します。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/11/2020
ms.author: cynthn
ms.openlocfilehash: 0d2b840b401dc90b332f91c93a9eda03d6643432
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245555"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>プレビュー:イメージの暗号化にカスタマー マネージド キーを使用する

ギャラリー イメージはマネージド ディスクとして保存されるため、サーバー側暗号化を使用して自動的に暗号化されます。 サーバー側暗号化には、利用可能な最強のブロック暗号の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)が使われ、FIPS 140-2 に準拠しています。 Azure マネージド ディスクの基になっている暗号化モジュールについて詳しくは、「[暗号化 API:次世代](/windows/desktop/seccng/cng-portal)」を参照してください。

プラットフォーム マネージド キーを利用すると、お使いのイメージを暗号化することも、独自のキーを使用することも、または両方を共に使用して二重に暗号化することもできます。 独自のキーを使用して暗号化を管理する場合は、イメージ内のすべてのディスクの暗号化と暗号化解除に使用する*カスタマー マネージド キー*を指定できます。 

カスタマー マネージド キーを使用したサーバー側暗号化には、Azure Key Vault が使用されます。 [ご使用の RSA キー](../key-vault/keys/hsm-protected-keys.md)を Key Vault にインポートするか、Azure Key Vault で新しい RSA キーを生成することができます。

## <a name="prerequisites"></a>前提条件

この記事では、お使いのイメージに使用するディスク暗号化が既に設定されている必要があります。

- カスタマー マネージド キーのみを [Azure portal](./windows/disks-enable-customer-managed-keys-portal.md) または [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset) で使用する場合は、**サーバー側の暗号化でカスタマー マネージド キーを有効にする**方法に関するページを参照してください。

- プラットフォーム マネージド キーとユーザー マネージド キーの両方を (二重暗号化)、[Azure portal](./windows/disks-enable-double-encryption-at-rest-portal.md) または [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md) で使用する場合は、**保存時の二重暗号化を有効にする**方法に関するページを参照してください。
    > [!IMPORTANT]
    > Azure portal にアクセスするには、このリンク[https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates)を使用します。 現時点では、このリンクを使用しないと、パブリックの Azure portal には保存時の二重暗号化が表示されません。

## <a name="limitations"></a>制限事項

共有イメージ ギャラリーのイメージを暗号化するためにカスタマー マネージド キーを使用する場合、いくつかの制限があります。  

- 暗号化キー セットは、お使いのイメージと同じサブスクリプションとリージョン内にある必要があります。

- カスタマー マネージド キーを使用するイメージは共有できません。 

- カスタマー マネージド キーを使用するイメージを他のリージョンにレプリケートすることはできません。

- いったん独自のキーを使用してディスクやイメージを暗号化すると、それらのディスクやイメージを暗号化するために、プラットフォーム マネージド キーを使用する方法に戻ることはできなくなります。


> [!IMPORTANT]
> カスタマー マネージド キーを使用した暗号化は、現在はパブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="powershell"></a>PowerShell

パブリック プレビューでは、まずこの機能を登録することが必要になります。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

登録が完了するまでに数分かかります。 機能の登録状態を確認するには、Get-AzProviderFeature を使用します。

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

RegistrationState が Registered を返したら、次の手順に進むことができます。

プロバイダーの登録を確認します。 `Registered` が返されることを確認します。

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

`Registered` が返されない場合は、次を使用してプロバイダーを登録します。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

イメージ バージョンのディスク暗号化セットを指定するには、`-TargetRegion` パラメーターを指定して [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) を使用します。 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @($region1)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>VM の作成

共有イメージ ギャラリーから VM を作成し、カスタマー マネージド キーを使用してディスクを暗号化することができます。 構文は、[一般化](vm-generalized-image-version-powershell.md)または[特殊化された](vm-specialized-image-version-powershell.md) VM をイメージから作成する場合と同じです。拡張パラメーター セットを使用して、VM 構成に `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` を追加する必要があります。

データ ディスクの場合は、[Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) を使用するときに `-DiskEncryptionSetId $setID` パラメーターを追加する必要があります。


## <a name="cli"></a>CLI 

パブリック プレビューでは、まずこの機能を登録することが必要になります。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

機能の登録の状態を確認します。

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

これで `"state": "Registered"` が返されたら、次の手順に進むことができます。

登録を確認します。

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

登録済みにならない場合は、次を実行します。

```azurecli-interactive
az provider register -n Microsoft.Compute
```


イメージ バージョンのディスク暗号化セットを指定するには、`--target-region-encryption` パラメーターを指定して [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) を使用します。 `--target-region-encryption` の形式は、OS とデータ ディスクを暗号化するためのキーのスペース区切りリストです。 `<encryption set for the OS disk>,<Lun number of the data disk>, <encryption set for the data disk>, <Lun number for the second data disk>, <encryption set for the second data disk>` のようになります。 

OS ディスクのソースがマネージド ディスクであるか VM である場合は、`--managed-image` を使用してイメージ バージョンのソースを指定します。 この例では、ソースは、OS ディスクと LUN 0 のデータ ディスクを備えたマネージド イメージです。 この OS ディスクは DiskEncryptionSet1 で暗号化され、データ ディスクは DiskEncryptionSet2 で暗号化されます。

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

OS ディスクのソースがスナップショットである場合は、`--os-snapshot` を使用して OS ディスクを指定します。 イメージ バージョンにも含まれる必要があるデータ ディスク スナップショットがある場合は、LUN の指定に `--data-snapshot-luns` を使用し、スナップショットの指定に `--data-snapshots` を使用してそれらを追加します。

この例では、ソースはディスク スナップショットです。 OS ディスクと、LUN 0 のデータ ディスクが存在します。 この OS ディスクは DiskEncryptionSet1 で暗号化され、データ ディスクは DiskEncryptionSet2 で暗号化されます。

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>VM の作成

共有イメージ ギャラリーから VM を作成し、カスタマー マネージド キーを使用してディスクを暗号化することができます。 構文は、[一般化](vm-generalized-image-version-cli.md)または[特殊化された](vm-specialized-image-version-cli.md) VM をイメージから作成する場合と同じです。必要なのは、暗号化セットの ID を使用して `--os-disk-encryption-set` パラメーターを追加することだけです。 データ ディスクの場合は、そのデータ ディスクのディスク暗号化セットのスペース区切りリストを使用して `--data-disk-encryption-sets` を追加します。


## <a name="portal"></a>ポータル

ポータルで自分のイメージ バージョンを作成する場合は、 **[暗号化]** タブを使用して、ストレージ暗号化セットに関する情報を入力します。

> [!IMPORTANT]
> 二重暗号化を使用するには、こちらのリンク[https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates)を使用して Azure portal にアクセスする必要があります。 現時点では、このリンクを使用しないと、パブリックの Azure portal には保存時の二重暗号化が表示されません。


1. **[イメージ バージョンを作成する]** ページで、 **[暗号化]** タブを選択します。
2. **[暗号化の種類]** で、 **[カスタマー マネージド キーを使用した保存時の暗号化]** または **[Double encryption with platform-managed and customer-managed keys]** \(プラットフォーム マネージド キーとカスタマー マネージド キーを使用した二重暗号化\) を選択します。 
3. イメージ内の各ディスクについて、使用する**ディスク暗号化セット**をドロップダウンから選択します。 

### <a name="create-the-vm"></a>VM の作成

イメージのバージョンから VM を作成し、カスタマー マネージド キーを使用してディスクを暗号化することができます。 ポータルで VM を作成する場合は、 **[ディスク]** タブの **[暗号化の種類]** で、 **[カスタマー マネージド キーを使用した保存時の暗号化]** または **[Double encryption with platform-managed and customer-managed keys]** \(プラットフォーム マネージド キーとカスタマー マネージド キーを使用した二重暗号化\) を選択します。 次に、ドロップダウンから暗号化セットを選択します。

## <a name="next-steps"></a>次のステップ

[ディスクのサーバー側暗号化](./windows/disk-encryption.md)について学習する。

購入プラン情報を提供する方法については、[イメージ作成時の Azure Marketplace 購入プラン情報の提供](marketplace-images.md)に関する記事を参照してください。
