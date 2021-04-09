---
title: Azure Kubernetes Service クラスターに対する Application Gateway イングレス コントローラー アドオンを無効にしてから再度有効にする
description: この記事では、AKS クラスターに対する AGIC アドオンを無効にしてから再度有効にする方法について説明します
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84807955"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>AKS クラスターに対する AGIC アドオンを無効にしてから再度有効にする
AKS アドオンとしてデプロイされた Application Gateway イングレス コントローラー (AGIC) を使用すると、1 行の Azure CLI でアドオンを有効および無効にすることができます。 AGIC アドオンを無効にしたときの Application Gateway のライフ サイクルは、Application Gateway が AGIC アドオンによって作成されたかどうか、または AGIC アドオンとは別にデプロイされたかどうかによって異なります。 AGIC アドオンを無効にした場合は、同じコマンドを実行して再び有効にすることができます。または、既存の AKS クラスターと Application Gateway を使用して、AGIC アドオンを有効にします。

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>関連付けられた Application Gateway で AGIC アドオンを無効にする 
最初にすべてをセットアップしたときに、AGIC アドオンによって Application Gateway が自動的にデプロイされた場合は、AGIC アドオンを無効にすると、2 つの条件に基づいて Application Gateway が既定で削除されます。 AGIC アドオンでは、無効にされたときに関連付けられている Application Gateway を削除する必要があるかどうかを判断するために、次の 2 つの条件が調べられます。
- AGIC アドオンが関連付けられている Application Gateway は、MC_* ノード リソース グループにデプロイされているか。 
- AGIC アドオンに関連付けられている Application Gateway には、"created-by: ingress-appgw" というタグが付いているか。 このタグは、Application Gateway がアドオンによってデプロイされたかどうかを判断するために、AGIC によって使用されます。 

両方の条件が満たされている場合、アドオンを無効にすると、AGIC アドオンによって作成された Application Gateway は削除されます。ただし、Application Gateway がデプロイされていたパブリック IP またはサブネットは削除されません。 最初の条件が満たされていない場合は、Application Gateway に "created-by: ingress-appgw" タグがあるかどうかは関係ありません。アドオンを無効にしても、Application Gateway は削除されません。 同様に、2 番目の条件が満たされていない場合は (つまり、Application Gateway にそのタグがない場合)、アドオンを無効にしても、MC_* ノード リソース グループ内の Application Gateway は削除されません。 

> [!TIP] 
> アドオンを無効にしたときに Application Gateway が削除されないようにしたい場合で、両方の条件が満たされているときは、"created-by: ingress-appgw" タグを削除することで、アドオンによって Application Gateway が削除されないようにします。 

AGIC アドオンを無効にするには、次のコマンドを実行します。 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>既存の Application Gateway と AKS クラスターで AGIC アドオンを有効にする
AGIC アドオンを無効にした後でアドオンを再度有効にする必要がある場合、または既存の Application Gateway と AKS クラスターを使用してアドオンを有効にする場合は、次のコマンドを実行します。

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>次のステップ
既存の Application Gateway と AKS クラスターを使用して AGIC アドオンを有効にする方法の詳細については、[AGIC アドオンのブラウンフィールド デプロイ](tutorial-ingress-controller-add-on-existing.md)に関する記事を参照してください。