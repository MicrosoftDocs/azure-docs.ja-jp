---
title: インクルード ファイル
description: インクルード ファイル
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70ac106995324c758bde942d12191a01e3457e6e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125091"
---
> [!NOTE]
> これらの例は、S2S/ExpressRoute の共存構成には該当しません。
> 共存構成におけるゲートウェイについて詳しくは、[接続の共存の構成](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)に関するページを参照してください。

## <a name="add-a-gateway"></a>ゲートウェイを追加する

クラシック リソース モデルを使用して仮想ネットワークにゲートウェイを追加するときは、ゲートウェイを作成する前に、ネットワーク構成ファイルに直接変更を加えます。 ゲートウェイを作成するためには、このファイルに以下の例の値が存在している必要があります。 過去に、仮想ネットワークにゲートウェイが関連付けられていた場合、そのいくつかの値は既に存在すると考えられます。 以下の値を反映するように、ファイルに変更を加えてください。

### <a name="download-the-network-configuration-file"></a>ネットワーク構成ファイルをダウンロードする

1. [ネットワーク構成ファイル](../articles/virtual-network/virtual-networks-using-network-configuration-file.md)に関する記事の手順に従ってネットワーク構成ファイルをダウンロードします。 テキスト エディターを使用してファイルを開きます。
2. ローカル ネットワーク サイトをファイルに追加します。 任意の有効なアドレス プレフィックスを使用できます。 VPN ゲートウェイに使用する任意の有効な IP アドレスを追加してください。 このセクションのアドレス値は ExpressRoute の動作上は使用されませんが、ファイルの検証に必要となります。 この例では、"branch1" がサイトの名前です。 別の名前を使用することもできますが、このファイルの Gateway セクションでも必ず同じ値を使用してください。

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. VirtualNetworkSites に移動して、その各フィールドに変更を加えます。

   * お使いの仮想ネットワークのゲートウェイ サブネットが存在することを確認します。 存在しなければ、ここで追加してください。 "GatewaySubnet" という名前にする必要があります。
   * ファイルに Gateway セクションが存在することを確認します。 存在しなければ追加してください。 仮想ネットワークを (接続先のネットワークを表す) ローカル ネットワーク サイトに関連付けるために必要となります。
   * Connection type が Dedicated になっていることを確認します。 ExpressRoute 接続では、これが必須となります。

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. ファイルを保存して Azure にアップロードします。

### <a name="create-the-gateway"></a>ゲートウェイを作成する

ゲートウェイを作成するには、次のコマンドを使用します。 指定する値は、実際の値に置き換えてください。

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>ゲートウェイが作成されていることの確認

次のコマンドを使用して、ゲートウェイが作成されていることを確認します。 このコマンドは、他の操作に必要なゲートウェイ ID も取得します。

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>ゲートウェイのサイズを変更する

[ゲートウェイ SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)には複数の種類があります。 次のコマンドを使用して、ゲートウェイ SKU をいつでも変更できます。

> [!IMPORTANT]
> このコマンドは UltraPerformance ゲートウェイでは使用できません。 ゲートウェイを UltraPerformance ゲートウェイに変更するには、既存の ExpressRoute ゲートウェイを削除してから、新しい UltraPerformance ゲートウェイを作成します。 ゲートウェイを UltraPerformance ゲートウェイからダウングレードするには、UltraPerformance ゲートウェイを削除してから、新しいゲートウェイを作成します。
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>ゲートウェイを削除する

ゲートウェイを削除するには、次のコマンドを使用します。

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```