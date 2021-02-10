---
title: バックエンド プールの管理
titleSuffix: Azure Load Balancer
description: Azure Load Balancer のバックエンド プールを構成および管理する方法の学習を開始する
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 07/07/2020
ms.author: allensu
ms.openlocfilehash: e5efbf695b85f474e5d7c84c86809acb2f5a1035
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429604"
---
# <a name="backend-pool-management"></a>バックエンド プールの管理
バックエンド プールは、ロード バランサーの重要なコンポーネントです。 バックエンド プールは、指定された負荷分散規則のトラフィックを処理するリソースのグループを定義します。

バックエンド プールを構成するには、次の 2 つの方法があります。
* ネットワーク インターフェイス カード (NIC)
* IP アドレスと Virtual Network (VNET) のリソース ID の組み合わせ

既存の仮想マシンと仮想マシン スケール セットを使用する場合は、NIC によってバックエンド プールを構成します。 この方法では、リソースとバックエンド プールの間に最も直接的なリンクが構築されます。 

後で仮想マシンと仮想マシン スケール セットを作成する予定の IP アドレス範囲を使用してバックエンド プールを事前に割り当てる場合は、IP アドレスと VNET ID の組み合わせによってバックエンド プールを構成します。

同じロード バランサーに対して IP ベースおよび NIC ベースのバックエンド プールを構成することはできますが、NIC の対象となる背景のアドレスと IP アドレスとが同じプール内に混在する単一のバックエンド プールを作成することはできません。

この記事の構成セクションでは、次のことに焦点を当てます。

* Azure PowerShell
* Azure CLI
* REST API
* Azure Resource Manager のテンプレート 

これらのセクションでは、各構成オプションについてバックエンド プールがどのように構造化されているかについて詳しく説明します。

## <a name="configuring-backend-pool-by-nic"></a>NIC によるバックエンド プールの構成
バックエンド プールは、ロード バランサー操作の一部として作成されます。 バックエンド プールのメンバーを追加するには、NIC の IP 構成プロパティを使用します。

次の例では、バックエンド プールの作成と設定の操作に焦点を当て、このワークフローと関係を強調表示しています。

  >[!NOTE] 
  >ネットワーク インターフェイスを使用して構成されたバックエンド プールは、バックエンド プールでの操作の一部として更新できないことに注意してください。 バックエンド リソースの追加または削除は、リソースのネットワーク インターフェイス上で行う必要があります。

### <a name="powershell"></a>PowerShell
新しいバックエンド プールを作成します。
 
```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$backendPool = 
New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

新しいネットワーク インターフェイスを作成して、バックエンド プールに追加します。

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$nicname = "myNic"
$location = "eastus"
$vnetname = <your-vnet-name>

$vnet = 
Get-AzVirtualNetwork -Name $vnetname -ResourceGroupName $resourceGroup

$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicname -LoadBalancerBackendAddressPool $backendPoolName -Subnet $vnet.Subnets[0]
```

ロード バランサーのバックエンド プール情報を取得して、このネットワーク インターフェイスがバックエンド プールに追加されていることを確認します。

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$lb =
Get-AzLoadBalancer -ResourceGroupName $res
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName 
```

新しい仮想マシンを作成し、ネットワーク インターフェイスをアタッチして、バックエンド プールに配置します。

```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
バックエンド プールを作成します。

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool 
```

新しいネットワーク インターフェイスを作成して、バックエンド プールに追加します。

```azurecli-interactive
az network nic create \
--resource-group myResourceGroup \
--name myNic \
--vnet-name myVnet \
--subnet mySubnet \
--network-security-group myNetworkSecurityGroup \
--lb-name myLB \
--lb-address-pools myBackEndPool
```

バックエンド プールを取得して、IP アドレスが正しく追加されたことを確認します。

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name myLb \
--name myBackendPool
```

新しい仮想マシンを作成し、ネットワーク インターフェイスをアタッチして、バックエンド プールに配置します。

```azurecli-interactive
az vm create \
--resource-group myResourceGroup \
--name myVM \
--nics myNic \
--image UbuntuLTS \
--admin-username azureuser \
--generate-ssh-keys
```

### <a name="rest-api"></a>REST API
バックエンド プールを作成します。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

ネットワーク インターフェイスを作成し、ネットワーク インターフェイスの IP 構成プロパティを使用して作成したバックエンド プールに追加します。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

