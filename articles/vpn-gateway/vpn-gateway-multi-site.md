---
title: 'VPN Gateway と PowerShell を使用して仮想ネットワークを複数のサイトに接続する: Classic | Microsoft Docs'
description: VPN Gateway を使用して複数のローカルのオンプレミスのサイトをクラシック仮想ネットワークに接続します。
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: 77f8b7094c96e507eef1d360a26240627bc0e350
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60836102"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>既存の VPN ゲートウェイ接続を使用してサイト間接続を VNet に追加する (クラシック)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (クラシック)](vpn-gateway-multi-site.md)
>
>

この記事では、PowerShell を使用して、既存の接続がある VPN ゲートウェイにサイト間 (S2S) 接続を追加する方法について説明します。 この種類の構成は、一般に "マルチサイト" 構成と呼ばれます。 この記事の手順は、クラシック デプロイ モデル (別名、サービス管理) を使用して作成された仮想ネットワークを対象としています。 次の手順は、ExpressRoute/サイト間の共存接続の構成には適用されません。

### <a name="deployment-models-and-methods"></a>デプロイメント モデルおよび方法

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

以下の表は、この構成について新しい記事、追加のツールが利用できるようになったら更新されるものです。 記事が利用できるようになったら、表から直接リンクできるようにします。

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>接続の概要

複数のオンプレミスのサイトを単一の仮想ネットワークに接続できます。 これは、ハイブリッドなクラウドのソリューションを構築する際は特に魅力的です。 Azure 仮想ネットワーク ゲートウェイに複数の接続を行うことは、他のサイト間接続に似ています。 実際、ゲートウェイが動的 (ルートベース) である限り、既存の Azure VPN ゲートウェイを使用できます。

お使いの仮想ネットワークに既に静的ゲートウェイが接続されている場合は、複数のサイトを対応するために仮想ネットワークを構築することなく、ゲートウェイ タイプを動的に変更できます。 ルーティング タイプを変更する前に、オンプレミスの VPN ゲートウェイがルートベースの VPN 構成をサポートしていることを確認します。

![マルチサイトの図](./media/vpn-gateway-multi-site/multisite.png "マルチサイト")

## <a name="points-to-consider"></a>考慮すべき点

**ポータルを使用して、この仮想ネットワークに変更を加えることはできません。** ポータルを使用しないで、ネットワーク構成ファイルに変更を加える必要があります。 ポータルで変更を行っても、この仮想ネットワークのマルチサイトのリファレンス設定が上書きされます。

マルチサイトの手順を終える頃には、ネットワーク構成ファイルを使用することにも慣れていることでしょう。 しかし、複数の人がネットワーク構成の作業を行う場合、必ず全員がこの制限事項について知っておく必要があります。 これはまったくポータルを使うことができないという意味ではありません。 この特定の仮想ネットワークへの構成の変更を行うとき以外には使用できます。

## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、次の前提があることを確認してください。

* 各オンプレミスのロケーションと互換性のある VPN ハードウェア 「[Virtual Network に使用する VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照して、使用するデバイスが互換性のあるものであることを確認してください。
* 各 VPN デバイスの外部接続用パブリック IPv4 IP アドレス。 IP アドレスを NAT の内側に割り当てることはできません。 これが要件です。
* Azure PowerShell コマンドレットの最新版をインストールする必要があります。 必ずサービス管理 (SM) バージョンと Resource Manager バージョンをインストールしてください。 詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview) 」ご覧ください。
* VPN ハードウェアの構成に詳しい作業者 そのため、VPN デバイスの構成に精通している必要があり、そうでなければ精通している人と一緒に作業を行ってください。
* 仮想ネットワークに使用する予定の IP アドレス範囲 (まだ 1 つも作成していない場合)。
* 接続する各ローカル ネットワークの IP アドレス範囲。 接続しようとしている各ローカル ネットワーク サイトの IP アドレス範囲が重複しないように確認する必要があります。 そうしないと、構成をアップロードする際に、ポータルまたは REST API によって拒否されます。<br>例えば、2 つのローカル ネットワーク サイトの両方が IP アドレス範囲 10.2.3.0/24 を含んでおり、送信先アドレスが 10.2.3.3 のパッケージを持っている場合、アドレス範囲が重複しているため、Azure はパッケージを送ろうとしているのがどちらのサイトなのかわかりません。 ルーティングの問題を避けるため、Azure は重複した範囲を持つ構成ファイルをアップロードすることを許可しません。

