---
title: エフェメラル OS ディスク
description: Azure VM のエフェメラル OS ディスクの詳細について説明します。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 814824fb6708abaf549bb3de19b4aced4774a244
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "102485760"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Azure VM のエフェメラル OS ディスク

エフェメラル OS ディスクは、ローカルの仮想マシン (VM) ストレージで作成され、リモートの Azure Storage に保存されません。 エフェメラル OS ディスクは、ステートレス ワークロードで適切に動作します。この場合、アプリケーションは個々の VM 障害を許容しますが、VM のデプロイ時間または個々の VM インスタンスの再イメージ化によって影響をより強く受けます。 エフェメラル OS ディスクでは、OS ディスクへの読み取り/書き込み待機時間が短縮され、VM の再イメージ化が高速化されます。 
 
エフェメラル ディスクの主な機能は次のとおりです。 
- ステートレス アプリケーションに最適です。
- それらは Marketplace イメージとカスタム イメージの両方で使用できます。
- VM およびスケール セット インスタンスを元のブート状態に速やかにリセットまたは再イメージ化することができます。  
- 一時ディスクと同様に、待機時間が短縮されます。 
- エフェメラル OS ディスクは無料で、OS ディスクのストレージ コストはかかりません。
- それらはすべての Azure リージョンで使用できます。 
- エフェメラル OS ディスクは、[共有イメージ ギャラリー](./shared-image-galleries.md)でサポートされています。 
 

 
永続 OS ディスクとエフェメラル OS ディスクの主な違いは、次のとおりです。

