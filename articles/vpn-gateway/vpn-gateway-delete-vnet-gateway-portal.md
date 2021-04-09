---
title: 'Azure VPN Gateway:ゲートウェイを削除する: ポータル'
description: Azure portal を使用して仮想ネットワーク ゲートウェイを削除する
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/10/2021
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 413fd8c7f03ef44abe4bece39ca717c533dea66b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376388"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>ポータルを使用して仮想ネットワーク ゲートウェイを削除する

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (クラシック)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

この記事は、仮想ネットワーク ゲートウェイを削除する際に役立ちます。 VPN ゲートウェイ構成ためのゲートウェイを削除するときには、選択可能な異なる方法がいくつかあります。

* テスト環境の場合のように、すべてを削除してやり直す場合は、リソース グループを削除することができます。 リソース グループを削除すると、グループ内のすべてのリソースが削除されます。 この方法は、リソース グループ内のどのリソースも保持する必要がない場合にのみお勧めします。 この方法を使用して一部のリソースだけを選択して削除することはできません。

* リソース グループ内の一部のリソースを保持する必要がある場合は、仮想ネットワーク ゲートウェイの削除が少し複雑になります。 仮想ネットワーク ゲートウェイを削除する前に、まず、そのゲートウェイに依存しているリソースをすべて削除する必要があります。 従う手順は、作成した接続の種類と、各接続に依存するリソースによって異なります。

> [!IMPORTANT]
> この記事の手順は、Resource Manager デプロイ モデルに適用されます。 クラシック デプロイ モデルを使用してデプロイされた VPN ゲートウェイを削除するには、[ゲートウェイの削除 (クラシック)](vpn-gateway-delete-vnet-gateway-classic-powershell.md) に関する記事の手順に従います。

## <a name="delete-a-vpn-gateway"></a>VPN ゲートウェイの削除

仮想ネットワーク ゲートウェイを削除するには、まず、仮想ネットワーク ゲートウェイに関連する各リソースを削除する必要があります。 リソースは、依存関係に応じた特定の順序で削除する必要があります。

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

この時点で、仮想ネットワーク ゲートウェイが削除されます。

### <a name="to-delete-additional-resources"></a>その他のリソースを削除するには

以降の手順は、使用されなくなったリソースを削除するのに役立ちます。

#### <a name="to-delete-the-local-network-gateway"></a>ローカル ネットワーク ゲートウェイを削除するには

1. **[すべてのリソース]** で、各接続に関連付けられていたローカル ネットワーク ゲートウェイを見つけます。
1. ローカル ネットワーク ゲートウェイの **[概要]** ブレードで、**[削除]** をクリックします。

#### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>ゲートウェイのパブリック IP アドレス リソースを削除するには

1. **[すべてのリソース]** で、ゲートウェイに割り当て済みのパブリック IP アドレス リソースを検索します。 仮想ネットワーク ゲートウェイがアクティブ/アクティブであった場合は、2 つのパブリック IP アドレスが表示されます。
1. パブリック IP アドレスの **[概要]** ページで **[削除]** をクリックしてから、**[はい]** をクリックして確認します。

#### <a name="to-delete-the-gateway-subnet"></a>ゲートウェイ サブネットを削除するには

1. **[すべてのリソース]** で、仮想ネットワークを検索します。 
1. **[サブネット]** ブレードで、**[GatewaySubnet]**、**[削除]** の順にクリックします。 
1. 確認画面で **[はい]** をクリックして、ゲートウェイ サブネットを削除します。

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>リソース グループを削除して VPN ゲートウェイを削除する

リソース グループにどのリソースも保持する必要がなく、単に最初からやり直したい場合は、リソース グループ全体を削除できます。 すべてを削除するには、これが簡単な方法です。 次の手順は、Resource Manager デプロイ モデルに該当します。

1. **[すべてのリソース]** でリソース グループを見つけ、クリックしてブレードを開きます。
1. **[削除]** をクリックします。 [削除] ブレードに関連するリソースを表示します。 これらのリソースをすべて削除することを確認します。 削除しない場合は、この記事の上部にある「VPN ゲートウェイの削除」の手順を実行してください。
1. 続行するには、削除するリソース グループの名前を入力し、**[削除]** をクリックします。
