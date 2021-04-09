---
title: 'オンプレミス ネットワークから Azure Virtual Network への接続:サイト間 VPN (クラシック): ポータル | Microsoft Docs'
description: パブリック インターネットを経由したオンプレミスのネットワークからクラシック Azure 仮想ネットワークへの IPsec 接続を作成します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 4ad05281f13885327c855a261a3101388f38af83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878055"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Azure Portal を使用してサイト間接続を作成する (クラシック)

この記事では、Azure Portal を使用して、オンプレミス ネットワークから VNet へのサイト間 VPN Gateway 接続を作成する方法について説明します。 この記事の手順は、クラシック デプロイ モデルに適用され、現在のデプロイ モデルである Resource Manager には適用されません。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Azure Portal](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

サイト間 VPN Gateway 接続は、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介してオンプレミス ネットワークを Azure 仮想ネットワークに接続するために使用します。 この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの VPN デバイスが必要です。 VPN Gateway の詳細については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。

![クロスプレミスのサイト間 VPN Gateway 接続の図](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>開始する前に

構成を開始する前に、以下の条件を満たしていることを確認します。

* クラシック デプロイ モデルで作業することを確認します。 Resource Manager デプロイ モデルで作業する場合は、[サイト間接続の作成 (Resource Manager)](./tutorial-site-to-site-portal.md) に関する記事を参照してください。 Resource Manager デプロイ モデルはレガシーであるため、このクラシック モデルを使用することをお勧めします。
* 互換性のある VPN デバイスがあり、デバイスを構成できる人員がいることを確認します。 互換性のある VPN デバイスとデバイスの構成の詳細については、[VPN デバイスの概要](vpn-gateway-about-vpn-devices.md)に関する記事を参照してください。
* VPN デバイスの外部接続用パブリック IPv4 アドレスがあることを確認します。
* オンプレミス ネットワーク構成の IP アドレス範囲を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。 この構成を作成する場合は、Azure がオンプレミスの場所にルーティングする IP アドレス範囲のプレフィックスを指定する必要があります。 オンプレミス ネットワークのサブネットと接続先の仮想ネットワーク サブネットが重複しないようにしなければなりません。
* 共有キーの指定と VPN ゲートウェイ接続の作成を行うには、PowerShell が必要です。 [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>この演習のサンプル構成値

この記事の例では、次の値を使用します。 この値を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。 通常、アドレス空間の IP アドレス値を操作する場合は、ルーティングに影響する可能性があるアドレス空間の重複を避けるためにネットワーク管理者と調整する必要があります。 この場合、動作する接続を作成したければ、IP アドレスの値を独自の値に置き換えてください。

* **[リソース グループ]:** TestRG1
* **VNet 名:** TestVNet1
* **[アドレス空間]:** 10.11.0.0/16
* **サブネット名:** FrontEnd
* **サブネットのアドレス範囲:** 10.11.0.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **[リージョン]:** (米国) 米国東部
* **ローカル サイト名:** Site2
* **[クライアント アドレス空間]:** オンプレミスのサイトにあるアドレス空間。

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>仮想ネットワークの作成

S2S 接続に使用する仮想ネットワークを作成する際には、指定するアドレス空間が、接続先のローカル サイトのクライアント アドレス空間と重複しないようにする必要があります。 サブネットの重複があると、接続が適切に動作しません。

* 既に VNet がある場合は、設定が VPN ゲートウェイの設計に適合していることを確認します。 特に、他のネットワークと重複している可能性のあるサブネットに注意してください。

* まだ仮想ネットワークがない場合は作成します。 スクリーンショットは例として示されています。 サンプルの値は必ず実際の値に変更してください。

### <a name="to-create-a-virtual-network"></a>仮想ネットワークを作成するには

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>サイトとゲートウェイを構成する

### <a name="to-configure-the-site"></a>サイトを構成するには

通常、ローカル サイトとはオンプレミスの場所を指します。 ここには、接続の作成先となる VPN デバイスの IP アドレスのほか、VPN ゲートウェイ経由で VPN デバイスにルーティングされる IP アドレス範囲が含まれます。

1. ご自分の VNet 用のページで、 **[設定]** の下の **[サイト間接続]** を選択します。
1. [サイト間接続] ページで、 **[+ 追加]** を選択します。
1. **[VPN 接続とゲートウェイの構成]** ページで、 **[接続の種類]** には **[サイト間接続]** が選択されたままにします。 この演習では、[サンプル値](#values)と独自の値の組み合わせを使用する必要があります。

   * **[VPN ゲートウェイの IP アドレス]:** これは、オンプレミス ネットワークの VPN デバイスのパブリック IP アドレスです。 VPN デバイスには IPv4 パブリック IP アドレスが必要です。 接続先となる VPN デバイスに有効なパブリック IP アドレスを指定します。 これは Azure で到達可能である必要があります。 VPN デバイスの IP アドレスが不明な場合は、(有効なパブリック IP アドレスの形式で) プレースホルダーの値を入力しておき、後で変更することができます。

   * **[クライアント アドレス空間]:** このゲートウェイ経由でオンプレミスのローカル ネットワークにルーティングする IP アドレス範囲を一覧表示します。 複数のアドレス領域の範囲を追加することができます。 ここで指定する範囲は、仮想ネットワークの接続先となる他のネットワークの範囲、または仮想ネットワーク自体のアドレス範囲と重複しないようにしてください。
1. ページの下部にある [確認と作成] を選択しないでください。 代わりに、 **[次へ: ゲートウェイ>]** を選択します。

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>仮想ネットワーク ゲートウェイを構成するには

1. **[ゲートウェイ]** ページで、次の値を選択します。

   * **Size:** これは、仮想ネットワーク ゲートウェイの作成に使用するゲートウェイ SKU です。 クラシック VPN ゲートウェイでは、古い (レガシ) ゲートウェイ SKU が使用されます。 レガシ ゲートウェイ SKU の詳細については、[仮想ネットワーク ゲートウェイ SKU (古い SKU) の利用](vpn-gateway-about-skus-legacy.md)に関するページを参照してください。 この演習では、 **[Standard]** を選択できます。

   * **ルーティングの種類:** お使いのゲートウェイのルーティングの種類を選択します。 これは VPN の種類とも呼ばれます。 ゲートウェイの種類は切り替えることができないため、適切な種類を選択することが重要です。 VPN デバイスは、選択するルーティングの種類に適合している必要があります。 ルーティングの種類の詳細については、「[VPN の種類](vpn-gateway-about-vpn-gateway-settings.md#vpntype)」を参照してください。 "RouteBased" と "PolicyBased" という VPN の種類の項目がありますが、 "動的" は "RouteBased"、"静的" は "PolicyBased" に対応しています。 通常は、動的ルーティングにします。

   * **ゲートウェイ サブネット:** 指定したゲートウェイ サブネットのサイズは、作成する VPN ゲートウェイの構成によって異なります。 /29 のような小さいゲートウェイ サブネットを作成できますが、/27 または /28 を使用することをお勧めします。 これにより、より多くのアドレスを含む大きなサブネットが作成されます。 大規模なゲートウェイ サブネットを使用すると、将来の構成に対応するのに十分な IP アドレスを確保できます。

1. ページの下部にある **[確認と作成]** を選択して、設定を検証します。 **[作成]** を選択してデプロイします。 選択した SKU によっては、仮想ネットワーク ゲートウェイを作成するまでに最大で 45 分かかる可能性があります。

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>VPN デバイスの構成

オンプレミス ネットワークとのサイト間接続には VPN デバイスが必要です。 この手順では、VPN デバイスを構成します。 VPN デバイスを構成する場合は、次の値が必要です。

* 共有キー。 サイト間 VPN 接続を作成するときにも、これと同じ共有キーを指定します。 ここで紹介している例では、基本的な共有キーを使用しています。 実際には、もっと複雑なキーを生成して使用することをお勧めします。
* 仮想ネットワーク ゲートウェイのパブリック IP アドレス。 パブリック IP アドレスは、Azure Portal、PowerShell、または CLI を使用して確認できます。

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>値を取得する

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>接続を作成する

> [!NOTE]
> クラシック デプロイ モデルの場合、この手順は Azure portal または Azure Cloud Shell では使用できません。 デスクトップから Azure PowerShell コマンドレットのサービス管理 (SM) バージョンをローカルで使用する必要があります。
>

この手順では、前の手順の値を使用して共有キーを設定し、接続を作成します。 設定するキーは、VPN デバイスの構成で使用したものと同じである必要があります。

1. 共有キーを設定して接続を作成します。

   * -VNetName 値と -LocalNetworkSiteName 値を変更します。 スペースを含む名前を指定するときは、単一引用符を使って値を囲みます。
   * "-SharedKey" は、生成して指定する値です。 この例では "abc123" を使いましたが、さらに複雑な値を使うこともできます (推奨)。 重要なのは、ここで指定する値は、VPN デバイスを構成する際に指定した値と同じにする必要があることです。

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. 接続が作成されたら、結果として **Status: Successful** を示している必要があります。

## <a name="verify-your-connection"></a><a name="verify"></a>接続を確認する

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

接続に問題がある場合は、左ペインの目次の「**トラブルシューティング**」セクションを参照してください。

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>VPN ゲートウェイをリセットする方法

1 つ以上のサイト間 VPN トンネルのクロスプレミス VPN 接続が失われた場合、Azure VPN Gateway をリセットすることによって解決できる場合があります。 この状況では、オンプレミスの VPN デバイスがすべて正しく機能していても、Azure VPN Gateway との間で IPsec トンネルを確立することができません。 手順については、「[VPN Gateway のリセット](./reset-gateway.md#resetclassic)」を参照してください。

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>ゲートウェイ SKU を変更する方法

ゲートウェイ SKU を変更する手順については、[ゲートウェイ SKU のサイズ変更](vpn-gateway-about-SKUS-legacy.md#classicresize)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* 接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](../index.yml) に関するページを参照してください。
* 強制トンネリングについては、[強制トンネリング](vpn-gateway-about-forced-tunneling.md)に関する記事を参照してください。