---
title: ポイント対サイトと RADIUS 認証を使用してコンピューターを仮想ネットワークに接続する:PowerShell | Azure
description: P2S および RADIUS 認証を使って、Windows クライアントと Mac OS X クライアントを仮想ネットワークに安全に接続します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: cherylmc
ms.openlocfilehash: 1096c120b4e7731fabd574c4096e70fe02b6272d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66146995"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>RADIUS 認証を使用して VNet へのポイント対サイト接続を構成する:PowerShell

この記事では、RADIUS 認証を使用するポイント対サイト接続を備えた VNet を作成する方法について説明します。 この構成は、Resource Manager デプロイ モデルについてのみ使用できます。

ポイント対サイト (P2S) VPN ゲートウェイでは、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。 ポイント対サイト VPN 接続は、自宅や会議室でのテレワークなど、リモートの場所から VNet に接続する場合に便利です。 P2S VPN は、VNet への接続が必要なクライアントがごく少ない場合に、サイト対サイト VPN の代わりに使用するソリューションとしても便利です。

P2S VPN 接続は、Windows デバイスと Mac デバイスから開始されます。 接続するクライアントでは、次の認証方法を使用できます。 

* RADIUS サーバー
* VPN Gateway のネイティブ証明書認証

この記事は、RADIUS サーバーによる認証を使用して P2S 構成を設定する際に役立ちます。 生成された証明書と VPN ゲートウェイ ネイティブ証明書認証を使用して認証を行いたい場合、[VPN ゲートウェイ ネイティブ証明書認証を使用した VNet へのポイント対サイト接続の構成](vpn-gateway-howto-point-to-site-rm-ps.md)に関するページを参照してください。