## <a name="1-create-a-site-to-site-vpn"></a>1.サイト間 VPN を作成する
動的ルーティング ゲートウェイを持つサイト間 VPN が既に存在する場合は、 [仮想ネットワーク構成設定のエクスポート](#export)に進んでください。 まだ作成していない場合は、以下の作業を行います。

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>サイト間仮想ネットワークは既に存在するが、静的な (ポリシー ベースの) ルーティング ゲートウェイの場合:
1. ゲートウェイ タイプを動的ルーティングに変更します。 マルチサイト VPN は動的 (ルート ベースとも呼ばれます) ルーティング ゲートウェイを必要とします。 ゲートウェイ タイプを変更するには、最初に既存のゲートウェイを削除し、新規で作成します。
2. 新しいゲートウェイを構成し、VPN トンネルを作成します。 手順については、[「SKU と VPN の種類の指定](vpn-gateway-howto-site-to-site-classic-portal.md#sku)」を参照してください。 ルーティングの種類として [動的] が指定されていることを確認してください。

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>サイト間仮想ネットワークが存在しない場合:
1. 次の手順を使用してサイト間仮想ネットワークを作成します: [サイト間 VPN 接続を持つ仮想ネットワークの作成](vpn-gateway-site-to-site-create.md)。  
2. 次の手順により動的ルーティング ゲートウェイを構成します: [VPN ゲートウェイの構成](vpn-gateway-configure-vpn-gateway-mp.md)。 必ずゲートウェイ タイプに**動的ルーティング**を選択してください。

## <a name="export"></a>2.ネットワーク構成ファイルをエクスポートする
次のコマンドを実行して、Azure のネットワーク構成ファイルをエクスポートします。 必要に応じて、ファイルの場所を変更して別の場所にエクスポートすることもできます。

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3.ネットワーク構成ファイルを開く
前のステップでダウンロードしたネットワーク構成ファイルを開きます。 任意の xml エディターを使用してください。 ファイルは次のようになります。

        <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

さらにサイト参照を追加するには (複数サイト構成を作成するには)、次の例に示すように "LocalNetworkSiteRef" 行を追加するだけです。

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5.ネットワーク構成ファイルをインポートする
ネットワーク構成ファイルをインポートします。 変更をしてこのファイルをインポートすると、新しいトンネルが追加されます。 トンネルは先ほど作成した動的ゲートウェイを使用します。 PowerShell を使用して、ファイルをインポートできます。

## <a name="6-download-keys"></a>6.キーをダウンロードする
新しいトンネルが追加されたら、PowerShell コマンドレット 'Get-AzureVNetGatewayKey' を使用して、IPsec/IKE 事前共有キーを各トンネル用に取得します。

例:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

必要に応じて、*Get Virtual Network Gateway Shared Key* という REST API を使用して事前共有キーを取得することもできます。

## <a name="7-verify-your-connections"></a>7.接続の確認
マルチサイトのトンネルの状態を確認します。 各トンネルのキーをダウンロードしたら、接続を確認します。 'Get-AzureVnetConnection' を使用して、下の例で示すように仮想ネットワークのトンネルのリストを取得します。 VNet1 とは VNet の名前です。

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

戻り値の例:

```
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
```

## <a name="next-steps"></a>次の手順

VPN Gateway について詳しくは、「 [VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。
