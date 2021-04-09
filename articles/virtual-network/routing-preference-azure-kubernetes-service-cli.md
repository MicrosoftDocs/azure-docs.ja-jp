---
title: Azure CLI を使用して Azure Kubernetes サービスのルーティング設定を構成する
titlesuffix: Azure Virtual Network
description: Azure CLI を使用して AKS クラスターのルーティング設定を構成する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: ac70f48a3c484f8865c54e09c59662a14a259e74
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678736"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Azure CLI を使用して Kubernetes クラスターのルーティング設定を構成する

この記事では、Azure CLI を使用して ISP ネットワーク ( **[インターネット]** オプション) を介して Kubernetes クラスターのルーティング設定を構成する方法について説明します。 ルーティング設定は、ルーティング設定の種類 **インターネット**** のパブリック IP アドレスを作成し、AKS クラスターの作成中にそれを使用することによって設定されます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.49 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する
[az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。 次の例では、Azure の **米国東部** リージョンにリソース グループを作成します。

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) コマンドを使用し、種類が **インターネット** のルーティング設定でパブリック IP アドレスを作成します。

次のコマンドを実行すると、**米国東部** Azure リージョンに **インターネット** をルーティング優先設定にして新しいパブリック IP アドレスが作成されます。

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```
> [!NOTE]
>  現在、ルーティング優先設定では IPV4 パブリック IP アドレスのみがサポートされています。

## <a name="get-the-id-of-public-ip-address"></a>パブリック IP アドレスの ID を取得する

次のコマンドを実行すると、前のセクションで作成したパブリック IP アドレスの ID が返されます。
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>パブリック IP を使用して Kubernetes クラスターを作成する

次のコマンドを実行すると、前のセクションで作成したパブリック IP を使用して AKS クラスターが作成されます。

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>AKS クラスターをデプロイするには数分かかります。

検証するには、Azure portal の前の手順で作成したパブリック IP を検索します。次に示すように、Kubernetes クラスターに関連付けられているロード バランサーに IP が関連付けられていることを確認できます。

 ![Kubernetes のパブリック IP のルーティング設定](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>次のステップ

- [パブリック IP アドレスでのルーティング優先設定](routing-preference-overview.md)について詳しく学習する。 
- [Azure CLI を使用して VM 用にルーティング優先設定を構成する](configure-routing-preference-virtual-machine-cli.md)。

