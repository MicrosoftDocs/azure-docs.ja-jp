---
title: Azure CLI を使用してゾーン VM を作成する
description: Azure CLI を使用して可用性ゾーン内に仮想マシンを作成する
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 04/05/2018
ms.author: cynthn
ms.openlocfilehash: 3405468c64a7130bb0d71543403dd4ce5ef9c241
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558374"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-azure-cli"></a>Azure CLI を使用して可用性ゾーン内に仮想マシンを作成する

この記事では、Azure CLI を使用して Azure 可用性ゾーン内に Linux VM を作成する手順を説明します。 [可用性ゾーン](../../availability-zones/az-overview.md)とは、1 つの Azure リージョン内で物理的に分離されたゾーンのことです。 可用性ゾーンは、データセンター全体に及ぶ珍しい障害や損失からアプリとデータを保護するために使用します。

可用性ゾーンを使用するには、[サポートされている Azure リージョン](../../availability-zones/az-region.md)に仮想マシンを作成します。

[Azure CLI](/cli/azure/install-az-cli2) の最新版がインストールされていること、および [az login](/cli/azure/reference-index) で Azure アカウントにログインしていることを確認します。


## <a name="check-vm-sku-availability"></a>提供されている VM SKU の確認
提供されている VM サイズ (SKU) は、リージョンやゾーンによって異なる場合があります。 可用性ゾーンの使用計画を立てやすくするために、提供されている VM SKU を Azure リージョンとゾーンごとに一覧表示することができます。 そうすることで適切な VM サイズを選び、必要な回復性をすべてのゾーンにわたって確保することができます。 さまざまな VM の種類とサイズについて詳しくは、[VM サイズの概要](../sizes.md)に関するページを参照してください。

[az vm list-skus](/cli/azure/vm) コマンドで、利用可能な VM SKU を表示することができます。 次の例では、*eastus2* リージョンで提供されている VM SKU を一覧表示しています。

```azurecli
az vm list-skus --location eastus2 --output table
```

次に示したのは、その出力例の抜粋です。これは各 VM サイズが提供されている可用性ゾーンを確認できます。

```output
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。  

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシンの前にリソース グループを作成する必要があります。 この例では、*myResourceGroupVM* という名前のリソース グループが *eastus2* リージョンに作成されます。 米国東部 2 は、可用性ゾーンをサポートする Azure リージョンの 1 つです。

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

この記事全体で示しているように、VM の作成時または変更時にリソース グループを指定します。

## <a name="create-virtual-machine"></a>仮想マシンの作成

仮想マシンを作成するには、[az vm create](/cli/azure/vm) コマンドを使用します。 

仮想マシンを作成するときに、オペレーティング システム イメージ、ディスクのサイズ、管理者資格情報など、いくつかの選択肢があります。 この例では、Ubuntu Server を実行する *myVM* という名前の仮想マシンを作成します。 VM は、可用性ゾーン *1* に作成されます。 既定では、VM は、*Standard_DS1_v2* サイズで作成されます。

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

VM の作成には数分かかることがあります。 VM が作成されると、Azure CLI で VM に関する以下の情報が出力されます。 `zones` の値を書き留めておきます。この値は、VM が実行されている可用性ゾーンを表します。 

```output
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>マネージド ディスクのゾーンと IP アドレスを確認する

VM が可用性ゾーンに展開されると、その VM のマネージド ディスクが同じ可用性ゾーンに作成されます。 既定では、パブリック IP アドレスもそのゾーンに作成されます。 次の例では、これらのリソースについての情報を取得します。

VM のマネージド ディスクが可用性ゾーンにあることを確認するには、[az vm show](/cli/azure/vm) コマンドを使ってディスク ID を取得します。 この例では、ディスク ID を変数に格納し、後の手順で使用します。 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
これで、マネージド ディスクに関する情報を取得できます。

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

この出力は、マネージド ディスクが VM と同じ可用性ゾーン内にあることを示しています。

```output
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

[az vm list-ip-addresses](/cli/azure/vm) コマンドを使って、*myVM* 内のパブリック IP アドレス リソースの名前を取得します。 この例では名前を変数に格納し、後の手順で使用します。

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

これで、IP アドレスに関する情報を取得できます。

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

この出力は、IP アドレスが VM と同じ可用性ゾーン内にあることを示しています。

```output
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>次のステップ

この記事では、可用性ゾーン内に VM を作成する方法を説明しました。 Azure VM の[可用性](../availability.md)の詳細を確認してください。