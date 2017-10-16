---
title: "Azure CLI を使用したゾーニングされたパブリック IP アドレスの作成 | Microsoft Docs"
description: "Azure CLI を使用して、可用性ゾーン内にパブリック IP を作成します。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ef93d43bbd0c58950027810c8c335d9076574326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-cli"></a>Azure CLI を使用して、可用性ゾーン内にパブリック IP アドレスを作成する

Azure 可用性ゾーン (プレビュー) 内にパブリック IP アドレスをデプロイできます。 可用性ゾーンとは、Azure リージョン内の物理的に分離されたゾーンのことです。 以下の項目について説明します。

> * 可用性ゾーン内にパブリック IP アドレスを作成する
> * 可用性ゾーン内に作成された関連リソースを識別する

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI をローカルにインストールして使用することを選択した場合、このチュートリアルでは、バージョン 2.0.17 を超える Azure CLI のバージョンを実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

> [!NOTE]
> 可用性ゾーンはプレビュー段階にあり、開発とテストのシナリオのために準備されています。 選択された Azure リソース、リージョン、および VM サイズ ファミリに対するサポートが使用できます。 使用を開始する方法、および可用性ゾーンを試行する場合にどの Azure リソース、リージョン、および VM サイズ ファミリを使用できるかの詳細については、「[Overview of Availability Zones (可用性ゾーンの概要)](https://docs.microsoft.com/azure/availability-zones/az-overview)」を参照してください。 サポートについては、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) でアクセスするか、または [Azure サポート チケットを開く](../azure-supportability/how-to-create-azure-support-request.md)ことができます。

## <a name="create-a-zonal-public-ip-address"></a>ゾーンのパブリック IP アドレスを作成する

次のコマンドを使用して、可用性ゾーン内にパブリック IP アドレスを作成します。


```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIp --zone 2 --location westeurope
```
> [!NOTE]
> Standard SKU のパブリック IP アドレスを仮想マシンのネットワーク インターフェイスに割り当てる場合は、[ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)で目的のトラフィックを明示的に許可する必要があります。 ネットワーク セキュリティ グループを作成して関連付け、目的のトラフィックを明示的に許可するまで、そのリソースとの通信は失敗します。

## <a name="get-zone-information-about-a-public-ip-address"></a>パブリック IP アドレスに関するゾーン情報を取得する

次のコマンドを使用して、パブリック IP アドレスのゾーン情報を取得します。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIp
```

## <a name="next-steps"></a>次のステップ

- [可用性ゾーン](https://docs.microsoft.com/azure/availability-zones/az-overview)の詳細を学習する
- [パブリック IP アドレス](../virtual-network/virtual-network-public-ip-address.md)の詳細を学習する 
