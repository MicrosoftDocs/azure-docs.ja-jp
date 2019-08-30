---
title: 'ルートベースの VPN ゲートウェイの作成: Azure portal | Microsoft Docs'
description: Azure portal を使用してルートベースの VPN Gateway をすばやく作成します
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: cherylmc
ms.openlocfilehash: 2a04c0fa2d92514103377c2aef420290d1bdd057
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781168"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Azure Portal を使用してルートベースの VPN ゲートウェイをすばやく作成する

この記事では、Azure Portal を使用して、ルートベースの VPN ゲートウェイをすばやく作成する方法について説明します。  VPN ゲートウェイが使用されるのは、ご利用のオンプレミスのネットワークへの VPN 接続を作成するときです。 また、VNet への接続に VPN ゲートウェイを使用することもできます。 

この記事の手順では、VNet、サブネット、ゲートウェイ サブネット、およびルートベースの VPN ゲートウェイ (仮想ネットワーク ゲートウェイ) を作成します。 ゲートウェイの作成が完了すると、接続を作成できます。 これらの手順には、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="vnet"></a>仮想ネットワークの作成

[!INCLUDE [create-gateway](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="gwsubnet"></a>ゲートウェイ サブネットの追加

[!INCLUDE [gateway subnet](../../includes/vpn-gateway-add-gateway-subnet-portal-include.md)]

## <a name="gwvalues"></a>ゲートウェイの構成または作成

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>Basic ゲートウェイ SKU では、IKEv2 と RADIUS 認証はサポートされません。 Mac クライアントを仮想ネットワークに接続する予定がある場合は、Basic SKU を使用しないでください。

## <a name="viewgw"></a>VPN ゲートウェイの表示

1. ゲートウェイの作成後、ポータルで VNet1 に移動します。 VPN ゲートウェイは、接続されたデバイスとして [概要] ページに表示されます。

   ![接続されたデバイス](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "接続されたデバイス")

2. デバイスの一覧で **[VNet1GW]** をクリックして詳細情報を表示します。

   ![VPN ゲートウェイの表示](./media/create-routebased-vpn-gateway-portal/view-gateway.png "VPN ゲートウェイの表示")

## <a name="next-steps"></a>次の手順

ゲートウェイの作成が完了したら、自分の仮想ネットワークと別の VNet の間の接続を作成できます。 または、自分の仮想ネットワークとオンプレミスの場所の間の接続を作成します。

> [!div class="nextstepaction"]
> [サイト間接続を作成する](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [ポイント対サイト接続を作成する](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [別の VNet への接続を作成する](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
