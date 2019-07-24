---
title: 仮想ネットワーク ゲートウェイを削除する:PowerShell:Azure クラシック | Microsoft Docs
description: クラシック デプロイ モデルで、PowerShell を使用して仮想ネットワーク ゲートウェイを削除します。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: ca014e4f5fbc4a5695dbc5fedc85826c71a2a906
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767772"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>PowerShell を使用して仮想ネットワーク ゲートウェイを削除する (クラシック)

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [クラシック - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

この記事は、クラシック デプロイ モデルで、PowerShell を使用して VPN ゲートウェイを削除するのに役立ちます。 仮想ネットワーク ゲートウェイを削除したら、ネットワーク構成ファイルを変更して、使用しなくなった要素を削除します。

## <a name="connect"></a>手順 1:Azure への接続

### <a name="1-install-the-latest-powershell-cmdlets"></a>1.最新の PowerShell コマンドレットをインストールします。

Azure サービス管理 (SM) PowerShell コマンドレットの最新バージョンをダウンロードしてインストールします。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。

### <a name="2-connect-to-your-azure-account"></a>2.Azure アカウントに接続します。 

管理者特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

```powershell
Add-AzureAccount
```

## <a name="export"></a>手順 2:ネットワーク構成ファイルのエクスポートと表示

コンピューターにディレクトリを作成し、ネットワーク構成ファイルをそのディレクトリにエクスポートします。 このファイルを使用して、現在の構成情報を表示するだけでなく、ネットワーク構成を変更することもできます。

次の例では、ネットワーク構成ファイルは C:\AzureNet にエクスポートされます。

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

テキスト エディターでファイルを開き、クラシック VNet の名前を確認します。 Azure Portal で VNet を作成するときには、Azure で使用される完全名はポータルには表示されません。 たとえば、Azure Portal に "ClassicVNet1" という名前で表示されている VNet は、ネットワーク構成ファイルではかなり長い名前である可能性があります。 名前は次のように表示されます。"Group ClassicRG1 ClassicVNet1"。 仮想ネットワーク名は、**'VirtualNetworkSite name ='** と示されています。 PowerShell コマンドレットを実行するときは、ネットワーク構成ファイルの名前を使用します。

## <a name="delete"></a>手順 3:仮想ネットワーク ゲートウェイの削除

仮想ネットワーク ゲートウェイを削除すると、そのゲートウェイ経由での VNet への接続がすべて切断されます。 VNet に接続されている P2S クライアントがある場合、警告なしに切断されます。

次の例では、仮想ネットワーク ゲートウェイを削除します。 必ずネットワーク構成ファイルの仮想ネットワークの完全名を使用します。

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

削除が正常に完了すると、次のように表示されます。

```
Status : Successful
```

## <a name="modify"></a>手順 4:ネットワーク構成ファイルの変更

仮想ネットワーク ゲートウェイを削除したときに、コマンドレットではネットワーク構成ファイルは変更されません。 構成ファイルを変更して、使用されなくなった要素を削除する必要があります。 以下のセクションは、ダウンロードしたネットワーク構成ファイルを変更する際に役立ちます。

### <a name="lnsref"></a>ローカル ネットワーク サイトの参照

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

### <a name="lns"></a>ローカル ネットワーク サイト

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

### <a name="clientaddresss"></a>クライアントの AddressPool

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

### <a name="gwsub"></a>GatewaySubnet

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

## <a name="upload"></a>手順 5: ネットワーク構成ファイルのアップロード

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
