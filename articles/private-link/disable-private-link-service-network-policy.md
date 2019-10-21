---
title: 'Azure Private Link サービスのソース IP アドレスのネットワーク ポリシーを無効にする '
description: Azure Private Link のネットワーク ポリシーを無効にする方法について学習します
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: b2003b9c7520cfa3e82576fd3430063c20d452ff
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104575"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Private Link サービスのソース IP のネットワーク ポリシーを無効にする

自分の Private Link サービスのソース IP アドレスを選択するには、そのサブネット上で明示的な無効化設定 `privateLinkServiceNetworkPolicies` が必要です。 この設定は、Private Link サービスのソース IP として選択した特定のプライベート IP アドレスにのみ適用されます。 サブネット内の他のリソースについては、ネットワーク セキュリティ グループ (NSG) セキュリティ規則定義に基づいてアクセスが制御されます。 
 
任意の Azure クライアント (PowerShell、CLI、またはテンプレート) を使用する場合は、このプロパティを変更するための追加の手順が必要になります。 このポリシーは、Azure portal の Cloud Shell、Azure PowerShell のローカル インストール、Azure CLI、または Azure Resource Manager テンプレートを使用して無効にすることができます。  
 
次の手順に従って、仮想ネットワークのプライベート リンク サービス ネットワーク ポリシーを無効にします。ここでは、*myResourceGroup* という名前のリソース グループでホストされる "*既定の*" サブネットを持つ *myVirtualNetwork* という名前の仮想ネットワークを想定しています。 

## <a name="using-azure-powershell"></a>Azure PowerShell の使用
ここでは、Azure PowerShell を使用して、サブネットのプライベート エンドポイント ポリシーを無効にする方法について説明します。

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork ` 
  | Select -ExpandProperty subnets ` 
  | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Azure CLI の使用
ここでは、Azure CLI を使用して、サブネットのプライベート エンドポイント ポリシーを無効にする方法について説明します。
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
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
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>次の手順
- [Azure プライベート エンドポイント](private-endpoint-overview.md)について学習します
 