|                             | 永続 OS ディスク                          | エフェメラル OS ディスク                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **OS ディスクのサイズ制限**      | 2 TiB                                                                                        | VM サイズのキャッシュ サイズまたは 2TiB のうち小さい方。 **GiB 単位のキャッシュ サイズ** については、[DS](sizes-general.md)、[ES](sizes-memory.md)、[M](sizes-memory.md)、[FS](sizes-compute.md)、および [GS](sizes-previous-gen.md#gs-series) に関するページを参照してください              |
| **サポート対象の VM サイズ**          | All                                                                                          | Premium ストレージをサポートしている DSv1、DSv2、DSv3、Esv3、Fs、FsV2、GS、M などの VM サイズ                                               |
| **サポート対象のディスクの種類**           | マネージド OS ディスクとアンマネージド OS ディスク                                                                | マネージド OS ディスクのみ                                                               |
| **リージョンのサポート**              | すべてのリージョン                                                                                  | すべてのリージョン                              |
| **データの永続化**            | 書き込まれた OS ディスク データは Azure Storage に格納                                  | OS ディスクに書き込まれたデータは、ローカル VM ストレージに格納され、Azure Storage に保持されない |
| **停止と割り当て解除**      | VM とスケール セット インスタンスの停止と割り当て解除、またその状態からの再起動が可能 | VM とスケール セット インスタンスの停止と割り当て解除は不可                                  |
| **特殊化された OS ディスクのサポート** | はい                                                                                          | いいえ                                                                                 |
| **OS ディスクのサイズ変更**              | VM 作成中、VM の停止と割り当て解除後に変更可能                                | VM 作成時のみ変更可能                                                  |
| **VM サイズ変更時の動作**   | OS ディスク データを維持                                                                    | OS ディスクのデータの削除後に OS を再プロビジョニング       
| **ページ ファイルの配置**   | Windows の場合、ページ ファイルはリソース ディスクに格納されます                                              | Windows の場合、ページ ファイルは OS ディスクに格納されます   |

## <a name="size-requirements"></a>サイズの要件

VM およびインスタンス イメージは、最大で VM キャッシュのサイズまでデプロイできます。 たとえば、マーケットプレースの標準の Windows Server イメージは約 127 GiB であり、これは 127 GiB より大きいキャッシュを持つ VM サイズが必要であることを意味します。 この場合、[Standard_DS2_v2](dv2-dsv2-series.md) のキャッシュ サイズは 86 GiB であり、十分な大きさではありません。 Standard_DS3_v2 のキャッシュ サイズは 172 GiB であり、十分な大きさです。 この場合、Standard_DS3_v2 が、このイメージで使用できる DSv2 シリーズの最小サイズ です。 Marketplace の基本の Linux イメージと `[smallsize]` で示される Windows Server イメージは約 30 GiB になる傾向があり、利用可能な VM サイズのほとんどを使用できます。

また、エフェメラル ディスクでは、VM サイズで Premium Storage がサポートされていることも必要です。 通常 (常にではありませんが)、 サイズには DSv2 や EsV3 のように名前に `s` があります。 詳細については、[Azure の VM サイズ](sizes.md)に関する記事で Premium Storage をサポートするサイズの詳細を参照してください。

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>プレビュー - エフェメラル OS ディスクを一時ディスクに保存できるようになりました
エフェメラル OS ディスクは、VM キャッシュに加え、VM の一時ディスクまたはリソース ディスクに保存できるようになりました。 つまり、これからは、VM にキャッシュがないか、キャッシュが十分でなくても、Dav3、Dav4、Eav4、Eav3 など、エフェメラル OS ディスクを保存する一時ディスクまたはリソース ディスクがあれば、エフェメラル OS ディスクを使用できます。 また、VM に十分なキャッシュと一時領域がある場合、[DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement) という名前の新しいプロパティを使用し、エフェメラル OS ディスクを格納する場所を指定できるようになります。 この機能を使用すると、Windows VM がプロビジョニングされる場合に、OS ディスクに配置されるページファイルが構成されます。 現在、この機能はプレビュー段階にあります。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 始めるには、[アクセスをリクエスト](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u)してください。

## <a name="powershell"></a>PowerShell

PowerShell VM デプロイにエフェメラル ディスクを使用するには、VM 構成内で [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) を使用します。 `-DiffDiskSetting` を `Local` に設定し、`-Caching` を `ReadOnly` に設定します。     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

スケール セットのデプロイの場合は、構成内で [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) コマンドレットを使用します。 `-DiffDiskSetting` を `Local` に設定し、`-Caching` を `ReadOnly` に設定します。


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

CLI VM デプロイにエフェメラル ディスクを使用するには、[az vm create](/cli/azure/vm#az-vm-create) 内の `--ephemeral-os-disk` パラメーターを `true` に設定し、`--os-disk-caching` パラメーターを `ReadOnly` に設定します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

スケール セットの場合は、[az-vmss-create](/cli/azure/vmss#az-vmss-create) に同じ `--ephemeral-os-disk true` パラメーターを使用し、`--os-disk-caching` パラメーターを `ReadOnly` に設定します。

## <a name="portal"></a>ポータル

Azure portal で、 **[ディスク]**  タブの **[詳細設定]** セクションを開いて VM をデプロイするときに、エフェメラル ディスクの使用を選択できます。 **[エフェメラル OS ディスクの使用]\(Use ephemeral OS disk\)** で **[はい]** を選択します。

![エフェメラル OS ディスクの使用を選択するラジオ ボタンを示すスクリーン ショット](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

エフェメラル ディスクの使用のオプションがグレー表示される場合は、OS イメージより大きいキャッシュ サイズがない VM サイズか、Premium Storage をサポートしていない VM サイズを選択した可能性があります。 **[基本]** ページに戻り、別の VM サイズを選択してみてください。

ポータルを使用してエフェメラル OS ディスクでスケール セットを作成することもできます。 十分な大きさのキャッシュ サイズを持つ VM サイズを選択してから、 **[エフェメラル OS ディスクの使用]\(Use ephemeral OS disk\)** で **[はい]** を選択するだけです。

![スケール セットに対してエフェメラル OS ディスクの使用を選択するラジオ ボタンを示すスクリーン ショット](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>スケール セット テンプレートのデプロイ  
エフェメラル OS ディスクを使用するスケール セットを作成するプロセスでは、テンプレートで `diffDiskSettings` プロパティをリソースの種類 `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` に追加します。 また、エフェメラル OS ディスクに対してキャッシュ ポリシーを `ReadOnly` に設定する必要があります。 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
            "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>VM テンプレートのデプロイ 
テンプレートを使用して、エフェメラル OS ディスクで VM をデプロイできます。 エフェメラル OS ディスクを使用する VM を作成するプロセスでは、テンプレートで `diffDiskSettings` プロパティをリソースの種類 Microsoft.Compute/virtualMachines に追加します。 また、エフェメラル OS ディスクに対してキャッシュ ポリシーを `ReadOnly` に設定する必要があります。 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>REST を使用して VM を再イメージ化する
以下で説明するように REST API を使用するか Azure portal で VM の [概要] ウィンドウに移動することで、エフェメラル OS ディスクを使用して仮想マシンを再イメージ化できます。 スケール セットの場合、再イメージ化は Powershell、CLI、およびポータルを使用して既に利用できます。

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q:ローカル OS ディスクのサイズは?**

A:プラットフォーム イメージとカスタム イメージを最大で VM キャッシュのサイズまでサポートしています。この場合、OS ディスクへのすべての読み取り/書き込みは、仮想マシンと同じノードでローカルに行われます。 

**Q:エフェメラル OS ディスクはサイズ変更できますか?**

A:いいえ。エフェメラル OS ディスクをプロビジョニングした後、OS ディスクをサイズ変更することはできません。 

**Q:エフェメラル VM にマネージド ディスクを接続できますか?**

A:はい。エフェメラル OS ディスクを使用する VM にマネージド データ ディスクをアタッチできます。 

**Q:エフェメラル OS ディスクではすべての VM サイズがサポートされますか?**

A:いいえ。ほとんどの Premium Storage VM サイズがサポートされています (DS、ES、FS、GS、M など)。 特定の VM サイズでエフェメラル OS ディスクがサポートされているかどうかは、次の方法で確認できます。

`Get-AzComputeResourceSku` PowerShell コマンドレットを呼び出します。
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**Q:エフェメラル OS ディスクを既存の VM およびスケール セットに適用できますか?**

A:いいえ。エフェメラル OS ディスクは、VM およびスケール セットの作成時にのみ使用できます。 

**Q:スケール セット内でエフェメラル OS ディスクと通常の OS ディスクを混在させることはできますか?**

A:いいえ。同じスケール セット内でエフェメラル OS ディスクと永続 OS ディスクのインスタンスを混在させることはできません。 

**Q:Powershell または CLI を使用してエフェメラル OS ディスクを作成できますか?**

A:はい。REST、テンプレート、PowerShell、CLI を使用して、エフェメラル OS ディスクで VM を作成できます。

**Q:エフェメラル OS ディスクでサポートされていない機能は何ですか?**

A:エフェメラル ディスクでサポートされていない機能は次のとおりです。
- VM イメージのキャプチャ
- ディスクのスナップショット 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- OS ディスクのスワップ 

> [!NOTE]
> 
> エフェメラル ディスクにはポータルからアクセスできません。 予想されるエフェメラル ディスクにアクセスするときに、"リソースが見つかりません" または "404" エラーが表示されます。
> 
 
## <a name="next-steps"></a>次のステップ
[Azure CLI](/cli/azure/vm#az-vm-create) を使用して、エフェメラル OS ディスクで VM を作成できます。