![接続図 - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

ポイント対サイト接続に、VPN デバイスや公開 IP アドレスは必要ありません。 P2S により、SSTP (Secure Socket トンネリング プロトコル) または IKEv2 経由の VPN 接続が作成されます。

* SSTP は、Windows クライアント プラットフォームでのみサポートされる SSL ベースの VPN トンネルです。 ファイアウォールを通過できるため、接続元の場所を問わず Azure に接続する際の理想的なオプションとなっています。 サーバー側でのサポート対象の SSTP バージョンは、1.0、1.1、1.2 です。 使用するバージョンはクライアントによって決まります。 Windows 8.1 以降の場合、SSTP では既定で 1.2 が使用されます。

* IKEv2 VPN。これは、標準ベースの IPsec VPN ソリューションです。 IKEv2 VPN は、Mac デバイス (OSX バージョン 10.11 以上) から接続する際に使用できます。

P2S 接続には、以下のものが必要です。

* RouteBased VPN ゲートウェイ。 
* ユーザー認証を処理する RADIUS サーバー。 RADIUS サーバーは、オンプレミスまたは Azure VNet にデプロイできます。
* VNet に接続する Winodows デバイスの VPN クライアント構成パッケージ。 VPN クライアント構成パッケージには、VPN クライアントが P2S を介して接続するために必要な設定が含まれています。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="aboutad"></a>P2S VPN の Active Directory (AD) ドメイン認証について

AD ドメイン認証では、ユーザーは組織のドメイン資格情報を使用して Azure にサインインできます。 これには AD サーバーと統合する RADIUS サーバーが必要です。 また、組織は既存の RADIUS デプロイを利用することもできます。
 
RADIUS サーバーは、オンプレミスまたは Azure VNet に配置できます。 認証が行われる間、VPN ゲートウェイがパススルーとして機能し、接続するデバイスと RADIUS サーバーの間で認証メッセージを転送します。 重要なのは、VPN ゲートウェイが RADIUS サーバーにアクセスできることです。 RADIUS サーバーがオンプレミスに存在する場合、Azure からオンプレミス サイトへの VPN サイト間接続が必要になります。

RADIUS サーバーは、Active Directory とは別に、その他の外部 ID システムとも統合できます。 これにより、MFA のオプションなど、ポイント対サイト VPN 向けの多数の認証オプションを利用できるようになります。 RADIUS サーバーと統合される ID システムの一覧を取得するには、そのベンダーのドキュメントをご確認ください。

![接続図 - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>オンプレミスの RADIUS サーバーへの接続に使用できるのは、VPN サイト間接続のみです。 ExpressRoute 接続は使用できません。
>
>

## <a name="before"></a>作業を開始する前に

Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example"></a>値の例

値の例を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。 手順をチュートリアルとして利用して値を変更せずに使用することも、実際の環境に合わせて値を変更することもできます。

* **名前:VNet1**
* **アドレス空間:192.168.0.0/16** および **10.254.0.0/16**<br>この例では、この構成が複数のアドレス空間で機能することを示すために、複数のアドレス空間を使用します。 ただし、この構成で複数のアドレス空間は必須ではありません。
* **サブネット名:FrontEnd**
  * **サブネットのアドレス範囲:192.168.1.0/24**
* **サブネット名:BackEnd**
  * **サブネットのアドレス範囲:10.254.1.0/24**
* **サブネット名:GatewaySubnet**<br>VPN ゲートウェイが機能するには、サブネット名 *GatewaySubnet* が必須となります。
  * **GatewaySubnet のアドレス範囲:192.168.200.0/24** 
* **VPN クライアント アドレス プール:172.16.201.0/24**<br>このポイント対サイト接続を利用して VNet に接続する VPN クライアントは、この VPN クライアント アドレス プールから IP アドレスを受け取ります。
* **サブスクリプション:** サブスクリプションが複数ある場合は、適切なサブスクリプションを使用していることを確認します。
* **リソース グループ:TestRG**
* **場所:米国東部**
* **DNS サーバー:** VNet の名前解決に利用する DNS サーバーの IP アドレス。 (省略可能)
* **GW 名:Vnet1GW**
* **パブリック IP 名:VNet1GWPIP**
* **VPN の種類:RouteBased**


## <a name="signin"></a>サインインと変数の設定

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="declare-variables"></a>変数の宣言

使用する変数を宣言します。 次のサンプルを使用し、必要に応じて独自の値で置き換えます。 演習中の任意の時点で PowerShell/Cloud Shell セッションを閉じた場合は、値をもう一度コピーして貼り付けるだけで、変数を再宣言します。

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## 1.<a name="vnet"></a>リソース グループ、VNet、パブリック IP アドレスの作成

次の手順では、1 つのリソース グループを作成し、3 つのサブネットと共に仮想ネットワークをそのリソース グループに作成します。 値を代入する場合は、ゲートウェイ サブネットの名前を必ず "GatewaySubnet" にすることが重要です。 別の名前にすると、ゲートウェイの作成は失敗します。

1. リソース グループを作成します。

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. 仮想ネットワークのサブネット構成を作成し、*FrontEnd*、*BackEnd*、*GatewaySubnet* いう名前を付けます。 これらのプレフィックスは、宣言した VNet アドレス空間に含まれている必要があります。

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. 仮想ネットワークを作成します。

   この例では、-DnsServer サーバー パラメーターはオプションです。 値を指定しても新しい DNS サーバーは作成されません。 指定する DNS サーバーの IP アドレスは、VNet から接続するリソースの名前を解決できる DNS サーバーの IP アドレスである必要があります。 この例ではプライベート IP アドレスを使用していますが、これはおそらく実際の DNS サーバーの IP アドレスと一致しません。 実際には独自の値を使用してください。 指定された値は、P2S 接続ではなく、VNet にデプロイするリソースが使用します。

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. VPN ゲートウェイには、パブリック IP アドレスが必要です。 これにはまず IP アドレスのリソースを要求したうえで、仮想ネットワーク ゲートウェイの作成時にそのリソースを参照する必要があります。 IP アドレスは、VPN ゲートウェイの作成時にリソースに対して動的に割り当てられます。 VPN Gateway では現在、パブリック IP アドレスの "*動的*" 割り当てのみサポートしています。 静的パブリック IP アドレスの割り当てを要求することはできません。 もっとも、VPN ゲートウェイに割り当てられた IP アドレスが後から変わることは基本的にありません。 パブリック IP アドレスが変わるのは、ゲートウェイが削除され、再度作成されたときのみです。 VPN ゲートウェイのサイズ変更、リセット、その他の内部メンテナンス/アップグレードでは、IP アドレスは変わりません。

   動的に割り当てられたパブリック IP アドレスを要求する変数を指定します。

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## 2.<a name="radius"></a>RADIUS サーバーの設定

仮想ネットワーク ゲートウェイを作成して構成する前に、RADIUS サーバーを認証用に正しく構成しておく必要があります。

1. RADIUS サーバーをデプロイしていない場合はデプロイします。 デプロイ手順については、RADIUS ベンダーから提供されたセットアップ ガイドを参照してください。  
2. RADIUS で VPN ゲートウェイを RADIUS クライアントとして構成します。 この RADIUS クライアントを追加する際に、作成した仮想ネットワーク GatewaySubnet を指定します。 
3. RADIUS サーバーの設定が完了したら、RADIUS クライアントが RADIUS サーバーとの通信に使用する RADIUS サーバーの IP アドレスと共有シークレットを取得します。 RADIUS サーバーが Azure VNet 内にある場合、RADIUS サーバー VM の CA IP を使用します。

記事「[ネットワーク ポリシー サーバー (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)」では、AD ドメイン認証用に Windows RADIUS サーバー (NPS) を構成する方法についてのガイダンスが示されています。

## 3.<a name="creategw"></a>VPN ゲートウェイの作成

VPN ゲートウェイを VNet 用に構成して作成します。

* -GatewayType は "Vpn"、-VpnType は "RouteBased" にする必要があります。
* 選択する [ゲートウェイ SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)  によっては、VPN ゲートウェイの作成が完了するまでに最大で 45 分かかる場合があります。

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4.<a name="addradius"></a>RADIUS サーバーおよびクライアント アドレス プールの追加
 
* -RadiusServer は名前または IP アドレスで指定できます。 サーバーがオンプレミスに存在する場合に名前を指定すると、VPN ゲートウェイはその名前を解決できない可能性があります。 この場合、サーバーの IP アドレスを指定することをお勧めします。 
* -RadiusSecret は、RADIUS サーバーで構成したものと一致している必要があります。
* -VpnClientAddressPool は、接続する VPN クライアントが受け取る IP アドレスの範囲です。 接続元であるオンプレミスの場所、または接続先とする VNet と重複しないプライベート IP アドレス範囲を使用してください。 また、構成するアドレス プールには十分な広さを確保してください。  

1. RADIUS シークレットのセキュリティで保護された文字列を作成します。

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. RADIUS シークレットを入力するように求められます。 入力した文字は表示されず、"*" 文字に置き換えられます。

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. VPN クライアント アドレス プールと RADIUS サーバー情報を追加します。

   SSTP 構成の場合:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   IKEv2 構成の場合:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   SSTP と IKEv2 の場合

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 5.<a name="vpnclient"></a>VPN クライアント構成パッケージのダウンロードと VPN クライアントの設定

VPN クライアント構成を使用すると、デバイスは P2S 接続を介して VNet に接続できます。 VPN クライアント構成パッケージを生成して VPN クライアントを設定するには、[RADIUS 認証用の VPN クライアント構成の作成](point-to-site-vpn-client-configuration-radius.md)に関するページを参照してください。

## <a name="connect"></a>6.Azure への接続

### <a name="to-connect-from-a-windows-vpn-client"></a>Windows VPN クライアントから接続するには

1. VNet に接続するには、クライアント コンピューターで [VPN 接続] に移動し、作成した VPN 接続を見つけます。 仮想ネットワークと同じ名前が付いています。 ドメインの資格情報を入力して、[接続] をクリックします。 管理者特権を求めるポップアップ メッセージが表示されます。 同意して資格情報を入力します。

   ![Azure への VPN クライアントの接続](./media/point-to-site-how-to-radius-ps/client.png)
2. 接続が確立されました。

   ![確立された接続](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Mac の VPN クライアントから接続する

[ネットワーク] ダイアログ ボックスで使用するクライアント プロファイルを探し、 **[接続]** をクリックします。

  ![Mac の接続](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>接続を確認するには

1. VPN 接続がアクティブであることを確認するには、管理者特権でのコマンド プロンプトを開いて、 *ipconfig/all*を実行します。
2. 結果を表示します。 受信した IP アドレスが、構成に指定したポイント対サイト VPN クライアント アドレス プール内のアドレスのいずれかであることに注意してください。 結果は次の例のようになります。

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

P2S 接続のトラブルシューティングについては、「[トラブルシューティング: Azure ポイント対サイト接続の問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)」をご覧ください。

## <a name="connectVM"></a>仮想マシンに接続するには

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>FAQ

この FAQ の対象となるのは、RADIUS 認証を使用した P2S です

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>次の手順

接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](https://docs.microsoft.com/azure/) に関するページを参照してください。 ネットワークと仮想マシンの詳細については、「[Azure と Linux の VM ネットワークの概要](../virtual-machines/linux/azure-vm-network-overview.md)」を参照してください。
