---
title: Azure 仮想マシン スケール セットのネットワーク
description: Azure 仮想マシンスケールセットに対して、より高度なネットワークプロパティを構成する方法。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 06/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 9ad761f289805d15d316fc6f528a0049adb36b30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97722319"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットのネットワーク

ポータルを通じて Azure 仮想マシン スケール セットをデプロイすると、特定のネットワーク プロパティには既定値が設定されます。たとえば、受信 NAT 規則付きの Azure ロード バランサーです。 この記事では、スケール セットで構成できる、いくつかのより高度なネットワーク機能の使用方法について説明します。

この記事で説明されているすべての機能は、Azure Resource Manager テンプレートを使用して構成することができます。 一部の機能については、Azure CLI と PowerShell の例も含まれています。

## <a name="accelerated-networking"></a>高速ネットワーク
Azure 高速ネットワークでは、仮想マシンでシングルルート I/O 仮想化 (SR-IOV) が可能になることにより、ネットワークのパフォーマンスが向上します。 高速ネットワークの使用方法について詳しくは、[Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) または [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 仮想マシンの高速ネットワークに関する記事をご覧ください。 スケール セットで高速ネットワークを使用するには、スケール セットの networkInterfaceConfigurations 設定で enableAcceleratedNetworking を **true** に設定します。 次に例を示します。

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="azure-virtual-machine-scale-sets-with-azure-load-balancer"></a>Azure 仮想マシン スケール セットと Azure Load Balancer
自分のシナリオに基づき、お使いの Standard Load Balancer で Virtual Machine Scale Sets を構成する方法の詳細については、[Azure Load Balancer と Virtual Machine Scale Sets](../load-balancer/load-balancer-standard-virtual-machine-scale-sets.md) に関するページを参照してください。

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>Application Gateway を参照するスケール セットを作成する
アプリケーション ゲートウェイを使うスケール セットを作成するには、次の ARM テンプレート構成のように、スケール セットの ipConfigurations セクションにおいてアプリケーション ゲートウェイのバックエンド アドレス プールを参照します。

```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> アプリケーション ゲートウェイは、スケール セットと同じ仮想ネットワーク内の、スケール セットとは異なるサブネット内に存在する必要があることに、注意してください。


## <a name="configurable-dns-settings"></a>構成可能な DNS 設定
スケール セットは、それが作成された VNET とサブネットの特定の DNS 設定を既定で引き継ぎます。 しかし、スケール セットの DNS 設定は直接構成することができます。

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>構成可能な DNS サーバーによるスケール セットの作成
Azure CLI を使用してカスタム DNS 構成を備えたスケール セットを作成するには、 **--dns-servers** 引数とその後に続けたスペース区切りのサーバー IP アドレスを **vmss create** コマンドに追加します。 次に例を示します。

```bash
--dns-servers 10.0.0.6 10.0.0.5
```

Azure テンプレート内でカスタム DNS サーバーを構成するには、スケール セットの networkInterfaceConfigurations セクションに dnsSettings プロパティを追加します。 次に例を示します。

```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>構成可能な仮想マシン ドメイン名を備えたスケール セットの作成
CLI を使用して、仮想マシンのカスタム DNS 名が付いたスケール セットを作成するには、**virtual machine scale set create** コマンドに **--vm-domain-name** 引数を追加し、その後にドメイン名を表す文字列を追加します。

Azure テンプレート内でドメイン名を設定するには、スケール セットの **networkInterfaceConfigurations** セクションに **dnsSettings** プロパティを追加します。 次に例を示します。

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": true,
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

個々の仮想マシンの DNS 名に対する出力は、次の形式になります。

```output
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>仮想マシンごとのパブリック IPv4
一般に、Azure スケール セット仮想マシンには、独自のパブリック IP アドレスは不要です。 ほとんどのシナリオでは、パブリック IP アドレスをロード バランサーまたは個々の仮想マシン (別名、ジャンプボックス) に関連付ける方が、経済的で安全です。そうすれば、受信接続は必要に応じて (たとえば、受信 NAT 規則を通じて) スケール セット仮想マシンにルーティングされます。

ただし、一部のシナリオでは、スケール セット仮想マシンが独自のパブリック IP アドレスを備える必要があります。 たとえば、ゲームで、ゲームの物理処理を行っているクラウド仮想マシンにコンソールが直接接続する必要がある場合です。 もう 1 つの例は、分散型データベースで複数のリージョンにわたって各仮想マシンが互いに対する外部接続を確立する必要がある場合です。

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>仮想マシンごとにパブリック IP アドレスがあるスケール セットの作成
CLI を使用して、各仮想マシンにパブリック IP アドレスを割り当てるスケール セットを作成するには、**vmss create** コマンドに **--public-ip-per-vm** パラメーターを追加します。 

Azure テンプレートを使用してスケール セットを作成するには、Microsoft.Compute/virtualMachineScaleSets リソースの API バージョンが少なくとも **2017-03-30** であることを確認し、スケール セットの ipConfigurations セクションに **publicIpAddressConfiguration** JSON プロパティを追加します。 次に例を示します。

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```

テンプレートの例:[201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>スケール セットに含まれた仮想マシンのパブリック IP アドレスの照会
CLI を使用して、スケール セット仮想マシンに割り当てられているパブリック IP アドレスを一覧表示するには、**az vmss list-instance-public-ips** コマンドを使用します。

PowerShell を使用して、スケール セットのパブリック IP アドレスを一覧表示するには、_Get-AzPublicIpAddress_ コマンドを使用します。 次に例を示します。

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

また、パブリック IP アドレス構成のリソース ID を直接参照して、パブリック IP アドレスを照会することもできます。 次に例を示します。

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

[Azure Resource Explorer](https://resources.azure.com) またはバージョン **2017-03-30** 以上の Azure REST API でクエリを実行して、スケール セット仮想マシンに割り当てられているパブリック IP アドレスを表示することもできます。

[Azure Resource Explorer](https://resources.azure.com) でクエリを実行するには:

1. Web ブラウザーで [Azure Resource Explorer](https://resources.azure.com) を開きます。
1. 左側の *subscriptions* を、その横にある *+* をクリックして展開します。 *subscriptions* に項目が 1 つしかない場合は、既に展開されている可能性があります。
1. ご自分のサブスクリプションを展開します。
1. ご自分のリソース グループを展開します。
1. *providers* を展開します。
1. *Microsoft.Compute* を展開します。
1. *virtualMachineScaleSets* を展開します。
1. ご自分のスケール セットを展開します。
1. *publicipaddresses* をクリックします。

Azure REST API でクエリを実行するには:

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

[Azure Resource Explorer](https://resources.azure.com) と Azure REST API からの出力の例:

```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>NIC ごとの複数の IP アドレス
スケール セット内の VM に接続されたすべての NIC には、1 つ以上の IP 構成を関連付けることができます。 各構成には、1 つのプライベート IP アドレスが割り当てられます。 また、1 つのパブリック IP アドレス リソースが関連付けられる場合もあります。 いくつの IP アドレスを NIC に割り当てることができるかと、Azure サブスクリプションでいくつのパブリック IP アドレスを使用できるかを理解するには、[Azure の制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関するページを参照してください。

## <a name="multiple-nics-per-virtual-machine"></a>仮想マシンごとの複数の NIC
マシンのサイズに応じて、仮想マシンごとに最大 8 個の NIC を使用することができます。 マシンごとの NIC の最大数については、[VM サイズの記事](../virtual-machines/sizes.md)を参照してください。 VM インスタンスに接続されているすべての NIC は、同じ仮想ネットワークに接続する必要があります。 NIC は異なるサブネットに接続できますが、すべてのサブネットは同じ仮想ネットワークに属している必要があります。

次の例のスケール セット ネットワーク プロファイルは、複数の NIC エントリと、仮想マシンごとの複数のパブリック IP を示しています。

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": true,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": false,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg--asgs-per-scale-set"></a>スケール セットごとの NSG と ASG
[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)では、セキュリティ規則を使用して、Azure 仮想ネットワーク内の Azure リソースとの間でやり取りされるトラフィックをフィルター処理できます。 [アプリケーション セキュリティ グループ](../virtual-network/network-security-groups-overview.md#application-security-groups)を使用すると、Azure リソースのネットワーク セキュリティを処理し、Azure リソースをアプリケーションの構造の拡張機能としてグループ化することができます。

ネットワーク セキュリティ グループは、スケール セットに直接適用できます。そのためには、スケール セット仮想マシン プロパティのネットワーク インターフェイス構成セクションに参照を追加します。

アプリケーション セキュリティ グループもスケール セットに直接指定できます。そのためには、スケール セット仮想マシン プロパティのネットワーク インターフェイス IP 構成セクションに参照を追加します。

次に例を示します。

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": true,
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

ネットワーク セキュリティ グループがスケール セットに関連付けられていることを確認するには、`az vmss show` コマンドを使用します。 次の例では、結果をフィルター処理して出力の関連セクションのみを表示するために、`--query` が使用されています。

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

アプリケーション セキュリティ グループがスケール セットに関連付けられていることを確認するには、`az vmss show` コマンドを使用します。 次の例では、結果をフィルター処理して出力の関連セクションのみを表示するために、`--query` が使用されています。

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```

## <a name="make-networking-updates-to-specific-instances"></a>特定のインスタンスにネットワークを更新する

特定の仮想マシン スケール セットのインスタンスにネットワークを更新できます。 

インスタンスに対して `PUT` を実行し、ネットワーク構成を更新できます。 これを利用し、たとえば、ネットワーク インターフェイス カード (NIC) を追加または削除したり、バックエンド プールからインスタンスを削除したりできます。

```
PUT https://management.azure.com/subscriptions/.../resourceGroups/vmssnic/providers/Microsoft.Compute/virtualMachineScaleSets/vmssnic/virtualMachines/1/?api-version=2019-07-01
```

次の例では、NIC に 2 つ目の IP 構成を追加する方法を示します。

1. `GET` 特定の仮想マシン スケール セットのインスタンスの詳細。
    
    ``` 
    GET https://management.azure.com/subscriptions/.../resourceGroups/vmssnic/providers/Microsoft.Compute/virtualMachineScaleSets/vmssnic/virtualMachines/1/?api-version=2019-07-01
    ```

    *次は、この例のためにネットワーク パラメーターのみを表示するため、簡略化されています。*

    ```json
    {
      ...
      "properties": {
        ...
        "networkProfileConfiguration": {
          "networkInterfaceConfigurations": [
            {
              "name": "vmssnic-vnet-nic01",
              "properties": {
                "primary": true,
                "enableAcceleratedNetworking": false,
                "networkSecurityGroup": {
                  "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/networkSecurityGroups/basicNsgvmssnic-vnet-nic01"
                },
                "dnsSettings": {
                  "dnsServers": []
                },
                "enableIPForwarding": false,
                "ipConfigurations": [
                  {
                    "name": "vmssnic-vnet-nic01-defaultIpConfiguration",
                    "properties": {
                      "publicIPAddressConfiguration": {
                        "name": "publicIp-vmssnic-vnet-nic01",
                        "properties": {
                          "idleTimeoutInMinutes": 15,
                          "ipTags": [],
                          "publicIPAddressVersion": "IPv4"
                        }
                      },
                      "primary": true,
                      "subnet": {
                        "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/virtualNetworks/vmssnic-vnet/subnets/default"
                      },
                      "privateIPAddressVersion": "IPv4"
                    }
                  }
                ]
              }
            }
          ]
        },
        ...
      }
    }
    ```
 
2. `PUT` インスタンスに対して。更新して IP 構成を追加します。 これは `networkInterfaceConfiguration` を追加する場合と似ています。

    
    ```
    PUT https://management.azure.com/subscriptions/.../resourceGroups/vmssnic/providers/Microsoft.Compute/virtualMachineScaleSets/vmssnic/virtualMachines/1/?api-version=2019-07-01
    ```

    *次は、この例のためにネットワーク パラメーターのみを表示するため、簡略化されています。*

    ```json
      {
      ...
      "properties": {
        ...
        "networkProfileConfiguration": {
          "networkInterfaceConfigurations": [
            {
              "name": "vmssnic-vnet-nic01",
              "properties": {
                "primary": true,
                "enableAcceleratedNetworking": false,
                "networkSecurityGroup": {
                  "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/networkSecurityGroups/basicNsgvmssnic-vnet-nic01"
                },
                "dnsSettings": {
                  "dnsServers": []
                },
                "enableIPForwarding": false,
                "ipConfigurations": [
                  {
                    "name": "vmssnic-vnet-nic01-defaultIpConfiguration",
                    "properties": {
                      "publicIPAddressConfiguration": {
                        "name": "publicIp-vmssnic-vnet-nic01",
                        "properties": {
                          "idleTimeoutInMinutes": 15,
                          "ipTags": [],
                          "publicIPAddressVersion": "IPv4"
                        }
                      },
                      "primary": true,
                      "subnet": {
                        "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/virtualNetworks/vmssnic-vnet/subnets/default"
                      },
                      "privateIPAddressVersion": "IPv4"
                    }
                  },
                  {
                    "name": "my-second-config",
                    "properties": {
                      "subnet": {
                        "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/virtualNetworks/vmssnic-vnet/subnets/default"
                      },
                      "privateIPAddressVersion": "IPv4"
                    }
                  }
                ]
              }
            }
          ]
        },
        ...
      }
    }
    ```



## <a name="next-steps"></a>次のステップ
Azure 仮想ネットワークの詳細については、「[Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)」を参照してください。
