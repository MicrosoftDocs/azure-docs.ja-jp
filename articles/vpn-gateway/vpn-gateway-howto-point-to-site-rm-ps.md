---
title: "ポイント対サイトと証明書認証を使用してコンピューターを Azure 仮想ネットワークに接続する: PowerShell | Microsoft Docs"
description: "証明書認証を使用してポイント対サイト VPN ゲートウェイ接続を作成することで、コンピューターを仮想ネットワークに安全に接続します。 この記事は、Resource Manager デプロイメント モデルに適用されます。また、ここでは PowerShell が使用されます。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: ebba36a0f6f9a3f2c8980741dd333ffc56a361cc
ms.contentlocale: ja-jp
ms.lasthandoff: 08/31/2017

---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-certificate-authentication-powershell"></a>証明書認証を使用した VNet へのポイント対サイト接続の構成: PowerShell

この記事では、PowerShell を使用して、ポイント対サイト接続を備えた VNet を Resource Manager デプロイメント モデルで作成する手順を説明します。 この構成では、証明書を使用して接続クライアントを認証します。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

ポイント対サイト (P2S) VPN ゲートウェイでは、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。 ポイント対サイト VPN 接続は、自宅や会議室でのテレワークなど、リモートの場所から VNet に接続する場合に便利です。 P2S VPN は、VNet への接続が必要なクライアントがごく少ない場合に、サイト対サイト VPN の代わりに使用するソリューションとしても便利です。

P2S は、Secure Socket トンネリング プロトコル (SSTP) を使用します。これは、SSL ベースの VPN プロトコルです。 P2S VPN 接続は、クライアント コンピューターから接続を開始することによって確立されます。

