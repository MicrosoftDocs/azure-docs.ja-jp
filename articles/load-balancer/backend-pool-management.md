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
ms.openlocfilehash: 81fad1c77b917c1e3eaf7ddd200c3fea83cb0e0a
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589675"
---
# <a name="backend-pool-management"></a>バックエンド プールの管理
バックエンド プールは、ロード バランサーの重要なコンポーネントです。 バックエンド プールは、指定された負荷分散規則のトラフィックを処理するリソースのグループを定義します。

バックエンド プールを構成するには、次の 2 つの方法があります。
* ネットワーク インターフェイス カード (NIC)
* IP アドレスと Virtual Network (VNET) のリソース ID の組み合わせ

既存の仮想マシンと仮想マシン スケール セットを使用する場合は、NIC によってバックエンド プールを構成します。 この方法では、リソースとバックエンド プールの間に最も直接的なリンクが構築されます。 

後で仮想マシンと仮想マシン スケール セットを作成する予定の IP アドレス範囲を使用してバックエンド プールを事前に割り当てる場合は、IP アドレスと VNET ID の組み合わせによってバックエンド プールを構成します。

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

$backendPool = 
New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
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
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName 
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
--resourceGroup myResourceGroup \
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
  >この機能は現在プレビュー段階であり、次の制限事項があります。
  >* 標準のロード バランサーのみ
  >* バックエンド プール内の IP アドレスの上限は 100 個
  >* バックエンド リソースは、ロード バランサーと同じ仮想ネットワークに存在する必要がある
  >* この機能は Azure portal では現在サポートされていない
  >* この機能では、ACI コンテナーは現在サポートされていない
  >* ロード バランサーまたはロード バランサーに面するサービスは、ロード バランサーのバックエンド プールに配置できない
  
### <a name="powershell"></a>PowerShell
新しいバックエンド プールを作成します。

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$vnetName = "myVnet"
$location = "eastus"
$nicName = "myNic"

$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName  
```

既存の仮想ネットワークから新しい IP を使用してバックエンド プールを更新します。
 
```azurepowershell-interactive
$virtualNetwork = 
Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -InputObject $backendPool
```

ロード バランサーのバックエンド プール情報を取得して、そのバックエンド アドレスがバックエンド プールに追加されていることを確認します。

```azurepowershell-interactive
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName 
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

### <a name="resource-manager-template"></a>Resource Manager テンプレート
ロード バランサーとバックエンド プールを作成し、バックエンド プールにバックエンド アドレスを設定します。
```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_myLB_location": {
            "type": "SecureString"
        },
        "loadBalancers_myLB_location_1": {
            "type": "SecureString"
        },
        "backendAddressPools_myBackendPool_location": {
            "type": "SecureString"
        },
        "backendAddressPools_myBackendPool_location_1": {
            "type": "SecureString"
        },
        "loadBalancers_myLB_name": {
            "defaultValue": "myLB",
            "type": "String"
        },
        "virtualNetworks_myVNET_externalid": {
            "defaultValue": "/subscriptions/6bb4a28a-db84-4e8a-b1dc-fabf7bd9f0b2/resourceGroups/ErRobin4/providers/Microsoft.Network/virtualNetworks/myVNET",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2020-04-01",
            "name": "[parameters('loadBalancers_myLB_name')]",
            "location": "eastus",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAddress": "10.0.0.7",
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[concat(parameters('virtualNetworks_myVNET_externalid'), '/subnets/Subnet-1')]"
                            },
                            "privateIPAddressVersion": "IPv4"
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "myBackendPool",
                        "properties": {
                            "loadBalancerBackendAddresses": [
                                {
                                    "name": "addr1",
                                    "properties": {
                                        "ipAddress": "10.0.0.4",
                                        "virtualNetwork": {
                                            "location": "[parameters('loadBalancers_myLB_location')]"
                                        }
                                    }
                                },
                                {
                                    "name": "addr2",
                                    "properties": {
                                        "ipAddress": "10.0.0.5",
                                        "virtualNetwork": {
                                            "location": "[parameters('loadBalancers_myLB_location_1')]"
                                        }
                                    }
                                }
                            ]
                        }
                    }
                ],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundRules": [],
                "inboundNatPools": []
            }
        },
        {
            "type": "Microsoft.Network/loadBalancers/backendAddressPools",
            "apiVersion": "2020-04-01",
            "name": "[concat(parameters('loadBalancers_myLB_name'), '/myBackendPool')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLB_name'))]"
            ],
            "properties": {
                "loadBalancerBackendAddresses": [
                    {
                        "name": "addr1",
                        "properties": {
                            "ipAddress": "10.0.0.4",
                            "virtualNetwork": {
                                "location": "[parameters('backendAddressPools_myBackendPool_location')]"
                            }
                        }
                    },
                    {
                        "name": "addr2",
                        "properties": {
                            "ipAddress": "10.0.0.5",
                            "virtualNetwork": {
                                "location": "[parameters('backendAddressPools_myBackendPool_location_1')]"
                            }
                        }
                    }
                ]
            }
        }
    ]
}
```

仮想マシンとアタッチされたネットワーク インターフェイスを作成します。 ネットワーク インターフェイスの IP アドレスをバックエンド アドレスの 1 つに設定します。
```
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "String",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "storageAccountDomain": {
      "type": "String",
      "metadata": {
        "description": "The domain of the storage account to be created."
      }
    },
    "adminUsername": {
      "type": "String",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "SecureString",
      "metadata": {
        "description": "Admin password"
      }
    },
    "vmName": {
      "defaultValue": "myVM",
      "type": "String",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "String",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "defaultValue": "WindowsServer",
      "type": "String",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "defaultValue": "2012-R2-Datacenter",
      "type": "String",
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "defaultValue": "myLB",
      "type": "String",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicName": {
      "defaultValue": "nic",
      "type": "String",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "privateIpAddress": {
      "defaultValue": "10.0.0.5",
      "type": "String",
      "metadata": {
        "description": "Private IP Address of the VM"
      }
    },
    "vnetName": {
      "defaultValue": "myVNET",
      "type": "String",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "defaultValue": "Standard_A1",
      "type": "String",
      "metadata": {
        "description": "Size of the VM"
      }
    },
    "storageLocation": {
      "type": "String",
      "metadata": {
        "description": "Location of the Storage Account."
      }
    },
    "location": {
      "type": "String",
      "metadata": {
        "description": "Location to deploy all the resources in."
      }
    }
  },
  "variables": {
    "networkSecurityGroupName": "networkSecurityGroup1",
    "storageAccountType": "Standard_LRS",
    "subnetName": "Subnet-1",
    "publicIPAddressType": "Static",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('storageLocation')]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2016-03-30",
      "name": "[variables('networkSecurityGroupName')]",
      "location": "[parameters('location')]",
      "properties": {
        "securityRules": []
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Static",
              "privateIpAddress": "[parameters('privateIpAddress')]",
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('vmName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
        "[parameters('nicName')]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[parameters('imagePublisher')]",
            "offer": "[parameters('imageOffer')]",
            "sku": "[parameters('imageSKU')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',parameters('storageAccountName'),'.blob.',parameters('storageAccountDomain'),'/vhds/','osdisk', '.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
            }
          ]
        }
      }
    }
  ]
}
```
## <a name="next-steps"></a>次のステップ
この記事では、Azure Load Balancer のバックエンド プール管理についてと、IP アドレスと仮想ネットワークを使用してバックエンド プールを構成する方法について学習しました。

[Azure Load Balancer](load-balancer-overview.md) についてさらに詳しく学習する。
