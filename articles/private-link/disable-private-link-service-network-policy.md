---
title: 'Azure Private Link サービスのソース IP アドレスのネットワーク ポリシーを無効にする '
description: Azure Private Link のネットワーク ポリシーを無効にする方法について学習します
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7528d12dcbfba09b4dae4ac07a5c683a7c11bd88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99548802"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Private Link サービスのソース IP のネットワーク ポリシーを無効にする

自分の Private Link サービスのソース IP アドレスを選択するには、そのサブネット上で明示的な無効化設定 `privateLinkServiceNetworkPolicies` が必要です。 この設定は、Private Link サービスのソース IP として選択した特定のプライベート IP アドレスにのみ適用されます。 サブネット内の他のリソースについては、ネットワーク セキュリティ グループ (NSG) セキュリティ規則定義に基づいてアクセスが制御されます。 
 
ポータルを使用して Private Link サービスを作成する場合、この設定は作成プロセスの一環として自動的に無効になります。 Azure クライアント (PowerShell、CLI、またはテンプレート) を使用したデプロイでは、このプロパティを変更するための追加の手順が必要になります。 このポリシーは、Azure portal の Cloud Shell、Azure PowerShell のローカル インストール、Azure CLI、または Azure Resource Manager テンプレートを使用して無効にすることができます。  
 
次の手順に従って、仮想ネットワークのプライベート リンク サービス ネットワーク ポリシーを無効にします。ここでは、*myResourceGroup* という名前のリソース グループでホストされる "*既定の*" サブネットを持つ *myVirtualNetwork* という名前の仮想ネットワークを想定しています。 

## <a name="using-azure-powershell"></a>Azure PowerShell の使用
ここでは、Azure PowerShell を使用して、サブネットのプライベート エンドポイント ポリシーを無効にする方法について説明します。
コード内で、"default" を仮想サブネットの名前で置換します。

```azurepowershell
$virtualSubnetName = "default"
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $virtualSubnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
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
## <a name="next-steps"></a>次のステップ
- [Azure プライベート エンドポイント](private-endpoint-overview.md)について学習します
 
