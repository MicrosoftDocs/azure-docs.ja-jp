---
title: Azure CLI を使用してパブリック IP アドレスのルーティング優先設定を構成する
titlesuffix: Azure Virtual Network
description: インターネット トラフィックのルーティング優先設定を使用してパブリック IP アドレスを作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 64284b198fc76c219ffe0dfbc57461b587b23130
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504604"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Azure CLI を使用してパブリック IP アドレスのルーティング優先設定を構成する

この記事では、Azure CLI を使用して、パブリック IP アドレスに対して ISP ネットワーク ( **[インターネット]** オプション) によるルーティング優先設定を構成する方法について説明します。 パブリック IP アドレスを作成した後は、インターネットでの受信および送信トラフィック用に、次の Azure リソースと関連付けることができます。

* 仮想マシン
* 仮想マシン スケール セット
* Azure Kubernetes Service (AKS)
* インターネットに接続するロード バランサー
* Application Gateway
* Azure Firewall

既定では、パブリック IP アドレスに対するルーティング優先設定は、すべての Azure サービスについて Microsoft グローバル ネットワークに設定され、任意の Azure サービスと関連付けることができます。

> [!IMPORTANT]
> 現在、ルーティング優先設定はパブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure サブスクリプションをお持ちでない場合は、ここで[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
代わりに Azure CLI をローカルにインストールして使用する場合、このクイック スタートでは、Azure CLI バージョン 2.0.49 以降を使用する必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードについては、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

## <a name="register-the-feature-for-your-subscription"></a>サブスクリプションに機能を登録する
現在、ルーティング優先設定の機能はプレビューの段階です。 次のようにして、サブスクリプションに機能を登録します。
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```

## <a name="create-a-resource-group"></a>リソース グループを作成する
[az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。 次の例では、Azure の**米国東部**リージョンにリソース グループを作成します。

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

次に示すような形式の [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) コマンドを使用し、種類が "インターネット" のルーティング優先設定でパブリック IP アドレスを作成します。

次のコマンドを実行すると、**米国東部** Azure リージョンに**インターネット**をルーティング優先設定にして新しいパブリック IP アドレスが作成されます。

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

上で作成したパブリック IP アドレスを、[Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の仮想マシンに関連付けることができます。 チュートリアル ページ「[仮想マシンへのパブリック IP アドレスの関連付け](associate-public-ip-address-vm.md#azure-cli)」の CLI セクションを使用して、パブリック IP アドレスを VM に関連付けます。 上で作成したパブリック IP アドレスを、ロード バランサーの**フロントエンド**構成に割り当てることで、[Azure Load Balancer](../load-balancer/load-balancer-overview.md) と関連付けることもできます。 このパブリック IP アドレスは、負荷分散された仮想 IP アドレス (VIP) として機能します。

## <a name="next-steps"></a>次のステップ

- [パブリック IP アドレスでのルーティング優先設定](routing-preference-overview.md)について詳しく学習する。 
- [Azure CLI を使用して VM 用にルーティング優先設定を構成する](configure-routing-preference-virtual-machine-cli.md)。

