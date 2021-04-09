---
title: 仮想ネットワーク ゲートウェイの削除:Azure クラシック
description: クラシック デプロイ モデルで、PowerShell を使用して仮想ネットワーク ゲートウェイを削除します。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 39585a68c5cddc50cd04e82caca71209270f7b68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91874120"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>PowerShell を使用して仮想ネットワーク ゲートウェイを削除する (クラシック)

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [クラシック - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

この記事は、クラシック デプロイ モデルで、PowerShell を使用して VPN ゲートウェイを削除するのに役立ちます。 仮想ネットワーク ゲートウェイを削除したら、ネットワーク構成ファイルを変更して、使用しなくなった要素を削除します。

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>手順 1:Azure に接続する

### <a name="1-install-the-latest-powershell-cmdlets"></a>1.最新の PowerShell コマンドレットをインストールします。

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2.Azure アカウントに接続します。

管理者特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

1. 管理者特権で PowerShell コンソールを開きます。
2. アカウントに接続します。 接続については、次の例を参考にしてください。

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>ステップ 2: ネットワーク構成ファイルのエクスポートと表示

コンピューターにディレクトリを作成し、ネットワーク構成ファイルをそのディレクトリにエクスポートします。 このファイルを使用して、現在の構成情報を表示するだけでなく、ネットワーク構成を変更することもできます。

次の例では、ネットワーク構成ファイルは C:\AzureNet にエクスポートされます。

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

テキスト エディターでファイルを開き、クラシック VNet の名前を確認します。 Azure Portal で VNet を作成するときには、Azure で使用される完全名はポータルには表示されません。 たとえば、Azure Portal に "ClassicVNet1" という名前で表示されている VNet は、ネットワーク構成ファイルではかなり長い名前である可能性があります。 "Group ClassicRG1 ClassicVNet1" のような名前の場合があります。 仮想ネットワーク名は、**'VirtualNetworkSite name ='** と示されています。 PowerShell コマンドレットを実行するときは、ネットワーク構成ファイルの名前を使用します。

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>ステップ 3: 仮想ネットワーク ゲートウェイの削除

仮想ネットワーク ゲートウェイを削除すると、そのゲートウェイ経由での VNet への接続がすべて切断されます。 VNet に接続されている P2S クライアントがある場合、警告なしに切断されます。

次の例では、仮想ネットワーク ゲートウェイを削除します。 必ずネットワーク構成ファイルの仮想ネットワークの完全名を使用します。

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

削除が正常に完了すると、次のように表示されます。

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>ステップ 4: ネットワーク構成ファイルの変更

仮想ネットワーク ゲートウェイを削除したときに、コマンドレットではネットワーク構成ファイルは変更されません。 構成ファイルを変更して、使用されなくなった要素を削除する必要があります。 以下のセクションは、ダウンロードしたネットワーク構成ファイルを変更する際に役立ちます。

### <a name="local-network-site-references"></a><a name="lnsref"></a>ローカル ネットワーク サイトの参照

サイトの参照情報を削除するには、**ConnectionsToLocalNetwork/LocalNetworkSiteRef** の構成を変更します。 ローカル サイトの参照を削除すると、Azure によってトンネルが削除されます。 作成済みの構成によっては、**LocalNetworkSiteRef** が表示されない場合があります。

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

例:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

### <a name="local-network-sites"></a><a name="lns"></a>ローカル ネットワーク サイト

使用しなくなったすべてのローカル サイトを削除します。 作成済みの構成により、**LocalNetworkSite** が表示されない可能性があります。

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

次の例では、Site3 だけを削除しています。

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="client-addresspool"></a><a name="clientaddresss"></a>クライアントの AddressPool

VNet に P2S 接続していた場合、**VPNClientAddressPool** があります。 削除した仮想ネットワーク ゲートウェイに対応するクライアント アドレス プールを削除します。

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

例:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gatewaysubnet"></a><a name="gwsub"></a>GatewaySubnet

VNet に対応する **GatewaySubnet** を削除します。

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

例:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>ステップ 5: ネットワーク構成ファイルのアップロード

変更を保存し、ネットワーク構成ファイルを Azure にアップロードします。 必要に応じて、ファイル パスを環境に合わせて変更してください。

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

アップロードが正常に完了すると、次の例のように表示されます。

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
