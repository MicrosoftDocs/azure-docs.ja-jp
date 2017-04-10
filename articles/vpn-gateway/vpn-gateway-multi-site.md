---
title: "VPN ゲートウェイと PowerShell を使用して仮想ネットワークを複数のサイトに接続する |Microsoft Docs"
description: "この記事では、クラシック デプロイメント モデルで VPN Gateway を使用して複数のローカルのオンプレミスのサイトを仮想ネットワークに接続する操作について説明します。"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2016
ms.author: yushwang
translationtype: Human Translation
ms.sourcegitcommit: 1c3a38e46f427e6c6398971d9c45baec27d39a03
ms.openlocfilehash: f34e5cfabbec7cc9c1299bea282404d6de69f82f


---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>既存の VPN ゲートウェイ接続を使用してサイト間接続を VNet に追加する
> [!div class="op_single_selector"]
> * [Resource Manager - ポータル](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [クラシック - PowerShell](vpn-gateway-multi-site.md)
>
>

この記事では、PowerShell を使用して、既存の接続がある VPN ゲートウェイにサイト間 (S2S) 接続を追加する方法について説明します。 この種類の構成は、一般に "マルチサイト" 構成と呼ばれます。

この記事は、クラシック デプロイメント モデル (別名、サービス管理) を使用して作成された仮想ネットワークを対象としています。 次の手順は、ExpressRoute/サイト間の共存接続の構成には適用されません。 共存接続の詳細については、「[クラシック デプロイ モデルにおいて共存する ExpressRoute 接続とサイト間接続を構成する](../expressroute/expressroute-howto-coexist-classic.md)」を参照してください。

### <a name="deployment-models-and-methods"></a>デプロイメント モデルおよび方法
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

以下の表は、この構成について新しい記事、追加のツールが利用できるようになったら更新されるものです。 記事が利用できるようになったら、表から直接リンクできるようにします。

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>接続の概要
複数のオンプレミスのサイトを単一の仮想ネットワークに接続できます。 これは、ハイブリッドなクラウドのソリューションを構築する際は特に魅力的です。 Azure 仮想ネットワーク ゲートウェイに複数の接続を行うことは、他のサイト間接続に非常に似ています。 実際、ゲートウェイが動的 (ルートベース) である限り、既存の Azure VPN ゲートウェイを使用できます。

お使いの仮想ネットワークに既に静的ゲートウェイが接続されている場合は、複数のサイトを対応するために仮想ネットワークを構築することなく、ゲートウェイ タイプを動的に変更できます。 ルーティング タイプを変更する前に、オンプレミスの VPN ゲートウェイがルートベースの VPN 構成をサポートしていることを確認します。

![マルチサイトの図](./media/vpn-gateway-multi-site/multisite.png "マルチサイト")

## <a name="points-to-consider"></a>考慮すべき点
**を Azure クラシック ポータル使用して、この仮想ネットワークに変更を加えることはできません。** このリリースでは、Azure クラシック ポータルを使用しないで、ネットワーク構成ファイルに変更を加える必要があります。 Azure クラシック ポータルで変更を行っても、この仮想ネットワークのマルチサイトのリファレンス設定が上書きされます。

マルチサイトの手順を終える頃には、ネットワーク構成ファイルを使用することにもだいぶ慣れていることでしょう。 しかし、複数の人がネットワーク構成の作業を行う場合、必ず全員がこの制限事項について知っておく必要があります。 これは全く Azure クラシック ポータルを使うことができないという意味ではありません。 この特定の仮想ネットワークへの構成の変更を行うとき以外には使用できます。

## <a name="before-you-begin"></a>開始する前に
構成を開始する前に、次の前提があることを確認してください。

* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* 各オンプレミスのロケーションと互換性のある VPN ハードウェア 「[Virtual Network に使用する VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照して、使用するデバイスが互換性のあるものであることを確認してください。
* 各 VPN デバイスの外部接続用パブリック IPv4 IP アドレス。 IP アドレスを NAT の内側に割り当てることはできません。 これが要件です。
* Azure PowerShell コマンドレットの最新版をインストールする必要があります。 PowerShell コマンドレットのインストールの詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」を参照してください。
* VPN ハードウェアの構成に詳しい作業者 VPN デバイスを構成する際に、Azure クラシック ポータルから自動生成の VPN スクリプトを使用することはできません。 そのため、VPN デバイスの構成に精通している必要があり、そうでなければ精通している人と一緒に作業を行ってください。
* 仮想ネットワークに使用する予定の IP アドレス範囲 (まだ&1; つも作成していない場合)。
* 接続する各ローカル ネットワークの IP アドレス範囲。 接続しようとしている各ローカル ネットワーク サイトの IP アドレス範囲が重複しないように確認する必要があります。 そうしないと、構成をアップロードする際に、Azure クラシック ポータルまたは REST API によって拒否されます。

    例えば、2 つのローカル ネットワーク サイトの両方が IP アドレス範囲 10.2.3.0/24 を含んでおり、送信先アドレスが 10.2.3.3 のパッケージを持っている場合、アドレス範囲が重複しているため、Azure はパッケージを送ろうとしているのがどちらのサイトなのかわかりません。 ルーティングの問題を避けるため、Azure は重複した範囲を持つ構成ファイルをアップロードすることを許可しません。

## <a name="1-create-a-site-to-site-vpn"></a>1.サイト間 VPN を作成する
動的ルーティング ゲートウェイを持つサイト間 VPN が既に存在する場合は、 [仮想ネットワーク構成設定のエクスポート](#export)に進んでください。 まだ作成していない場合は、以下の作業を行います。

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>サイト間仮想ネットワークは既に存在するが、静的な (ポリシー ベースの) ルーティング ゲートウェイの場合:
1. ゲートウェイ タイプを動的ルーティングに変更します。 マルチサイト VPN は動的 (ルート ベースとも呼ばれます) ルーティング ゲートウェイを必要とします。 ゲートウェイ タイプを変更するには、最初に既存のゲートウェイを削除し、新規で作成します。 手順については、「 [ゲートウェイの VPN ルーティングの種類を変更する方法](vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway)」を参照してください。  
2. 新しいゲートウェイを構成し、VPN トンネルを作成します。 手順については、「 [Azure クラシック ポータルで VPN ゲートウェイを構成する](vpn-gateway-configure-vpn-gateway-mp.md)」を参照してください。 最初に、ゲートウェイ タイプを動的ルーティングに変更します。

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>サイト間仮想ネットワークが存在しない場合:
1. 次の手順によりサイト間仮想ネットワークを作成します: [Azure クラシック ポータルでサイト間 VPN 接続を使用して Virtual Network を作成する](vpn-gateway-site-to-site-create.md)。  
2. 次の手順により動的ルーティング ゲートウェイを構成します。[VPN ゲートウェイの構成](vpn-gateway-configure-vpn-gateway-mp.md) 必ずゲートウェイ タイプに**動的ルーティング**を選択してください。

## <a name="a-nameexporta2-export-the-network-configuration-file"></a><a name="export"></a>2.ネットワーク構成ファイルをエクスポートする
ネットワーク構成ファイルをエクスポートします。 エクスポートするファイルは新しいマルチサイト設定の構成に使われます。 ファイルのエクスポートに関する手順が必要な場合は、「[Azure ポータルで従来の VNet を作成する方法](../virtual-network/virtual-networks-create-vnet-classic-pportal.md#how-to-create-a-classic-vnet-in-the-azure-portal)」のセクションをご覧ください。

## <a name="3-open-the-network-configuration-file"></a>3.ネットワーク構成ファイルを開く
前のステップでダウンロードしたネットワーク構成ファイルを開きます。 任意の xml エディターを使用してください。 ファイルは次のようになります。

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4.複数のサイト リファレンスを追加する
サイト リファレンスの情報を追加または削除する場合、ConnectionsToLocalNetwork/LocalNetworkSiteRef に対して構成の変更を行います。 新しいローカル サイトのリファレンスを追加することで、Azure が新しくトンネルを作成します。 下の例で、ネットワーク構成はシングル サイト接続のものです。 変更が完了したら、ファイルを保存します。

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

    To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below:

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## <a name="5-import-the-network-configuration-file"></a>5.ネットワーク構成ファイルをインポートする
ネットワーク構成ファイルをインポートします。 変更をしてこのファイルをインポートすると、新しいトンネルが追加されます。 トンネルは先ほど作成した動的ゲートウェイを使用します。 ファイルのインポートに関する手順が必要な場合は、「[Azure ポータルで従来の VNet を作成する方法](../virtual-network/virtual-networks-create-vnet-classic-pportal.md#how-to-create-a-classic-vnet-in-the-azure-portal)」のセクションをご覧ください。  

## <a name="6-download-keys"></a>6.キーをダウンロードする
新しいトンネルが追加されたら、PowerShell コマンドレット `Get-AzureVNetGatewayKey` を使用して、IPsec/IKE 事前共有キーを各トンネル用に取得します。

次に例を示します。

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

必要に応じて、*Get Virtual Network Gateway Shared Key* という REST API を使用して事前共有キーを取得することもできます。

## <a name="7-verify-your-connections"></a>7.接続の確認
マルチサイトのトンネルの状態を確認します。 各トンネルのキーをダウンロードしたら、接続を確認します。 `Get-AzureVnetConnection` を使用して、下の例で示すように仮想ネットワークのトンネルのリストを取得します。 VNet1 とは VNet の名前です。

    Get-AzureVnetConnection -VNetName VNET1

    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

## <a name="next-steps"></a>次のステップ
VPN Gateway について詳しくは、「 [VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。



<!--HONumber=Jan17_HO4-->