JSON 要求本文:
```json
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          },
          "loadBalancerBackendAddressPools": {
                                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

ロード バランサーのバックエンド プール情報を取得して、このネットワーク インターフェイスがバックエンド プールに追加されていることを確認します。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name/providers/Microsoft.Network/loadBalancers/{load-balancer-name/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

VM を作成し、バックエンド プールを参照する NIC をアタッチします。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

JSON 要求本文:
```JSON
{
  "location": "easttus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```

### <a name="resource-manager-template"></a>Resource Manager テンプレート
この[クイックスタート Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/)に従って、ロード バランサーと仮想マシンをデプロイし、ネットワーク インターフェイスを使用して仮想マシンをバックエンド プールに追加します。

## <a name="configure-backend-pool-by-ip-address-and-virtual-network"></a>IP アドレスと仮想ネットワークを使用してバックエンド プールを構成する
事前に設定されたバックエンド プールのシナリオでは、IP と仮想ネットワークを使用します。

バックエンド プールのすべての管理は、次の例で強調されているように、バックエンド プール オブジェクトで直接実行されます。

  >[!IMPORTANT] 
  >現在、この機能はプレビュー段階にあります。 この機能の現在の制限については、「[制限事項](#limitations)」を参照してください。

### <a name="powershell"></a>PowerShell
新しいバックエンド プールを作成します。

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$vnetName = "myVnet"
$location = "eastus"
$nicName = "myNic"

$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName  
```

既存の仮想ネットワークから新しい IP を使用してバックエンド プールを更新します。
 
```azurepowershell-interactive
$virtualNetwork = 
Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -InputObject $backendPool
```

ロード バランサーのバックエンド プール情報を取得して、そのバックエンド アドレスがバックエンド プールに追加されていることを確認します。

```azurepowershell-interactive
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName 
```
ネットワーク インターフェイスを作成して、バックエンド プールに追加します。 IP アドレスをバックエンド アドレスの 1 つに設定します。

```azurepowershell-interactive
$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicName -PrivateIpAddress 10.0.0.4 -Subnet $virtualNetwork.Subnets[0]
```

VM を作成し、バックエンド プールの IP アドレスで NIC をアタッチします。
```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
CLI を使用すると、コマンドライン パラメーターを使用するか、JSON 構成ファイルを使用して、バックエンド プールを設定できます。 

コマンドライン パラメーターを使用して、バックエンド プールを作成して設定します。

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address name=addr1 ip-address=10.0.0.4 \
--backend-address name=addr2 ip-address=10.0.0.5
```

JSON 構成ファイルを使用して、バックエンド プールを作成して設定します。

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address-config-file @config_file.json
```

JSON 構成ファイル:
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

ロード バランサーのバックエンド プール情報を取得して、そのバックエンド アドレスがバックエンド プールに追加されていることを確認します。

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name MyLb \
--name MyBackendPool
```

ネットワーク インターフェイスを作成して、バックエンド プールに追加します。 IP アドレスをバックエンド アドレスの 1 つに設定します。

```azurecli-interactive
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

VM を作成し、バックエンド プールの IP アドレスで NIC をアタッチします。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

### <a name="rest-api"></a>REST API

PUT バックエンド プール要求を使用してバックエンド プールを作成し、バックエンド アドレスを定義します。 次のいずれかを使用して PUT 要求の JSON 本文にバックエンド アドレスを構成します。

* アドレス名
* IP アドレス
* 仮想ネットワーク ID 

```
PUT https://management.azure.com/subscriptions/subid/resourceGroups/testrg/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/backend?api-version=2020-05-01
```

JSON 要求本文:
```JSON
{
  "properties": {
    "loadBalancerBackendAddresses": [
      {
        "name": "address1",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.4"
        }
      },
      {
        "name": "address2",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.5"
        }
      }
    ]
  }
}
```

ロード バランサーのバックエンド プール情報を取得して、そのバックエンド アドレスがバックエンド プールに追加されていることを確認します。
```
GET https://management.azure.com/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

ネットワーク インターフェイスを作成して、バックエンド プールに追加します。 IP アドレスをバックエンド アドレスの 1 つに設定します。
```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

JSON 要求本文:
```JSON
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "privateIPAddress": "10.0.0.4",
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

VM を作成し、バックエンド プールの IP アドレスで NIC をアタッチします。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

JSON 要求本文:
```JSON
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```

## <a name="limitations"></a>制限事項
IP アドレスで構成されたバックエンド プールには、次の制限があります。
  * 標準のロード バランサーのみ
  * バックエンド プール内の IP アドレスの上限は 100 個
  * バックエンド リソースは、ロード バランサーと同じ仮想ネットワークに存在する必要がある
  * IP ベースのバックエンド プールを使用するロード バランサーは、Private Link サービスとして機能することはできない
  * この機能は Azure portal では現在サポートされていない
  * この機能では、ACI コンテナーは現在サポートされていない
  * ロード バランサーまたはロード バランサーに面するサービスは、ロード バランサーのバックエンド プールに配置できない
  * インバウンド NAT 規則を IP アドレスで指定することはできない
  
## <a name="next-steps"></a>次のステップ
この記事では、Azure Load Balancer のバックエンド プール管理についてと、IP アドレスと仮想ネットワークを使用してバックエンド プールを構成する方法について学習しました。

[Azure Load Balancer](load-balancer-overview.md) についてさらに詳しく学習する。
