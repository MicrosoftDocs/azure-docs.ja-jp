---
title: "Azure Network Watcher Next Hop で次ホップを探す - Azure CLI 2.0 | Microsoft Docs"
description: "この記事では、Next Hop で Azure CLI を使用して、次ホップの種類と IP アドレスを検索する方法について説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c865793190e6079227ca093a018b0a6f98528d4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-20"></a>Azure Network Watcher の Next Hop 機能で Azure CLI 2.0 を使用して次ホップの種類を検索する

> [!div class="op_single_selector"]
> - [Azure ポータル](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Next Hop は Network Watcher の機能の 1 つであり、指定された仮想マシンに基づいて次ホップの種類と IP アドレスを取得できます。 この機能は、仮想マシンからのトラフィックがゲートウェイ、インターネット、または仮想ネットワークを経由して宛先に到達するかどうかを判断する際に役立ちます。

この記事では、Azure CLI 2.0 を使います。Azure CLI 2.0 は、Resource Manager デプロイメント モデルを対象とする Microsoft の次世代 CLI であり、Windows、Mac、Linux で利用できます。

この記事の手順を実行するには、[Mac、Linux、Windows 用の Azure コマンドライン インターフェイス (Azure CLI) をインストール](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)する必要があります。

## <a name="before-you-begin"></a>開始する前に

このシナリオでは Azure CLI を使用して、次ホップの種類と IP アドレスを検索します。

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関するページの手順を参照して、Network Watcher を作成済みであることを前提としています。 また、有効な仮想マシンがあるリソース グループを使用することも前提としています。

## <a name="scenario"></a>シナリオ

この記事で説明するシナリオでは、リソースの次ホップの種類と IP アドレスを調べる、Next Hop という Network Watcher の機能を使用します。 Next Hop の詳細については、[Next Hop の概要](network-watcher-next-hop-overview.md)に関するページをご覧ください。


## <a name="get-next-hop"></a>次ホップの取得

次ホップを取得するには、`az network watcher show-next-hop` コマンドレットを呼び出します。 このコマンドレットには、Network Watcher リソース グループ、Network Watcher、仮想マシン ID、送信元 IP アドレス、宛先 IP アドレスを渡します。 この例では、宛先 IP アドレスは別の仮想ネットワークにある VM です。 2 つの仮想ネットワークの間には、仮想ネットワーク ゲートウェイがあります。

まだ行っていない場合は、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールして構成し、[az login](/cli/azure/#login) を使用して Azure アカウントにログインします。 次に、次のコマンドを実行します。

```azurecli
az network watcher show-next-hop --resource-group <resourcegroupName> --vm <vmNameorID> --source-ip <source-ip> --dest-ip <destination-ip>

```

> [!NOTE]
VM に複数の NIC があり、NIC のいずれかで IP 転送が有効な場合は、NIC のパラメーター (-i nic-id) を指定する必要があります。 それ以外の場合は省略可能です。

## <a name="review-results"></a>結果の確認

完了すると、結果が表示されます。 次ホップの IP アドレスと、リソースの種類が返されます。

```azurecli
{
    "nextHopIpAddress": null,
    "nextHopType": "Internet",
    "routeTableId": "System Route"
}
```

現在利用可能な NextHopType の値は次のとおりです。

**次ホップの種類**

* インターネット
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* なし

## <a name="next-steps"></a>次のステップ

[Network Watcher での NSG 監査](network-watcher-nsg-auditing-powershell.md)に関する記事を参照して、ネットワーク セキュリティ グループの設定をプログラムで確認する方法を見る
