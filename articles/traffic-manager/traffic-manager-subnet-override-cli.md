---
title: Azure CLI を使用した Azure Traffic Manager のサブネットのオーバーライド | Microsoft Docs
description: この記事では、Traffic Manager サブネットのオーバーライドを使用して、Traffic Manager プロファイルのルーティング方法をオーバーライドし、事前に定義された IP 範囲からエンドポイントへのマッピングを介してエンドユーザーの IP アドレスに基づいてエンドポイントにトラフィックを送信する方法について説明します。
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 2e289728c7fde9b98256d079d45067aba1d4d805
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211330"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Azure CLI を使用した Traffic Manager のサブネットのオーバーライド

Traffic Manager サブネットのオーバーライドを使用すると、プロファイルのルーティング方法を変更できます。  オーバーライドを追加すると、事前に定義された IP 範囲からエンドポイントへのマッピングを使用してエンド ユーザーの IP アドレスに基づいてトラフィックが転送されます。 

## <a name="how-subnet-override-works"></a>サブネットのオーバーライドのしくみ

サブネットのオーバーライドがトラフィック マネージャー プロファイルに追加されると、Traffic Manager によって、エンド ユーザーの IP アドレスに対してサブネットのオーバーライドがあるかどうかが最初に確認されます。 見つかった場合、ユーザーの DNS クエリは対応するエンドポイントに送られます。  マッピングが見つからない場合、Traffic Manager はプロファイルの元のルーティング方法にフォールバックします。 

IP アドレス範囲は、CIDR 範囲 (1.2.3.0/24 など) またはアドレス範囲として指定できます (1.2.3.4-5.6.7.8 など) として指定できます。 各エンドポイントに関連付けられている IP 範囲は、そのエンドポイントに対して一意である必要があります。 異なるエンドポイント間で IP 範囲が重複していると、プロファイルが Traffic Manager によって拒否されます。

サブネットのオーバーライドをサポートするルーティング プロファイルには、次の 2 種類があります。

* **地理的** - DNS クエリの IP アドレスに対するサブネットのオーバーライドが Traffic Manager によって検出された場合、エンドポイントの正常性にかかわらず、クエリがエンドポイントにルーティングされます。
* **パフォーマンス** - DNS クエリの IP アドレスに対するサブネットのオーバーライドが Traffic Manager によって検出された場合、トラフィックは正常な場合にのみエンドポイントにルーティングされます。  サブネットのオーバーライド エンドポイントが正常でない場合、Traffic Manager はパフォーマンス ルーティング ヒューリスティックにフォールバックします。

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager のサブネットのオーバーライドの作成

Traffic Manager サブネットのオーバーライドを作成するには、Azure CLI を使用して、Traffic Manager エンドポイントへのオーバーライド用のサブネットを追加します。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>サブネットのオーバーライドを使用して Traffic Manager エンドポイントを更新します。
Azure CLI を使用して、[az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update) でエンドポイントを更新します。

```azurecli-interactive
### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints
```

IP アドレス範囲を削除するには、**--remove** オプションを指定して [az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update) を実行します。

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>次の手順

Traffic Manager の [トラフィック ルーティング方法](traffic-manager-routing-methods.md)の詳細を確認する。

[サブネット トラフィックのルーティング方法](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)の詳細を確認する