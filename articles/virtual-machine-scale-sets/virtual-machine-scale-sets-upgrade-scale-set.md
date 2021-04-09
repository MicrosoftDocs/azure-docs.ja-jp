---
title: Azure 仮想マシン スケール セットを変更する
description: REST API、Azure PowerShell、Azure CLI を使って、Azure 仮想マシン スケール セットに変更を加え、更新する方法について説明します
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/10/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: bd16f0ef330d1d4a33dd796af0ec3e94dda5acfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684595"
---
# <a name="modify-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを変更する

アプリケーションのライフサイクルを通して、仮想マシン スケール セットを変更したり更新したりすることが必要になる場合があります。 スケール セットの構成を更新したりアプリケーションの構成を変更したりする際の方法が、こうした更新の対象になることもあります。 この記事では、REST API、Azure PowerShell、Azure CLI を使って、既存のスケール セットに変更を加える方法を説明します。

## <a name="fundamental-concepts"></a>基本的な概念

### <a name="the-scale-set-model"></a>スケール セット モデル
スケール セットには、スケール セット全体の "*望ましい*" 状態をキャプチャする "スケール セット モデル" があります。 スケール セット モデルは、次の方法で照会することができます。 

- REST API ([compute/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get))

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell ([Get-AzVmss](/powershell/module/az.compute/get-azvmss))

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI ([az vmss show](/cli/azure/vmss))

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- その他、[resources.azure.com](https://resources.azure.com) や言語固有の [Azure SDK](https://azure.microsoft.com/downloads/) を使う方法もあります。

出力の正確な表示は、コマンドに指定したオプションによって異なります。 次の例は、Azure CLI から得たサンプル出力の抜粋です。

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

これらのプロパティはスケール セット全体に適用されます。


### <a name="the-scale-set-instance-view"></a>スケール セットのインスタンス ビュー
スケール セットには、スケール セット全体の現在の "*ランタイム*" 状態をキャプチャする "スケール セットのインスタンス ビュー" もあります。 スケール セットのインスタンス ビューのクエリを実行するには、以下を使用できます。

- REST API ([compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview))

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell ([Get-AzVmss](/powershell/module/az.compute/get-azvmss))

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI ([az vmss get-instance-view](/cli/azure/vmss))

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- その他、[resources.azure.com](https://resources.azure.com) や言語固有の [Azure SDK](https://azure.microsoft.com/downloads/) を使う方法もあります

出力の正確な表示は、コマンドに指定したオプションによって異なります。 次の例は、Azure CLI から得たサンプル出力の抜粋です。

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

これらのプロパティは、スケールセット内の VM の現在のランタイム状態の概要を示します (スケール セットに適用された拡張機能の状態など)。


### <a name="the-scale-set-vm-model-view"></a>スケール セットの VM モデル ビュー
スケール セットにモデル ビューがあるのと同様に、スケール セット内の各 VM インスタンスには独自のモデル ビューがあります。 スケール セットで特定の VM インスタンスに関してモデル ビューにクエリを実行するには、以下を使用できます。

- REST API ([compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get))

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell ([Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm))

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI ([az vmss show](/cli/azure/vmss))

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- その他、[resources.azure.com](https://resources.azure.com) や [Azure SDK](https://azure.microsoft.com/downloads/) を使う方法もあります。

出力の正確な表示は、コマンドに指定したオプションによって異なります。 次の例は、Azure CLI から得たサンプル出力の抜粋です。

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

これらのプロパティは、スケール セット全体の構成ではなく、スケール セット内の VM インスタンスの構成を示しています。 たとえば、スケール セット モデルにはプロパティとして `overprovision` がありますが、スケール セット内の VM インスタンスのモデルにはありません。 この違いは、オーバープロビジョニングがスケール セット内の個々の VM インスタンスではなくスケール セット全体のプロパティであることが原因です (オーバープロビジョニングについて詳しくは、「[スケール セットの設計上の考慮事項](virtual-machine-scale-sets-design-overview.md#overprovisioning)」をご覧ください)。


### <a name="the-scale-set-vm-instance-view"></a>スケール セットの VM インスタンス ビュー
スケール セットにインスタンス ビューがあるのと同様に、スケール セット内の各 VM インスタンスには独自のインスタンス ビューがあります。 スケール セット内の特定の VM インスタンスに関してインスタンス ビューにクエリを実行するには、以下を使用できます。

- REST API ([compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview))

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell ([Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm))

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI ([az vmss get-instance-view](/cli/azure/vmss))

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- その他、[resources.azure.com](https://resources.azure.com) や [Azure SDK](https://azure.microsoft.com/downloads/) を使う方法もあります

出力の正確な表示は、コマンドに指定したオプションによって異なります。 次の例は、Azure CLI から得たサンプル出力の抜粋です。

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

これらのプロパティは、スケール セット内の VM インスタンスの現在のランタイム状態を示しています。これには、スケール セットに適用された拡張機能が含まれます。


## <a name="how-to-update-global-scale-set-properties"></a>グローバル スケール セットのプロパティの更新方法
グローバル スケール セットのプロパティを更新するには、スケール セット モデルのプロパティを更新する必要があります。 この更新は以下を使用して実行できます。

- REST API ([compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate))

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- REST API からプロパティを使用して Resource Manager テンプレートをデプロイすることで、グローバル スケール セットのプロパティを更新することもできます。

- Azure PowerShell ([Update-AzVmss](/powershell/module/az.compute/update-azvmss))

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI ([az vmss update](/cli/azure/vmss))
    - プロパティを変更する:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - スケール セットのリスト プロパティにオブジェクトを追加する: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - スケール セットのリスト プロパティからオブジェクトを削除する: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - `az vmss create` コマンドを使用してスケール セットを既にデプロイした場合は、`az vmss create` コマンドをもう一度実行してスケール セットを更新できます。 変更するプロパティを除き、`az vmss create` コマンドのすべてのプロパティが以前と同じであることを確認してください。

- その他、[resources.azure.com](https://resources.azure.com) や [Azure SDK](https://azure.microsoft.com/downloads/) を使う方法もあります。

スケール セット モデルを更新すると、スケール セットに作成された新しい VM に新しい構成が適用されます。 ただし、スケール セット内の既存の VM のモデルは、最新の全体的なスケール セット モデルで最新の状態にされている必要があります。 各 VM のモデルでは、`latestModelApplied` というブール型のプロパティがあります。このプロパティは、VM が最新の全体的なスケール セット モデルで最新の状態にされているかどうかを示します (`true` は VM が最新のモデルで最新の状態になっていることを意味します)。


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>最新のスケール セット モデルで VM を最新の状態にする方法
スケール セットには、VM を最新のスケール セット モデルで最新の状態にする方法を決定する "アップグレード ポリシー" があります。 アップグレード ポリシーの 3 つのモードは次のとおりです。

- **自動** - このモードでは、スケール セットは VM の停止順序を保証しません。 スケール セットは、すべての VM を同時に停止できます。 
- **ローリング** - このモードでは、スケール セットは更新をバッチでロールアウトします。必要に応じて、バッチ間の一時停止時間を設定します。
- **手動** - このモードでは、スケール セット モデルの更新時に、既存の VM は更新されません。
 
既存の VM を更新するには、各既存の VM の "手動アップグレード" を行う必要があります。 この手動アップグレードは以下を使用して行うことができます。

- REST API ([compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances))

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell ([Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance))
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI ([az vmss update-instances](/cli/azure/vmss))

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- その他、言語固有の [Azure SDK](https://azure.microsoft.com/downloads/) を使う方法もあります。

>[!NOTE]
> Service Fabric クラスターは "*自動*" モードのみを使用できますが、更新は別途処理されます。 詳しくは、「[Service Fabric アプリケーションのアップグレード](../service-fabric/service-fabric-application-upgrade.md)」をご覧ください。

グローバル スケール セットのプロパティに対する変更には、アップグレード ポリシーに従わない種類が 1 つあります。 スケール セット OS およびデータ ディスク プロファイル (管理者のユーザー名とパスワードなど) に対する変更は、API バージョン *2017-12-01* 以降でのみ実行できます。 これらの変更は、スケール セット モデルの変更後に作成された VM にのみ適用されます。 既存の VM を最新の状態にするには、各既存の VM の "再イメージ化" を行う必要があります。 この再イメージ化は、以下を使用して行うことができます。

- REST API ([compute/virtualmachinescalesets/reimage](/rest/api/compute/virtualmachinescalesets/reimage))

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell ([Set-AzVmssVm](/powershell/module/az.compute/set-azvmssvm))

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI ([az vmss reimage](/cli/azure/vmss))

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- その他、言語固有の [Azure SDK](https://azure.microsoft.com/downloads/) を使う方法もあります。


## <a name="properties-with-restrictions-on-modification"></a>変更に制限のあるプロパティ

### <a name="create-time-properties"></a>作成時のプロパティ
一部のプロパティは、スケール セットの作成時にのみ設定できます。 これには次のようなプロパティがあります。

- 可用性ゾーン
- イメージ参照発行元
- イメージ参照プラン
- 管理対象 OS ディスク ストレージ アカウントの種類

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>現在の値に基づいてのみ変更できるプロパティ
一部のプロパティは、現在の値に基づく例外付きで変更できます。 これには次のようなプロパティがあります。

- **singlePlacementGroup** - singlePlacementGroup が true の場合は、false に変更できます。 ただし、singlePlacementGroup が false の場合は、true に変更 **できません**。
- **サブネット** - 元のサブネットと新しいサブネットが同じ仮想ネットワークにある限り、スケール セットのサブネットは変更できます。
- **imageReferenceSku** - イメージ参照 SKU は、動作保証済み [Linux ディストリビューション](../virtual-machines/linux/endorsed-distros.md)、Windows サーバー/クライアント イメージ、[プラン情報](../virtual-machines/linux/cli-ps-findimage.md#view-plan-properties)のないイメージ向けに更新できます。 

### <a name="properties-that-require-deallocation-to-change"></a>変更するために割り当て解除が必要なプロパティ
一部のプロパティは、スケール セット内の VM の割り当てが解除されている場合に、特定の値にのみ変更できます。 これには次のようなプロパティがあります。

- **SKU 名** - スケール セットが現在存在するハードウェアで新しい VM SKU がサポートされていない場合は、SKU 名を変更する前にスケール セット内の VM の割り当てを解除する必要があります。 詳細については、[Azure VM のサイズを変更する方法](../virtual-machines/windows/resize-vm.md)に関するページを参照してください。 

## <a name="vm-specific-updates"></a>VM 固有の更新
特定の変更は、グローバル スケール セットのプロパティではなく特定の VM に適用できます。 現在サポートされている唯一の VM 固有の更新は、スケール セット内の VM との間でのデータ ディスクのアタッチ/デタッチです。 この機能はプレビュー段階にあります。 詳しくは、[プレビュー ドキュメント](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk)をご覧ください。


## <a name="scenarios"></a>シナリオ

### <a name="application-updates"></a>アプリケーションの更新
拡張機能を通じてアプリケーションがスケール セットにデプロイされている場合、拡張機能の構成を更新すると、アップグレード ポリシーに従ってアプリケーションが更新されます。 たとえば、カスタム スクリプト拡張機能で実行するスクリプトの新しいバージョンがある場合は、新しいスクリプトを指すように *fileUris* プロパティを更新できます。 場合によっては、拡張機能の構成が変更されていない場合でも、強制的に更新したいことがあります (たとえば、スクリプトの URI を変更せずにスクリプトを更新した場合など)。 このような場合は、強制的に更新するように *forceUpdateTag* を変更することができます。 Azure プラットフォームはこのプロパティを解釈しません。 その値を変更しても、拡張機能の動作は変わりません。 変更しても、拡張機能が強制的に再実行されるだけです。 *forceUpdateTag* について詳しくは、[拡張機能の REST API ドキュメント](/rest/api/compute/virtualmachineextensions/createorupdate)をご覧ください。 *forceUpdateTag* はカスタム スクリプト拡張機能だけでなく、すべての拡張機能で使用できることに注意してください。

カスタム イメージを使用してアプリケーションをデプロイするのも一般的です。 このシナリオについては、次のセクションで説明します。

### <a name="os-updates"></a>OS の更新
Azure プラットフォーム イメージを使用している場合は、*imageReference* を変更してイメージを更新することができます (詳しくは、[REST API のドキュメント](/rest/api/compute/virtualmachinescalesets/createorupdate)をご覧ください)。

>[!NOTE]
> プラットフォーム イメージでは、イメージ参照バージョンに "最新" を指定することが一般的です。 作成、スケール アウト、再イメージ化を行うと、VM が使用可能な最新バージョンで作成されます。 ただし、新しいイメージ バージョンがリリースされたときに OS イメージが自動的に更新されることは意味 **しません**。 OS の自動アップグレードを実現する単独の機能が、現在プレビュー段階となっています。 詳しくは、[OS アップグレードのドキュメント](virtual-machine-scale-sets-automatic-upgrade.md)をご覧ください。

カスタム イメージを使用している場合は、*imageReference* ID を更新してイメージを更新することができます (詳しくは、[REST API のドキュメント](/rest/api/compute/virtualmachinescalesets/createorupdate)をご覧ください)。

## <a name="examples"></a>例

### <a name="update-the-os-image-for-your-scale-set"></a>スケール セットの OS イメージの更新
古いバージョンの Ubuntu LTS 16.04 を実行するスケール セットがあるとします。 新しいバージョンの Ubuntu LTS 16.04 (バージョン *16.04.201801090* など) に更新する必要があります。 イメージ参照バージョン プロパティは一覧に含まれないため、次のいずれかのコマンドでこれらのプロパティを直接変更できます。

- Azure PowerShell ([Update-AzVmss](/powershell/module/az.compute/update-azvmss))

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI ([az vmss update](/cli/azure/vmss))

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

または、スケール セットで使用されるイメージを変更したい場合があります。 たとえば、スケール セットで使用されるカスタム イメージを更新または変更したいような場合です。 イメージ参照 ID プロパティを更新することにより、スケール セットで使用されるイメージを変更できます。 イメージ参照 ID プロパティは一覧に含まれないため、次のいずれかのコマンドでこのプロパティを直接変更できます。

- Azure PowerShell ([Update-AzVmss](/powershell/module/az.compute/update-azvmss))

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI ([az vmss update](/cli/azure/vmss))

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>スケール セットのロード バランサーの更新
Azure Load Balancer を使用するスケール セットがあり、Azure Load Balancer を Azure Application Gateway で置換するとします。 スケール セットのロード バランサーと Application Gateway のプロパティは一覧に含まれるため、プロパティを直接変更する代わりに一覧の要素を削除または追加するコマンドを使用できます。

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> これらのコマンドでは、スケール セットの IP 構成とロード バランサーが 1 つだけであると想定されます。 複数ある場合は、*0* 以外のリスト インデックスを使用する必要があります。


## <a name="next-steps"></a>次のステップ
スケール セットに関する一般的な管理タスクは、[Azure CLI](virtual-machine-scale-sets-manage-cli.md) または [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md) を使用して実行することもできます。