---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 742e0028b1f92beb8300cc97f09d8292259fbc0a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712491"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>Azure Ultra SSD を有効にしてデプロイする (プレビュー)

Azure Ultra ソリッド ステート ドライブ (SSD) (プレビュー) では、Azure IaaS 仮想マシン (VM) に対して、高スループット、高 IOPS、一貫性のある低待機時間のディスク ストレージが提供されます。 この新しいオファリングは、Microsoft の既存のディスク オファリングと同じレベルの可用性で最上のパフォーマンスを提供します。 Ultra SSD のメリットの 1 つが、VM を再起動せずに、ご自身のワークロードに合わせて SSD のパフォーマンスを動的に変更できることです。 Ultra SSD は、SAP HANA、最上位層のデータベース、トランザクションの多いワークロードなど、データ集中型のワークロードに適しています。

現在、Ultra SSD はプレビュー段階であり、アクセスするにはプレビューに[登録](https://aka.ms/UltraSSDPreviewSignUp)する必要があります。

## <a name="determine-your-availability-zone"></a>可用性ゾーンを確認する

承認後、Ultra SSD を使用するには、ご自身の可用性ゾーンを確認する必要があります。 米国東部 2 内のどのゾーンに Ultra ディスクをデプロイするかを確認するには、次のいずれかのコマンドを実行します。

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query "[?name=='UltraSSD_LRS'].locationInfo"`

応答は下のフォームのようになります。X は、米国東部 2 でのデプロイに使用するゾーンです。 X は 1、2、3 のいずれかになります。

**[ゾーン]** の値を保持します。これは可用性ゾーンを表し、Ultra SSD のデプロイに必要です。

|ResourceType  |Name  |Location  |ゾーン  |Restriction  |機能  |値  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> コマンドから応答がなかった場合は、機能に対する登録がまだ保留中か、まだ承認されていません。

デプロイするゾーンがわかったので、この記事のデプロイ手順に従い、Ultra SSD を使用して最初の VM をデプロイします。

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Azure Resource Manager を使用して Ultra SSD をデプロイする

最初に、デプロイする VM のサイズを決定します。 このプレビューの一部としては、DsV3 および EsV3 VM ファミリのみがサポートされています。 VM サイズについて詳しくは、こちらの[ブログ](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)の 2 番目のテーブルをご覧ください。

複数の Ultra SSD を使用して VM を作成する場合は、[複数の Ultra SSD で VM を作成](https://aka.ms/UltraSSDTemplate)するためのサンプルを参照してください。

独自のテンプレートを使用する場合は、`Microsoft.Compute/virtualMachines` と `Microsoft.Compute/Disks` の **apiVersion** が `2018-06-01` (またはそれ以降) に設定されていることを確認します。

ディスク SKU を **UltraSSD_LRS** に設定し、ディスク容量、IOPS、可用性ゾーン、およびスループット (MBps) を設定して、Ultra ディスクを作成します。

VM がプロビジョニングされたら、データ ディスクをパーティション分割してフォーマットし、ワークロード用に構成できます。

## <a name="deploy-an-ultra-ssd-using-cli"></a>CLI を使用して Ultra SSD をデプロイする

最初に、デプロイする VM のサイズを決定します。 このプレビューの一部としては、DsV3 および EsV3 VM ファミリのみがサポートされています。 VM サイズについて詳しくは、こちらの[ブログ](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)の 2 番目のテーブルをご覧ください。

Ultra SSD を使用するには、Ultra SSD を使用できる VM を作成する必要があります。

**$vmname**、 **$rgname**、 **$diskname**、 **$location**、 **$password**、 **$user** の各変数を、ご自身の実際の値に置き換えるか、設定します。 **$zone** を、[この記事の最初](#determine-your-availability-zone)に取得したご自身の可用性ゾーンの値に設定します。 その後、次の CLI コマンドを実行して、Ultra 対応 VM を作成します。

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>CLI を使用して Ultra SSD を作成する

Ultra SSD を使用できる VM が作成されたので、Ultra SSD を作成して、その VM に接続します。

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>CLI を使用して Ultra SSD のパフォーマンスを調整する

Ultra SSD にはパフォーマンスを調整するための固有の機能が用意されています。次のコマンドは、この機能の使用方法を示しています。

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>PowerShell を使用して Ultra SSD をデプロイする

最初に、デプロイする VM のサイズを決定します。 このプレビューの一部としては、DsV3 および EsV3 VM ファミリのみがサポートされています。 VM サイズについて詳しくは、こちらの[ブログ](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)の 2 番目のテーブルをご覧ください。

Ultra SSD を使用するには、Ultra SSD を使用できる VM を作成する必要があります。 **$resourcegroup** 変数と **$vmName** 変数を、ご自身の実際の値に置き換えるか、設定します。 **$zone** を、[この記事の最初](#determine-your-availability-zone)に取得したご自身の可用性ゾーンの値に設定します。 その後、次の [New-AzVm](/powershell/module/az.compute/new-azvm) コマンドを実行して、Ultra 対応 VM を作成します。

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-ssd-using-powershell"></a>PowerShell を使用して Ultra SSD を作成する

Ultra SSD を使用できる VM が作成されたので、Ultra SSD を作成して、その VM に接続します。

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>PowerShell を使用して Ultra SSD のパフォーマンスを調整する

Ultra SSD にはパフォーマンスを調整するための固有の機能が用意されています。次のコマンドは、ディスクをデタッチせずにパフォーマンスを調整する例を示しています。

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>次の手順

新しいディスクの種類を試したい場合は、[この調査からプレビューへのアクセスを要求](https://aka.ms/UltraSSDPreviewSignUp)してください。