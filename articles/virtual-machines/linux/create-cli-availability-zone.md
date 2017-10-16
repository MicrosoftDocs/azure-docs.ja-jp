---
title: "Azure CLI を使用したゾーン Linux VM の作成 | Microsoft Docs"
description: "Azure CLI を使用して可用性ゾーン内に Linux VM を作成する"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: 232c2cf1ba0a7de23da10357de9a6e6ad9a0d41d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Azure CLI を使用して可用性ゾーン内に Linux 仮想マシンを作成する

この記事では、Azure CLI を使用して Azure 可用性ゾーン内に Linux VM を作成する手順を説明します。 [可用性ゾーン](../../availability-zones/az-overview.md)とは、1 つの Azure リージョン内で物理的に分離されたゾーンのことです。 可用性ゾーンは、データセンター全体に及ぶ珍しい障害や損失からアプリとデータを保護するために使用します。

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]

[Azure CLI 2.0](/cli/azure/install-az-cli2) の最新版がインストールされていること、および [az login](/cli/azure/#login) で Azure アカウントにログインしていることを確認します。

## <a name="create-resource-group"></a>Create resource group

[az group create](/cli/azure/group#az_group_create) コマンドでリソース グループを作成します。  

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシンの前にリソース グループを作成する必要があります。 この例では、*myResourceGroupVM* という名前のリソース グループが *eastus2* リージョンに作成されます。 米国東部 2 は、可用性ゾーン (プレビュー段階) をサポートする Azure リージョンの 1 つです。

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus2
```

この記事全体で示しているように、VM の作成時または変更時にリソース グループを指定します。

## <a name="create-virtual-machine"></a>仮想マシンの作成

仮想マシンを作成するには、[az vm create](/cli/azure/vm#az_vm_create) コマンドを使用します。 

仮想マシンを作成するときに、オペレーティング システム イメージ、ディスクのサイズ、管理者資格情報など、いくつかの選択肢があります。 この例では、Ubuntu Server を実行する *myVM* という名前の仮想マシンを作成します。 VM は、可用性ゾーン *1* に作成されます。 既定では、VM は、*Standard_DS1_v2* サイズで作成されます。 このサイズは、可用性ゾーン (プレビュー) でサポートされています。

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys --zone 1
```

VM の作成には数分かかることがあります。 VM が作成されると、Azure CLI で VM に関する以下の情報が出力されます。 `zones` の値を書き留めておきます。この値は、VM が実行されている可用性ゾーンを表します。 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="zone-for-ip-address-and-managed-disk"></a>IP アドレスおよび管理ディスクのゾーン

VM が可用性ゾーン内にデプロイされると、IP アドレスと管理ディスク リソースが同じ可用性ゾーン内にデプロイされます。 次の例では、これらのリソースについての情報を取得します。

まず [az vm list-ip-addresses](/cli/azure/vm#az_vm_list_ip_addresses) コマンドを使用して、*myVM* 内のパブリック IP アドレス リソースの名前を返します。 この例では名前を変数に格納し、後の手順で使用します。

```azurecli-interactive
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

これで、IP アドレスに関する情報を取得できます。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

この出力は、IP アドレスが VM と同じ可用性ゾーン内にあることを示しています。

```azurecli-interactive
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
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

同様に、VM の管理ディスクがこの可用性ゾーン内にあることを確認します。 ディスク ID を取得するには、[az vm show](/cli/azure/vm#az_vm_show) コマンドを使用します。この例ではディスク ID を変数に格納し、後の手順で使用します。 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
これで、管理ディスクに関する情報を取得できます。

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

この出力は、管理ディスクが VM と同じ可用性ゾーン内にあることを示しています。

```azurecli-interactive
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/Subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2017-09-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```
 


## <a name="next-steps"></a>次のステップ

この記事では、可用性ゾーン内に VM を作成する方法を説明しました。 Azure VM の[リージョンと可用性](regions-and-availability.md)の詳細を確認してください。




