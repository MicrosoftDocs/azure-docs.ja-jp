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
ms.openlocfilehash: b66f1f0061f697349afae21f5f9c63a4089c2794
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401709"
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

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.28 以降のバージョンを実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>サブネットのオーバーライドを使用して Traffic Manager エンドポイントを更新します。
Azure CLI を使用して、[az network traffic-manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) でエンドポイントを更新します。

```azurecli

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

IP アドレス範囲を削除するには、 **--remove** オプションを指定して [az network traffic-manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) を実行します。

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>次の手順
Traffic Manager の [トラフィック ルーティング方法](traffic-manager-routing-methods.md)の詳細を確認する。

[サブネット トラフィックのルーティング方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)の詳細を確認する