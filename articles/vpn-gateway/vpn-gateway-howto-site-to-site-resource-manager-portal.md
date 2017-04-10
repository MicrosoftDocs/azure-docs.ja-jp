---
title: "オンプレミスのネットワークを Azure 仮想ネットワークに接続する: サイト間 VPN: ポータル | Microsoft Docs"
description: "パブリック インターネットを経由したオンプレミスのネットワークから Azure 仮想ネットワークへの IPsec 接続を作成する手順。 これらの手順は、ポータルを使用してクロスプレミスのサイト間 VPN Gateway 接続を作成する際に役立ちます。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 80939bb48c29ba39e2d347cb80d6169d79329cfc
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Azure Portal でサイト間接続を作成する
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [クラシック - Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [クラシック - クラシック ポータル](vpn-gateway-site-to-site-create.md)
>
>


サイト間 (S2S) VPN ゲートウェイ接続とは、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介した接続です。 この種類の接続では、オンプレミスの VPN デバイスが必要です。そのデバイスは、パブリック IP アドレスを割り当てられていて、NAT の内側に配置されていない必要があります。 サイト間接続は、クロスプレミスおよびハイブリッド構成に使用できます。

この記事では、Azure Resource Manager デプロイメント モデルと Azure Portal を使用して、仮想ネットワークと、オンプレミス ネットワークに対するサイト間 VPN ゲートウェイ接続を作成する手順について説明します。 

![クロスプレミスのサイト間 VPN Gateway 接続の図](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>サイト間接続のデプロイメント モデルとデプロイ方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

以下の表は、サイト間の構成に関して現在利用できるデプロイメント モデルとデプロイ方法を示しています。 構成手順を説明した記事が利用できるようになったら、表から直接リンクできるようにします。

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>追加の構成
VNet どうしは接続しても、オンプレミスへの接続は作成しない場合は、 [VNet 間の接続の構成](vpn-gateway-vnet-vnet-rm-ps.md)に関するページを参照してください。 既存の接続が存在する VNet にサイト間接続を追加する場合は、[VPN Gateway 接続が既に存在する VNet へのサイト間接続の追加](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に
構成を開始する前に、以下が揃っていることを確認します。

* 互換性のある VPN デバイスおよびデバイスを構成できる人員。 「 [VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照してください。
* オンプレミス ネットワークの IP アドレス空間を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。 複数のサイト間接続で、重複するアドレス空間を使用することはできません。
* VPN デバイスの外部接続用パブリック IP アドレス。 この IP アドレスを NAT の内側に割り当てることはできません。
* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](http://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

### <a name="values"></a>値の例
以下の手順を練習として使用する場合は、次の例の値を使用できます。

* **VNet の名前:** TestVNet1
* **アドレス空間:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (この演習では省略可能)
* **サブネット:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (この演習では省略可能)
  * GatewaySubnet: 10.11.255.0/27
* **リソース グループ:** TestRG1
* **場所:** 米国東部
* **DNS サーバー:** ご利用の DNS サーバーの IP アドレス
* **仮想ネットワーク ゲートウェイ名:** VNet1GW
* **パブリック IP:** VNet1GWIP
* **VPN の種類:** ルート ベース
* **接続の種類:** サイト間 (IPsec)
* **ゲートウェイの種類:** VPN
* **ローカル ネットワーク ゲートウェイ名:** Site2
* **接続名:** VNet1toSite2

## <a name="CreatVNet"></a>1.仮想ネットワークの作成

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2.DNS サーバーの指定
サイト間接続に DNS は不要です。 ただし仮想ネットワークにデプロイされたリソースで名前解決を使用する場合は、DNS サーバーを指定する必要があります。 この設定では、この仮想ネットワークの名前解決に使用する DNS サーバーを指定することができます。 この設定で、DNS サーバーは作成されません。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3.ゲートウェイ サブネットの作成
VPN ゲートウェイのゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サブネットには、VPN ゲートウェイ サービスが使用する IP アドレスが含まれます。 可能であれば、CIDR ブロック /28 または /27 を使用してゲートウェイ サブネットを作成してください。 そうすることで、将来的なゲートウェイ構成要件に対応するのに十分な IP アドレスを確保できます。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]

## <a name="VNetGateway"></a>4.仮想ネットワーク ゲートウェイの作成

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5.ローカル ネットワーク ゲートウェイの作成
ローカル ネットワーク ゲートウェイは、オンプレミスの場所を指します。 オンプレミスの VPN デバイスにルーティングされるアドレス空間は、ローカル ネットワーク ゲートウェイに指定した設定によって決まります。

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6.VPN デバイスの構成
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>7.サイト間 VPN 接続の作成

この手順では、仮想ネットワーク ゲートウェイとオンプレミス VPN デバイスとの間にサイト間 VPN 接続を作成します。 このセクションを開始する前に、仮想ネットワーク ゲートウェイとローカル ネットワーク ゲートウェイの作成が完了していることを確認してください。

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8.VPN 接続の確認

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>次のステップ
*  接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) に関するページを参照してください。
*  BGP の詳細については、[BGP の概要](vpn-gateway-bgp-overview.md)に関する記事と [BGP の構成方法](vpn-gateway-bgp-resource-manager-ps.md)に関する記事を参照してください。