![コンピューターを Azure VNet に接続する - ポイント対サイト接続の図](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

ポイント対サイトの証明書認証接続には、以下のものが必要です。

* RouteBased VPN ゲートウェイ。
* Azure にアップロードされた、ルート証明書の公開キー (.cer ファイル)。 証明書をアップロードすると、その証明書は信頼された証明書と見なされ、認証に使用されます。
* ルート証明書から生成され、VNet に接続する各クライアント コンピューターにインストールされたクライアント証明書。 この証明書はクライアントの認証に使用されます。
* VPN クライアント構成パッケージ。 VPN クライアント構成パッケージには、クライアントが VNet に接続するために必要な情報が含まれています。 このパッケージを使用すると、Windows オペレーティング システムにネイティブな既存の VPN クライアントが構成されます。 接続する各クライアントは、構成パッケージを使用して構成する必要があります。

ポイント対サイト接続には、VPN デバイスやオンプレミスの公開 IP アドレスは必要ありません。 VPN 接続は、SSTP (Secure Socket トンネリング プロトコル) 経由で作成されます。 サーバー側でのサポート対象の SSTP バージョンは、1.0、1.1、1.2 です。 使用するバージョンはクライアントによって決まります。 Windows 8.1 以降の場合、SSTP では既定で 1.2 が使用されます。 

ポイント対サイト接続の詳細については、この記事の最後にある「[ポイント対サイト接続に関してよく寄せられる質問](#faq)」を参照してください。

## <a name="before-you-begin"></a>開始する前に

* Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。
* Azure Resource Manager PowerShell コマンドレットの最新版をインストールしてください。 PowerShell コマンドレットのインストールの詳細については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)に関するページを参照してください。

### <a name="example"></a>値の例

値の例を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。 この記事のパート [1](#declare) で変数を設定します。 手順をチュートリアルとして利用して値を変更せずに使用することも、実際の環境に合わせて値を変更することもできます。 

* **名前: VNet1**
* **アドレス空間: 192.168.0.0/16** と **10.254.0.0/16**<br>この例では、この構成が複数のアドレス空間で機能することを示すために、複数のアドレス空間を使用します。 ただし、この構成で複数のアドレス空間は必須ではありません。
* **サブネット名: FrontEnd**
  * **サブネットのアドレス範囲: 192.168.1.0/24**
* **サブネット名: BackEnd**
  * **サブネットのアドレス範囲: 10.254.1.0/24**
* **サブネット名: GatewaySubnet**<br>VPN ゲートウェイが機能するには、サブネット名 *GatewaySubnet* が必須となります。
  * **GatewaySubnet のアドレス範囲: 192.168.200.0/24** 
* **VPN クライアント アドレス プール: 172.16.201.0/24**<br>このポイント対サイト接続を利用して VNet に接続する VPN クライアントは、この VPN クライアント アドレス プールから IP アドレスを受け取ります。
* **サブスクリプション:** サブスクリプションが複数ある場合は、適切なサブスクリプションを使用していることを確認します。
* **リソース グループ: TestRG**
* **場所: 米国東部**
* **DNS サーバー**: 名前解決に利用する DNS サーバーの IP アドレス。
* **GW 名: Vnet1GW**
* **パブリック IP 名: VNet1GWPIP**
* **VpnType: RouteBased** 

## <a name="declare"></a>1.ログインと変数の設定

このセクションでは、ログインのほか、この構成で使用される値の宣言を行います。 サンプル スクリプトでは、宣言済みの値が使用されます。 実際の環境に合わせて値を変更してください。 宣言済みの値を使用し、以下の手順を練習として使用することもできます。

1. 昇格された特権で PowerShell コンソールを開き、Azure アカウントにログインします。 このコマンドレットは、ログイン資格情報をユーザーに求めます。 ログイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。

  ```powershell
  Login-AzureRmAccount
  ```
2. Azure サブスクリプションの一覧を取得します。

  ```powershell
  Get-AzureRmSubscription
  ```
3. 使用するサブスクリプションを指定します。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
4. 使用する変数を宣言します。 次のサンプルを使用し、必要に応じて独自の値で置き換えます。

  ```powershell
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
  $DNS = "10.1.1.3"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="ConfigureVNet"></a>2.VNet の構成

1. リソース グループを作成します。

  ```powershell
  New-AzureRmResourceGroup -Name $RG -Location $Location
  ```
2. 仮想ネットワークのサブネット構成を作成し、*FrontEnd*、*BackEnd*、*GatewaySubnet* いう名前を付けます。 これらのプレフィックスは、宣言した VNet アドレス空間に含まれている必要があります。

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
  ```
3. 仮想ネットワークを作成する。

  この例では、DNS サーバーはオプションです。 値を指定しても新しい DNS サーバーは作成されません。 指定する DNS サーバーの IP アドレスは、接続先のリソースの名前を解決できる DNS サーバーの IP アドレスである必要があります。 この例ではプライベート IP アドレスを使用していますが、これはおそらく実際の DNS サーバーの IP アドレスと一致しません。 実際には独自の値を使用してください。

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
  ```
4. 作成した仮想ネットワークの変数を指定します。

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  ```
5. VPN ゲートウェイには、パブリック IP アドレスが必要です。 これにはまず IP アドレスのリソースを要求したうえで、仮想ネットワーク ゲートウェイの作成時にそのリソースを参照する必要があります。 IP アドレスは、VPN ゲートウェイの作成時にリソースに対して動的に割り当てられます。 VPN Gateway では現在、パブリック IP アドレスの "*動的*" 割り当てのみサポートしています。 静的パブリック IP アドレスの割り当てを要求することはできません。 もっとも、VPN ゲートウェイに割り当てられた IP アドレスが後から変わることは基本的にありません。 パブリック IP アドレスが変わるのは、ゲートウェイが削除され、再度作成されたときのみです。 VPN ゲートウェイのサイズ変更、リセット、その他の内部メンテナンス/アップグレードでは、IP アドレスは変わりません。

  動的に割り当てられたパブリック IP アドレスを要求します。

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```

## <a name="creategateway"></a>3.VPN ゲートウェイを作成する

VNet の仮想ネットワーク ゲートウェイを構成、作成します。

* *-GatewayType* は **Vpn** に、*-VpnType* は **RouteBased** にする必要があります。
* 選択する[ゲートウェイ SKU](vpn-gateway-about-vpn-gateway-settings.md) によっては、VPN ゲートウェイで処理が完了するまでに最大で 45 分かかる場合があります。

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 `
```

## <a name="addresspool"></a>4.VPN クライアント アドレス プールの追加

VPN ゲートウェイの作成が完了したら、VPN クライアント アドレス プールを追加できます。 VPN クライアント アドレス プールは、VPN クライアントが接続時に受け取る IP アドレスの範囲です。 接続元であるオンプレミスの場所、または接続先とする VNet と重複しないプライベート IP アドレス範囲を使用してください。 この例での VPN クライアント アドレス プールは、手順 1. で[変数](#declare)として宣言されています。

```powershell
$Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="Certificates"></a>5.証明書の生成

証明書は、ポイント対サイト VPN の VPN クライアントを認証するために、Azure によって使用されます。 そのため、ルート証明書の公開キー情報を Azure にアップロードします。 その後、その公開キーは "信頼された" と見なされます。 信頼されたルート証明書からクライアント証明書を生成し、それを各クライアント コンピューターの [証明書 - 現在のユーザー] の [個人] 証明書ストアにインストールする必要があります。 この証明書は、クライアントで VNet への接続を開始するときに、そのクライアントを認証するために使用されます。 

自己署名証明書を使用する場合は、特定のパラメーターを使って証明書を作成する必要があります。 自己署名証明書は、[PowerShell と Windows 10](vpn-gateway-certificates-point-to-site.md) を使った手順で作成できるほか、Windows 10 をご利用でない場合は、[MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) を使って作成することができます。 自己署名ルート証明書やクライアント証明書を生成するときは、これらの説明に記載されている手順に従うことが大切です。 説明どおりに生成しないと、証明書が P2S 接続に適合せず、接続エラーが発生します。

### <a name="cer"></a>1.ルート証明書の .cer ファイルの取得

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]


### <a name="generate"></a>2.クライアント証明書を生成

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>6.ルート証明書の公開キー情報のアップロード

VPN ゲートウェイの作成が完了していることを確認します。 この操作が完了した後は、信頼されたルート証明書の (公開キー情報を含む) .cer ファイルを Azure にアップロードできます。 .cer ファイルがアップロードされると、Azure ではそれを使用し、信頼されたルート証明書から生成されたクライアント証明書がインストールされているクライアントを認証できます。 その後は必要に応じて、最大で合計 20 個になるまで信頼されたルート証明書を追加でアップロードできます。

1. 証明書名のための変数を、独自の値に置き換えて宣言します。

  ```powershell
  $P2SRootCertName = "P2SRootCert.cer"
  ```
2. ファイルのパスを独自のパスに置換し、次のコマンドレットを実行します。

  ```powershell
  $filePathForCert = "C:\cert\P2SRootCert.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
  ```
3. 公開キー情報を Azure にアップロードします。 証明書情報がアップロードされると、Azure はこれを信頼されたルート証明書と見なします。

   ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64
  ```

## <a name="clientconfig"></a>7.VPN クライアント構成パッケージのダウンロード

ポイント対サイト VPN を使用して VNet に接続するには、仮想ネットワークに接続するために必要な設定とファイルを使用してネイティブ VPN クライアントを構成するクライアント構成パッケージを各クライアントにインストールする必要があります。 VPN クライアント構成パッケージは、ネイティブ Windows VPN クライアントを構成するもので、新規または別の VPN クライアントをインストールするものではありません。 

バージョンがクライアントのアーキテクチャと一致すれば、各クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。 サポートされているクライアント オペレーティング システムの一覧については、この記事の最後にある「[ポイント対サイト接続に関してよく寄せられる質問](#faq)」を参照してください。

1. ゲートウェイの作成が完了したら、クライアント構成パッケージを生成してダウンロードできます。 この例では、64 ビット クライアント用のパッケージをダウンロードします。 32 ビット クライアントをダウンロードする場合は、"Amd64" を "x86" に置き換えます。 Azure Portal を使用して VPN クライアントをダウンロードすることもできます。

  ```powershell
  Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
  -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
  ```
2. 返されたリンクをコピーして Web ブラウザーに貼り付け、パッケージをダウンロードします。リンクを囲む引用符は必ず削除してください。 
3. パッケージをクライアント コンピューターにダウンロードしてインストールします。 SmartScreen ポップアップが表示された場合は、**[詳細]**、**[実行]** の順にクリックしてください。 パッケージを保存して、他のクライアント コンピューターにインストールすることもできます。
4. クライアント コンピューターで **[ネットワークの設定]** に移動し、**[VPN]** をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。

## <a name="clientcertificate"></a>8.エクスポートしたクライアント証明書のインストール

クライアント証明書の生成に使用したクライアント コンピューター以外から P2S 接続を作成する場合は、クライアント証明書をインストールする必要があります。 クライアント証明書をインストールするときに、クライアント証明書のエクスポート時に作成されたパスワードが必要になります。 通常は、証明書をダブルクリックするだけでインストールできます。

クライアント証明書が証明書チェーン全体と一緒に .pfx としてエクスポートされている (既定値) ことを確認します。 そうでないと、ルート証明書情報がクライアント コンピューターに存在せず、クライアントは正しく認証されません。 詳細については、「[エクスポートしたクライアント証明書のインストール](vpn-gateway-certificates-point-to-site.md#install)」を参照してください。 

## <a name="connect"></a>9.Azure への接続

1. VNet に接続するには、クライアント コンピューターで [VPN 接続] に移動し、作成した VPN 接続を見つけます。 仮想ネットワークと同じ名前が付いています。 **[接続]**をクリックします。 証明書を使用することを示すポップアップ メッセージが表示される場合があります。 **[続行]** をクリックして、昇格された特権を使用します。 
2. **接続**の状態ページで、**[接続]** をクリックして接続を開始します。 **[証明書の選択]** 画面が表示された場合は、表示されているクライアント証明書が接続に使用する証明書であることを確認します。 そうでない場合は、ドロップダウン矢印を使用して適切な証明書を選択し、 **[OK]**をクリックします。

  ![Azure への VPN クライアントの接続](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. 接続が確立されました。

  ![確立された接続](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

#### <a name="troubleshooting-p2s-connections"></a>P2S 接続のトラブルシューティング

[!INCLUDE [client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify"></a>10.接続を確認する

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

## <a name="connectVM"></a>仮想マシンへの接続

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="addremovecert"></a>ルート証明書を追加または削除する

信頼されたルート証明書を Azure に追加したり、Azure から削除したりできます。 ルート証明書を削除すると、そのルート証明書から生成された証明書を持つクライアントは認証できなくなり、接続できなくなります。 クライアントの認証と接続を正常に実行できるようにするには、Azure に信頼されている (Azure にアップロードされている) ルート証明書から生成した新しいクライアント証明書をインストールする必要があります。

### <a name="addtrustedroot"></a>信頼されたルート証明書を追加するには

Azure には、最大 20 個のルート証明書 .cer ファイルを追加できます。 ルート証明書は次の手順で追加できます。

#### <a name="certmethod1"></a>方法 1

これは、ルート証明書をアップロードする場合に最も効率的な方法です。

1. アップロードする .cer ファイルを準備します。

  ```powershell
  $filePathForCert = "C:\cert\P2SRootCert3.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64_3
  ```
2. ファイルをアップロードします。 ファイルは一度に 1 つしかアップロードできません。

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64_3
  ```

3. 証明書ファイルがアップロードされたことを確認するには:

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

#### <a name="certmethod2"></a>方法 2

この方法では方法 1 より多くの手順が必要になりますが、結果は同じになります。 証明書データを表示する必要がある場合のためにこの方法を紹介しています。

1. Azure に追加する新しいルート証明書を作成して準備します。 Base64 でエンコードされた X.509 (.cer) として公開キーをエクスポートし、テキスト エディターで開きます。 次の例で示すように値をコピーします。

  ![証明書](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

  > [!NOTE]
  > 証明書データをコピーするときはに、必ず、テキストを復帰や改行のない 1 つの連続した行としてコピーしてください。 復帰や改行を確認するには、テキスト エディターのビューを "記号を表示する/すべての文字を表示する" ように変更することが必要になる場合があります。
  >
  >

2. 証明書名とキーの情報を変数として指定しています。 次の例で示すように、情報は実際のものに置き換えてください。

  ```powershell
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
3. 新しいルート証明書を追加します。 一度に追加できる証明書は 1 つだけです。

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
4. 次の例を使用して、新しい証明書が正しく追加されたことを確認できます。

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

### <a name="removerootcert"></a>ルート証明書を削除するには

1. 変数を宣言します。

  ```powershell
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
2. 証明書を削除します。

  ```powershell
  Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
3. 次の例を使用して、証明書が正常に削除されたことを確認します。

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

## <a name="revoke"></a>クライアント証明書の失効

クライアント証明書は失効させることができます。 証明書失効リストを使用すると、個々のクライアント証明書に基づくポイント対サイト接続を選択して拒否することができます。 これは、信頼されたルート証明書を削除することとは異なります。 信頼されたルート証明書 .cer を Azure から削除すると、失効したルート証明書によって生成または署名されたすべてのクライアント証明書のアクセス権が取り消されます。 ルート証明書ではなくクライアント証明書を失効させることで、ルート証明書から生成されたその他の証明書は、その後も認証に使用できます。

一般的な方法としては、ルート証明書を使用してチームまたは組織レベルでアクセスを管理し、失効したクライアント証明書を使用して、個々のユーザーの細かいアクセス制御を構成します。

### <a name="revokeclientcert"></a>クライアント証明書を失効させるには

1. クライアント証明書の拇印を取得します。 詳細については、「[方法: 証明書のサムプリントを取得する](https://msdn.microsoft.com/library/ms734695.aspx)」を参照してください。
2. 情報をテキスト エディターにコピーし、文字列が 1 つにつながるようにスペースをすべて削除します。 この文字列を次の手順で変数として宣言します。
3. 変数を宣言します。 必ず前の手順で取得した拇印を宣言してください。

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
4. 失効した証明書のリストに拇印を追加します。 拇印が追加されると、"Succeeded" と表示されます。

  ```powershell
  Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
  -Thumbprint $RevokedThumbprint1
  ```
5. 証明書失効リストに拇印が追加されたことを確認します。

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```
6. 拇印が追加された後は、証明書を接続に使用することができなくなります。 この証明書を使用して接続を試みたクライアントには、証明書が無効になっていることを示すメッセージが表示されます。

### <a name="reinstateclientcert"></a>クライアント証明書を回復するには

失効したクライアント証明書のリストから拇印を削除することで、クライアント証明書を回復できます。

1. 変数を宣言します。 回復する証明書用の適切な拇印を宣言していることを確認してください。

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
2. 証明書の失効リストから証明書の拇印を削除します。

  ```powershell
  Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
  ```
3. 失効リストから拇印が削除されているかどうかを確認します。

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```

## <a name="faq"></a>ポイント対サイト接続に関してよく寄せられる質問

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-include.md)]

## <a name="next-steps"></a>次のステップ
接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) に関するページを参照してください。 ネットワークと仮想マシンの詳細については、「[Azure と Linux の VM ネットワークの概要](../virtual-machines/linux/azure-vm-network-overview.md)」を参照してください。
