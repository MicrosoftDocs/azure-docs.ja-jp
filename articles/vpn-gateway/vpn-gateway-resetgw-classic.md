---
title: "IPsec トンネルを再確立するための Azure VPN Gateway のリセット | Microsoft Docs"
description: "この記事では、Azure VPN Gateway をリセットして IPsec トンネルを再確立する方法を紹介します。 この記事は、クラシック デプロイメント モデルと Resource Manager デプロイメント モデルの両方の VPN ゲートウェイに適用されます。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 79d77cb8-d175-4273-93ac-712d7d45b1fe
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1c93a8900ea5fae8abe0d2d47f632a067736ac56
ms.openlocfilehash: 7aef9360ab341dd7d4932a6e9c2d8ed1d7bf1163
ms.lasthandoff: 02/08/2017


---
# <a name="reset-a-vpn-gateway"></a>VPN Gateway のリセット

サイト間 VPN トンネルのクロスプレミス VPN 接続が失われた場合、Azure VPN Gateway をリセットすることによって解決できる場合があります。 この状況では、オンプレミスの VPN デバイスがすべて正しく機能していても、Azure VPN Gateway との間で IPsec トンネルを確立することができません。 この記事では、Azure VPN Gateway をリセットする方法を紹介します。 

各 Azure VPN Gateway は、アクティブ/スタンバイ構成で動作する&2; つの VM インスタンスから成る仮想ネットワーク ゲートウェイです。 ゲートウェイをリセットすると、ゲートウェイが再起動してクロスプレミス構成が再適用されます。 ゲートウェイに割り当てられているパブリック IP アドレスはそのまま維持されます。 つまり、VPN ルーターの構成を Azure VPN Gateway の新しいパブリック IP アドレスで更新する必要がありません。  

このコマンドを実行すると、現在アクティブな Azure VPN Gateway のインスタンスが直ちに再起動されます。 再起動中のアクティブ インスタンスからスタンバイ インスタンスにフェールオーバーされる際に、わずかな時間差が生じます。 通常、時間差は&1; 分未満です。

初回再起動で接続が復元されない場合は、同じコマンドを再度実行して、2 つ目の VM インスタンス (新しくアクティブになった方のゲートウェイ) を再起動してください。 連続して&2; 回の再起動が必要であった場合、アクティブとスタンバイの両方の VM インスタンスを再起動する分、1 回で済んだ場合よりもわずかに時間がかかります。 この場合、VPN 接続の途切れが長引いて、両方の VM が再起動を完了するまでに最大 2 ～ 4 分かかります。

2 回再起動してもクロスプレミス接続の問題が解消しない場合は、Azure ポータルからサポート リクエストを作成してください。

## <a name="before-you-begin"></a>開始する前に
ゲートウェイをリセットする前に、個々の IPsec サイト間 (S2S) VPN トンネルについて、以下に挙げた主な項目を確認してください。 いずれか&1; つの項目でも不備があると、S2S VPN トンネルの接続が失われます。 オンプレミスの VPN ゲートウェイと Azure VPN Gateway に使用されている構成を確認して修正すれば、そのゲートウェイ上で正常に機能している他の接続に対して無駄な再起動や中断を行わずに済みます。

ゲートウェイをリセットする前に以下の項目をご確認ください。

* Azure とオンプレミスの両方の VPN ポリシーに、Azure VPN Gateway とオンプレミスの VPN ゲートウェイのインターネット IP アドレス (VIP) がどちらも正しく構成されていること。
* Azure の VPN ゲートウェイとオンプレミスの VPN ゲートウェイが同じ事前共有キーを持っていること。
* 暗号化、ハッシュ アルゴリズム、PFS (Perfect Forward Secrecy) など特定の IPsec/IKE 構成を適用する場合、Azure の VPN ゲートウェイとオンプレミスの VPN ゲートウェイとに、必ず同じ構成を適用すること。

## <a name="reset-a-vpn-gateway-using-the-azure-portal"></a>Azure Portal を使用して VPN Gateway をリセットする

Azure Portal を使用して Resource Manager VPN Gateway をリセットできます。 クラシック ゲートウェイをリセットする場合は、[PowerShell](#resetclassic) の手順を参照してください。

### <a name="resource-manager-deployment-model"></a>リソース マネージャーのデプロイ モデル

1. Azure Portal を開いて、リセットする Resource Manager 仮想ネットワーク ゲートウェイに移動します。
2. 仮想ネットワーク ゲートウェイのブレードで、[リセット] をクリックします。

    ![Reset VPN Gateway blade](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)

3. [リセット] ブレードで、 ![Reset VPN Gateway blade](./media/vpn-gateway-howto-reset-gateway/reset-button.png) ボタンを選択します。


## <a name="reset-a-vpn-gateway-using-powershell"></a>PowerShell を使用して VPN Gateway をリセットする

### <a name="resource-manager-deployment-model"></a>リソース マネージャーのデプロイ モデル

最新バージョンの PowerShell コマンドレットが必要になります。 詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」ご覧ください。 ゲートウェイをリセットするための PowerShell Resource Manager コマンドレットは、 `Reset-AzureRmVirtualNetworkGateway`です。 次の例では、リソース グループ "TestRG1" の Azure VPN ゲートウェイ "VNet1GW" をリセットします。

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

### <a name="a-nameresetclassicaclassic-deployment-model"></a><a name="resetclassic"></a>クラシック デプロイ モデル

最新バージョンの PowerShell コマンドレットが必要になります。 詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」ご覧ください。 Azure VPN Gateway をリセットするための PowerShell コマンドレットは `Reset-AzureVNetGateway`です。 次の例では、"ContosoVNet" という仮想ネットワークの Azure VPN Gateway をリセットしています。

    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

結果は次のとおりです。

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK



