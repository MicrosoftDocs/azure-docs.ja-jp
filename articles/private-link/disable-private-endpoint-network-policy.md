---
title: Azure のプライベート エンドポイントのネットワーク ポリシーを無効にする
description: プライベート エンドポイントのネットワーク ポリシーを無効にする方法について学習します。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: a4c04033f3fb5ff523f0d80bb6c978955f4c9ec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84737514"
---
# <a name="disable-network-policies-for-private-endpoints"></a>プライベート エンドポイントのネットワーク ポリシーを無効にする

ネットワーク セキュリティ グループ (NSG) などのネットワーク ポリシーは、プライベート エンドポイントではサポートされていません。 特定のサブネットにプライベート エンドポイントをデプロイするには、そのサブネット上で明示的な無効化設定が必要です。 この設定は、プライベート エンドポイントにのみ適用されます。 サブネット内の他のリソースについては、ネットワーク セキュリティ グループ (NSG) セキュリティ規則定義に基づいてアクセスが制御されます。 
 
ポータルを使用してプライベート エンドポイントを作成する場合、この設定は作成プロセスの一環として自動的に無効になります。 他のクライアントを使用したデプロイでは、この設定を変更するための追加の手順が必要になります。 この設定は、Azure portal の Cloud Shell、Azure PowerShell のローカル インストール、Azure CLI、または Azure Resource Manager テンプレートを使用して無効にすることができます。  
 
次の例では、仮想ネットワークの `PrivateEndpointNetworkPolicies` を無効にする方法を説明します。ここでは、*myResourceGroup* という名前のリソース グループでホストされる "*既定の*" サブネットを持つ *myVirtualNetwork* という名前の仮想ネットワークを想定しています。

## <a name="using-azure-powershell"></a>Azure PowerShell の使用
ここでは、Azure PowerShell を使用して、サブネットのプライベート エンドポイント ポリシーを無効にする方法について説明します。

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Azure CLI の使用
ここでは、Azure CLI を使用して、サブネットのプライベート エンドポイント ポリシーを無効にする方法について説明します。
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>テンプレートの使用
ここでは、Azure Resource Manager テンプレートを使用して、サブネットのプライベート エンドポイント ポリシーを無効にする方法について説明します。
```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>次のステップ
- [Azure プライベート エンドポイント](private-endpoint-overview.md)について学習します
 
