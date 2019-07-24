---
title: 低優先度の VM を使用する Azure スケール セットを作成する (プレビュー) | Microsoft Docs
description: コスト削減のために低優先度の VM を使う Azure 仮想マシン スケール セットを作成する方法を説明します
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: 861c68ae8163e0ba8c2af2a3d96153ac3e84855f
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978138"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>スケール セットでの低優先度の VM (プレビュー)

スケール セットで低優先度の VM を使うと、非常に低コストで未使用の容量を利用できます。 Azure で容量の回復が必要になると常に、Azure インフラストラクチャは低優先度の VM を排除します。 したがって、低優先度の VM は、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断してもかまわないワークロードに最適です。

利用可能な未使用の容量は、サイズ、リージョン、時刻などによって異なります。 スケール セットに低優先度の VM を展開すると、利用可能な容量がある場合は Azure は VM を割り当てますが、このような VM には SLA はありません。 低優先度のスケール セットは 1 つの障害ドメインに展開され、高可用性の保証はありません。

## <a name="eviction-policy"></a>削除ポリシー

低優先度のスケール セットの作成では､この排除ポリシーを*Deallocate* (既定) または *Delete* のいずれかに設定できます｡ 

*Deallocate* ポリシーでは､排除された VM は stopped-deallocated 状態に移行し､排除されたインスタンスはデプロイし直すことができます｡ ただし､割り当てが成功する保証はありません｡ 割り当てを解除された VM は、スケール セットのインスタンス クォータを基にカウントされ、構成しているディスクの料金が課金されます。 

低優先度のスケールセットの VM が排除されるときに、VM を削除したい場合は、排除ポリシーを *deliete* に設定できます。 排除ポリシーを削除に設定した場合、スケール セット インスタンスのカウント プロパティを増やすことで、新しい VM を作成できます。 排除された VM は基になっているディスクと共に削除されるので、ストレージが課金されることはありません。 また、スケール セットの自動スケーリング機能を使って、排除された VM の自動的な補正を試みることはできますが、割り当てが成功するという保証はありません。 排除ポリシーを削除に設定して、ディスクのコストとクォータ制限への到達を回避するとき、低優先度のスケール セットでは自動スケーリング機能だけを使うことをお勧めします。 

> [!NOTE]
> プレビュー中は、[Azure Portal](#use-the-azure-portal)と [Azure Resource Manager テンプレート](#use-azure-resource-manager-templates)を使って排除ポリシーを設定できます。 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>スケール セットへの低優先度 VM の展開

スケール セットに低優先度の VM を展開するには、新しい *Priority* フラグを *Low* に設定します。 スケール セット内のすべての VM が、低優先度に設定されます。 低優先度の VM でスケール セットを作成するには、次のいずれかの方法を使います。
- [Azure Portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure リソース マネージャーのテンプレート](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

低優先度の VM を使用するスケール セットを作成するプロセスは、[使用の開始に関する記事](quick-create-portal.md)で詳しく説明されているプロセスと同じです。 スケール セットのデプロイでは､低優先度フラグと排除ポリシーを設定することができます｡![低優先度の VM からなるスケール セットの作成](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli"></a>Azure CLI の使用

低優先度の VM でスケール セットを作成するプロセスは、[使用の開始に関する記事](quick-create-cli.md)で詳しく説明されているものと同じです。 次の例に示すように、"--Priority" パラメーターを CLI の呼び出しに追加し、値を *Low* に設定します。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Azure PowerShell の使用

低優先度の VM でスケール セットを作成するプロセスは、[使用の開始に関する記事](quick-create-powershell.md)で詳しく説明されているものと同じです。
次の例に示すように、[New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) に '-Priority' パラメーターを追加し、それを *Low* に設定するだけです。

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用する

低優先度の VM を使うスケール セットを作成するプロセスは、[Linux](quick-create-template-linux.md) または [Windows](quick-create-template-windows.md) での使用の開始に関する記事で詳しく説明されているものと同じです。 テンプレートの *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* のリソースの種類に "priority" プロパティを追加し、値として *Low* を指定します。 必ず *2018-03-01* API バージョン以降を使用してください｡ 

排除ポリシーを削除に設定するには、"evictionPolicy" パラメーターを追加して、値を *delete* に設定します。

次の例では、"*米国中西部*" に *myScaleSet* という名前で Linux の低優先度スケール セットを作成します。排除時には、スケール セットの VM は "*削除*" されます。

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
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
## <a name="faq"></a>FAQ

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>既存のスケール セットを低優先度のスケール セットに変換できますか ?
できません｡低優先度の設定は作成時にのみ行うことができます｡

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>通常の VM と低優先度の VM の両方からなるスケール セットを作成できますか ?
できません｡スケール セットが複数の異なる優先順位をサポートすることはできません｡

### <a name="how-is-quota-managed-for-low-priority-vms"></a>低優先度の VM に対するクォータの管理はどのようになっているのでしょうか ?
低優先度の VM と通常の VM は同じクォータ プールを利用します｡ 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>低優先度スケール セットで自動スケールを使用することはできますか ?
はい､できます｡低優先度スケール セットに自動スケール ルールを設定できます｡ VM が排除されると､自動スケールは新しい低優先度 VM の作成を試みることができます｡ ただし､この容量が保証されるわけではないことを忘れないでください｡ 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>自動スケールは両方の排除ポリシー (割り当て解除と削除) で機能しますか ?
自動スケールを使用する場合は排除ポリシーを削除に設定することをお勧めします｡ これは､割り当てを解除されたインスタンスが､スケール セットに対する容量数を基にカウントされるためです｡ 自動スケールを使用すると､インスタンスが割り当て解除されて排除されるため､短時間に目標インスタンス数に達する可能性があります｡ 

## <a name="next-steps"></a>次の手順
低優先度の VM でスケール セットを作成したので、[低優先度を使って自動スケール テンプレート](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri)を展開してみてください。

価格について詳しくは、[仮想マシン スケール セットの価格のページ](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)をご覧ください。
