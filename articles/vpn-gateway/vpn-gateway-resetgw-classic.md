---
title: IPsec トンネルを再確立するための Azure VPN Gateway のリセット | Microsoft Docs
description: この記事では、Azure VPN Gateway をリセットして IPsec トンネルを再確立する方法を紹介します。 この記事は、クラシック デプロイ モデルと Resource Manager デプロイ モデルの両方の VPN ゲートウェイに適用されます。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc
ms.openlocfilehash: 9744a4b7bc5d2e9ce22bfa14ea33a2b11dacda85
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612468"
---
# <a name="reset-a-vpn-gateway"></a>VPN Gateway のリセット

1 つ以上のサイト間 VPN トンネルのクロスプレミス VPN 接続が失われた場合、Azure VPN Gateway をリセットすることによって解決できる場合があります。 この状況では、オンプレミスの VPN デバイスがすべて正しく機能していても、Azure VPN Gateway との間で IPsec トンネルを確立することができません。 この記事では、VPN ゲートウェイをリセットすることができます。

### <a name="what-happens-during-a-reset"></a>リセット中の動作

VPN Gateway は、アクティブ/スタンバイ構成で動作する 2 つの VM インスタンスから成ります。 ゲートウェイをリセットすると、ゲートウェイが再起動してクロスプレミス構成が再適用されます。 ゲートウェイに割り当てられているパブリック IP アドレスはそのまま維持されます。 つまり、VPN ルーターの構成を Azure VPN Gateway の新しいパブリック IP アドレスで更新する必要がありません。

このコマンドを実行してゲートウェイをリセットすると、現在アクティブな Azure VPN Gateway のインスタンスが直ちに再起動されます。 再起動中のアクティブ インスタンスからスタンバイ インスタンスにフェールオーバーされる際に、わずかな時間差が生じます。 通常、時間差は 1 分未満です。

初回再起動で接続が復元されない場合は、同じコマンドを再度実行して、2 つ目の VM インスタンス (新しくアクティブになった方のゲートウェイ) を再起動してください。 連続して 2 回の再起動が必要であった場合、アクティブとスタンバイの両方の VM インスタンスを再起動する分、1 回で済んだ場合よりもわずかに時間がかかります。 この場合、VPN 接続の途切れが長引いて、両方の VM が再起動を完了するまでに最大 30 ～ 45 分かかります。

2 回再起動してもクロスプレミス接続の問題が解消しない場合は、Azure ポータルからサポート リクエストを作成してください。

## <a name="before"></a>開始する前に

ゲートウェイをリセットする前に、個々の IPsec サイト間 (S2S) VPN トンネルについて、以下に挙げた主な項目を確認してください。 いずれか 1 つの項目でも不備があると、S2S VPN トンネルの接続が失われます。 オンプレミスの VPN ゲートウェイと Azure VPN Gateway に使用されている構成を確認して修正すれば、そのゲートウェイ上で正常に機能している他の接続に対して無駄な再起動や中断を行わずに済みます。

ゲートウェイをリセットする前に以下の項目をご確認ください。

* Azure とオンプレミスの両方の VPN ポリシーに、Azure VPN Gateway とオンプレミスの VPN ゲートウェイのインターネット IP アドレス (VIP) がどちらも正しく構成されていること。
* Azure の VPN ゲートウェイとオンプレミスの VPN ゲートウェイが同じ事前共有キーを持っていること。
* 暗号化、ハッシュ アルゴリズム、PFS (Perfect Forward Secrecy) など特定の IPsec/IKE 構成を適用する場合、Azure の VPN ゲートウェイとオンプレミスの VPN ゲートウェイとに、必ず同じ構成を適用すること。

## <a name="portal"></a>Azure Portal

Azure Portal を使用して Resource Manager VPN Gateway をリセットできます。 クラシック ゲートウェイをリセットする場合は、[PowerShell](#resetclassic) の手順を参照してください。

### <a name="resource-manager-deployment-model"></a>Resource Manager デプロイ モデル

1. [Azure Portal](https://portal.azure.com) を開いて、リセットする Resource Manager 仮想ネットワーク ゲートウェイに移動します。
2. 仮想ネットワーク ゲートウェイのブレードで、[リセット] をクリックします。

   ![Reset VPN Gateway blade](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. [リセット] ブレードで、 **[リセット]** ボタンをクリックします。

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Resource Manager デプロイ モデル

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

ゲートウェイをリセットするコマンドレットは **Reset-AzVirtualNetworkGateway** です。 リセットを実行する前に [PowerShell Az コマンドレット](https://docs.microsoft.com/powershell/module/az.network)の最新バージョンがあることを確認します。 次の例では、TestRG1 リソース グループの VNet1GW という名前の仮想ネットワーク ゲートウェイをリセットします。

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

結果:

返された結果を受け取ったら、ゲートウェイのリセットが成功したとみなすことができます。 ただし、返された結果にはリセットが成功したことをはっきりと示すものは何もありません。 ゲートウェイのリセットが発生した正確な時間の履歴を詳細に調査したい場合は、[Azure Portal](https://portal.azure.com) でその情報を見ることができます。 ポータル上で、 **'GatewayName' から [リソース正常性]** に移動します。

### <a name="resetclassic"></a>クラシック デプロイ モデル

ゲートウェイをリセットするためのコマンドレットは **Reset-AzureVNetGateway** です。 リセットを実行する前に [Service Management (SM) PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) の最新版があることを確認します。 次の例では、"ContosoVNet" という仮想ネットワークのゲートウェイをリセットしています。

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
```

結果:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="cli"></a>Azure CLI

ゲートウェイをリセットするには [az network vnet-gateway reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway) コマンドを使用します。 次の例では、TestRG5 リソース グループの VNet5GW という名前の仮想ネットワーク ゲートウェイをリセットします。

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

結果:

返された結果を受け取ったら、ゲートウェイのリセットが成功したとみなすことができます。 ただし、返された結果にはリセットが成功したことをはっきりと示すものは何もありません。 ゲートウェイのリセットが発生した正確な時間の履歴を詳細に調査したい場合は、[Azure Portal](https://portal.azure.com) でその情報を見ることができます。 ポータル上で、 **'GatewayName' から [リソース正常性]** に移動します。
