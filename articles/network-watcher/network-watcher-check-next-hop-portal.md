---
title: "Azure Network Watcher Next Hop を使用して次ホップを検索する - Azure Portal | Microsoft Docs"
description: "この記事では、Azure Portal で Next Hop を使用して、次ホップの種類と IP アドレスを確認する方法について説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 95ecd8d4ef3dd7a956206e7c3e07c793db0111f7
ms.openlocfilehash: 5fb85551d3c5fb8d1c63a965d89bae788000afe8
ms.lasthandoff: 03/31/2017


---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>ポータルで Azure Network Watcher の Next Hop 機能を使用して次ホップの種類を確認する

> [!div class="op_single_selector"]
> - [Azure ポータル](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Next Hop は Network Watcher の機能の&1; つであり、指定された仮想マシンに基づいて次ホップの種類と IP アドレスを取得できます。 この機能は、仮想マシンからのトラフィックがゲートウェイ、インターネット、または仮想ネットワークを経由して宛先に到達するかどうかを判断する際に役立ちます。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する記事の手順に従って Network Watcher を作成済みであることを前提としています。 また、有効な仮想マシンがあるリソース グループを使用することも前提としています。

## <a name="scenario"></a>シナリオ

この記事で説明するシナリオでは、Next Hop を使用して、リソースの次ホップの種類と IP アドレスを確認します。 Next Hop の詳細については、[Next Hop の概要](network-watcher-next-hop-overview.md)に関する記事をご覧ください。

このシナリオでは次のことを行います。

* 仮想マシンの次ホップを取得する。

## <a name="get-next-hop"></a>次ホップの取得

### <a name="step-1"></a>手順 1

Azure Portal で Network Watcher リソースに移動します。

### <a name="step-2"></a>手順 2.

ナビゲーション ウィンドウで **[次ホップ]** をクリックします。仮想マシンとネットワーク インターフェイスを選択し、送信元 IP と宛先 IP を入力して、**[次ホップ]** をクリックします。

> [!NOTE]
> Next Hop では、実行する VM リソースが割り当てられている必要があります。

![次ホップの概要の取得][1]

### <a name="step-3"></a>手順 3.

タスクが完了すると、結果が表示されます。 次ホップのデバイスの IP アドレスと種類が表示されます。 ポータルで使用可能な戻り値を次の表に示します。

**次ホップの種類**

* インターネット
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* なし

このトラフィックのルーティングにカスタム ルートを使用していた場合、結果にユーザー定義ルート (UDR) も表示されます。

![次ホップの結果の取得][2]

## <a name="next-steps"></a>次のステップ

[Network Watcher での NSG 監査](network-watcher-nsg-auditing-powershell.md)に関する記事を参照して、ネットワーク セキュリティ グループの設定をプログラムで確認する方法を見る

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png















