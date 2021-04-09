---
title: 'コンピューターから VNet に接続する - P2S VPN とネイティブ Azure 証明書認証: PowerShell'
description: Azure Virtual Network に対し、P2S と自己署名または CA によって発行された証明書を使用して安全に Windows および macOS クライアントを接続します。 この記事では、PowerShell を使用します。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed0a60c88c33af70b7d780d6c4735c5f8e65b35b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660408"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>ネイティブ Azure 証明書認証を使用した VNet へのポイント対サイト VPN 接続の構成:PowerShell

この記事では、Windows、Linux、または macOS が実行されている個々のクライアントを Azure VNet に対して安全に接続する方法を紹介します。 ポイント対サイト VPN 接続は、自宅や会議室でのテレワークなど、リモートの場所から VNet に接続する場合に便利です。 VNet への接続を必要とするクライアントがごく少ない場合は、サイト対サイト VPN の代わりに P2S を使用することもできます。 ポイント対サイト接続に、VPN デバイスや公開 IP アドレスは必要ありません。 P2S により、SSTP (Secure Socket トンネリング プロトコル) または IKEv2 経由の VPN 接続が作成されます。

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="コンピューターから Azure VNet に接続する - ポイント対サイト接続の図":::

ポイント対サイト VPN の詳細については、「[ポイント対サイト VPN について](point-to-site-about.md)」を参照してください。 Azure portal を使用してこの構成を作成するには、[Azure portal を使用したポイント対サイト VPN の構成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関する記事を参照してください。

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> この記事の多くの手順では、Azure Cloud Shell を使用できます。 ただし、Cloud Shell を使用して証明書を生成することはできません。 また、ルート証明書の公開キーをアップロードするには、ローカルの Azure PowerShell または Azure portal を使用する必要があります。
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1.サインイン

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2.変数の宣言

この記事では、サンプル自体を変更することなく、独自の環境に適用する値を簡単に変更できるように、変数を使用しています。 使用する変数を宣言します。 次のサンプルを使用し、必要に応じて独自の値で置き換えられます。 演習中の任意の時点で PowerShell/Cloud Shell セッションを閉じた場合は、値をもう一度コピーして貼り付けるだけで、変数を再宣言します。

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3.VNet の構成

1. リソース グループを作成します。

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. 仮想ネットワークのサブネット構成を作成し、*FrontEnd* および *GatewaySubnet* という名前を付けます。 これらのプレフィックスは、宣言した VNet アドレス空間に含まれている必要があります。

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. 仮想ネットワークを作成します。

   この例では、-DnsServer サーバー パラメーターはオプションです。 値を指定しても新しい DNS サーバーは作成されません。 指定する DNS サーバーの IP アドレスは、VNet から接続するリソースの名前を解決できる DNS サーバーの IP アドレスである必要があります。 この例ではプライベート IP アドレスを使用していますが、これはおそらく実際の DNS サーバーの IP アドレスと一致しません。 実際には独自の値を使用してください。 指定された値は、P2S 接続や VPN のクライアントではなく、VNet にデプロイするリソースが使用します。

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. 作成した仮想ネットワークの変数を指定します。

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. VPN ゲートウェイには、パブリック IP アドレスが必要です。 これにはまず IP アドレスのリソースを要求したうえで、仮想ネットワーク ゲートウェイの作成時にそのリソースを参照する必要があります。 IP アドレスは、VPN ゲートウェイの作成時にリソースに対して動的に割り当てられます。 VPN Gateway では現在、パブリック IP アドレスの "*動的*" 割り当てのみサポートしています。 静的パブリック IP アドレスの割り当てを要求することはできません。 もっとも、VPN ゲートウェイに割り当てられた IP アドレスが後から変わることは基本的にありません。 パブリック IP アドレスが変わるのは、ゲートウェイが削除され、再度作成されたときのみです。 VPN ゲートウェイのサイズ変更、リセット、その他の内部メンテナンス/アップグレードでは、IP アドレスは変わりません。

   動的に割り当てられたパブリック IP アドレスを要求します。

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4.VPN ゲートウェイを作成する

この手順では、VNet の仮想ネットワーク ゲートウェイを構成して作成します。

* -GatewayType は **Vpn**、-VpnType は **RouteBased** にする必要があります。
* -VpnClientProtocol は、有効にするトンネルの種類を指定する際に使用します。 トンネルのオプションには、**OpenVPN、SSTP**、**IKEv2** があります。 いずれか 1 つを有効にすることも、サポートされるすべての組み合わせを有効にすることもできます。 複数の種類を有効にする場合は、名前をコンマで区切って指定します。 OpenVPN と SSTP は一緒に有効にすることができません。 Android と Linux の strongSwan クライアントおよび iOS と OSX のネイティブ IKEv2 VPN クライアントでは、接続に IKEv2 トンネルのみを使用します。 Windows クライアントでは最初に IKEv2 を試し、接続できなかった場合に SSTP を使用します。 OpenVPN クライアントを使用して、OpenVPN トンネルの種類に接続することができます。
* 仮想ネットワーク ゲートウェイ 'Basic' SKU では、IKEv2、OpenVPN、RADIUS 認証はサポートされません。 Mac クライアントを仮想ネットワークに接続する予定がある場合は、Basic SKU を使用しないでください。
* 選択する[ゲートウェイ SKU](vpn-gateway-about-vpn-gateway-settings.md) によっては、VPN ゲートウェイで処理が完了するまでに最大で 45 分かかる場合があります。 この例では、IKEv2 を使用します。

1. VNet の仮想ネットワーク ゲートウェイを構成、作成します。 ゲートウェイの作成には約 45 分かかります。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. ゲートウェイを作成したら、次の例を使用してゲートウェイを表示できます。 PowerShell を閉じた場合やゲートウェイの作成中にタイムアウトになった場合は、再度[変数を宣言](#declare)できます。

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5.VPN クライアント アドレス プールの追加

VPN ゲートウェイの作成が完了したら、VPN クライアント アドレス プールを追加できます。 VPN クライアント アドレス プールは、VPN クライアントが接続時に受け取る IP アドレスの範囲です。 接続元であるオンプレミスの場所、または接続先とする VNet と重複しないプライベート IP アドレス範囲を使用してください。

この例での VPN クライアント アドレス プールは、前の手順で[変数](#declare)として宣言されています。

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6.証明書の生成

>[!IMPORTANT]
> Azure Cloud Shell を使用して証明書を生成することはできません。 このセクションで説明されているいずれかの方法を使用する必要があります。 PowerShell を使用する場合は、ローカルにインストールする必要があります。
>

証明書は、ポイント対サイト VPN の VPN クライアントを認証するために、Azure によって使用されます。 そのため、ルート証明書の公開キー情報を Azure にアップロードします。 その後、その公開キーは "信頼された" と見なされます。 信頼されたルート証明書からクライアント証明書を生成し、それを各クライアント コンピューターの [証明書 - 現在のユーザー] の [個人] 証明書ストアにインストールする必要があります。 この証明書は、クライアントで VNet への接続を開始するときに、そのクライアントを認証するために使用されます。 

自己署名証明書を使用する場合は、特定のパラメーターを使って証明書を作成する必要があります。 自己署名証明書は、[PowerShell と Windows 10](vpn-gateway-certificates-point-to-site.md) を使った手順で作成できるほか、Windows 10 をご利用でない場合は、[MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) を使って作成することができます。 自己署名ルート証明書やクライアント証明書を生成するときは、これらの説明に記載されている手順に従うことが大切です。 そうしないと、生成される証明書が P2S 接続に適合せず、接続エラーが発生します。

### <a name="root-certificate"></a><a name="cer"></a>ルート証明書

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. ルート証明書の作成後、秘密キーではなく公開証明書データを、Base64 でエンコードされた X.509 .cer ファイルとして[エクスポート](vpn-gateway-certificates-point-to-site.md#cer)します。

### <a name="client-certificate"></a><a name="generate"></a>クライアント証明書

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. クライアント証明書を作成したら、それを[エクスポート](vpn-gateway-certificates-point-to-site.md#clientexport)します。 クライアント証明書は、接続するクライアント コンピューターに配布されます。

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7.ルート証明書の公開キー情報のアップロード

VPN ゲートウェイの作成が完了していることを確認します。 この操作が完了した後は、信頼されたルート証明書の (公開キー情報を含む) .cer ファイルを Azure にアップロードできます。 .cer ファイルがアップロードされると、Azure ではそれを使用し、信頼されたルート証明書から生成されたクライアント証明書がインストールされているクライアントを認証できます。 その後は必要に応じて、最大で合計 20 個になるまで信頼されたルート証明書を追加でアップロードできます。

>[!NOTE]
> Azure Cloud Shell を使用して .cer ファイルをアップロードすることはできません。 ローカル環境のコンピューターで PowerShell を使用するか、[Azure portal の手順](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)を使用できます。
>

1. 証明書名のための変数を、独自の値に置き換えて宣言します。

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. ファイルのパスを独自のパスに置換し、次のコマンドレットを実行します。

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. 公開キー情報を Azure にアップロードします。 証明書情報をアップロードすると、Azure ではそれが信頼されたルート証明書と見なされます。 アップロードする際は、ご利用のコンピューターのローカルから PowerShell を実行するか、[Azure portal の手順](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)を使用してください。 Azure Cloud Shell を使用してアップロードすることはできません。

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8.エクスポートしたクライアント証明書のインストール

次の手順に従い、Windows クライアントにインストールできます。 その他のクライアントと詳細については、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関する記事を参照してください。

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

クライアント証明書が証明書チェーン全体と一緒に .pfx としてエクスポートされている (既定値) ことを確認します。 そうでないと、ルート証明書情報がクライアント コンピューターに存在せず、クライアントは正しく認証されません。

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9.VPN クライアントを構成する

このセクションでは、仮想ネットワーク ゲートウェイに接続するようにコンピューターのネイティブ クライアントを構成します。 たとえば、Windows コンピューターの VPN 設定に移動すると、VPN 接続を追加できます。 ポイント対サイト接続には、特定の構成設定が必要です。 この手順では、ネイティブ VPN クライアントがポイント対サイト接続を介して仮想ネットワークに接続できるようにするために必要な特定の設定を使用して、パッケージを作成できます。

次の簡単な例を使用して、クライアント構成パッケージを生成してインストールできます。 パッケージの内容に関する詳細と、VPN クライアント構成ファイルの生成とインストールに関する追加の手順については、「[VPN クライアント構成ファイルの作成とインストール](point-to-site-vpn-client-configuration-azure-cert.md)」を参照してください。

変数を再宣言する必要がある場合は、[こちら](#declare)を参照してください。

### <a name="to-generate-configuration-files"></a>構成ファイルを生成するには

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>クライアント構成パッケージをインストールするには

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10.Azure に接続する

### <a name="windows-vpn-client"></a>Windows VPN クライアント

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Mac VPN クライアント

[ネットワーク] ダイアログ ボックスで使用するクライアント プロファイルを探し、 **[接続]** をクリックします。
手順の詳細については、[Mac (OS X) のインストール](./point-to-site-vpn-client-configuration-azure-cert.md#installmac)に関するセクションを参照してください。 接続に問題がある場合は、仮想ネットワーク ゲートウェイが Basic SKU を使用していないことを確認します。 Basic SKU は Mac クライアントではサポートされていません。

  ![Mac の接続](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>接続を確認するには

ここで紹介する手順は、Windows クライアントに適用されます。

1. VPN 接続がアクティブであることを確認するには、管理者特権でのコマンド プロンプトを開いて、 *ipconfig/all* を実行します。
2. 結果を表示します。 受信した IP アドレスが、構成で指定したポイント対サイト VPN クライアント アドレス プール内のアドレスのいずれかであることに注意してください。 結果は次の例のようになります。

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>仮想マシンに接続するには

ここで紹介する手順は、Windows クライアントに適用されます。

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* VNet に対して DNS サーバーの IP アドレスが指定された後に VPN クライアント構成パッケージが生成されたことを確認します。 DNS サーバーの IP アドレスを更新した場合は、新しい VPN クライアント構成パッケージを生成してインストールしてください。

* 接続元のコンピューターのイーサネット アダプターに割り当てられている IPv4 アドレスを "ipconfig" でチェックします。 その IP アドレスが、接続先の VNet のアドレス範囲内または VPNClientAddressPool のアドレス範囲内にある場合、これを "アドレス空間の重複" といいます。 アドレス空間がこのように重複していると、ネットワーク トラフィックが Azure に到達せずローカル ネットワーク上に留まることになります。

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>ルート証明書を追加または削除するには

信頼されたルート証明書を Azure に追加したり、Azure から削除したりできます。 ルート証明書を削除すると、そのルート証明書から生成された証明書を持つクライアントは認証できなくなり、接続できなくなります。 クライアントの認証と接続を正常に実行できるようにするには、Azure に信頼されている (Azure にアップロードされている) ルート証明書から生成した新しいクライアント証明書をインストールする必要があります。 この手順では、(Azure Cloud Shell ではなく) お使いのコンピューターに Azure PowerShell コマンドレットがローカルにインストールされている必要があります。 また、Azure portal を使用してルート証明書を追加することもできます。

**追加するには:**

Azure には、最大 20 個のルート証明書 .cer ファイルを追加できます。 ルート証明書は次の手順で追加できます。 

1. アップロードする .cer ファイルを準備します。

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. ファイルをアップロードします。 ファイルは一度に 1 つしかアップロードできません。

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. 証明書ファイルがアップロードされたことを確認するには:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**削除するには:**

1. 変数を宣言します。 削除する証明書と一致するように、例の変数を変更します。

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. 証明書を削除します。

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. 次の例を使用して、証明書が正常に削除されたことを確認します。

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>クライアント証明書を失効または回復するには

クライアント証明書は失効させることができます。 証明書失効リストを使用すると、個々のクライアント証明書に基づくポイント対サイト接続を選択して拒否することができます。 これは、信頼されたルート証明書を削除することとは異なります。 信頼されたルート証明書 .cer を Azure から削除すると、失効したルート証明書によって生成または署名されたすべてのクライアント証明書のアクセス権が取り消されます。 ルート証明書ではなくクライアント証明書を失効させることで、ルート証明書から生成されたその他の証明書は、その後も認証に使用できます。

一般的な方法としては、ルート証明書を使用してチームまたは組織レベルでアクセスを管理し、失効したクライアント証明書を使用して、個々のユーザーの細かいアクセス制御を構成します。

**取り消するには、次を実行します。**

1. クライアント証明書の拇印を取得します。 詳細については、「[方法: 証明書のサムプリントを取得する](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)」を参照してください。

1. 情報をテキスト エディターにコピーし、文字列が 1 つにつながるようにスペースをすべて削除します。 この文字列を次の手順で変数として宣言します。

1. 変数を宣言します。 必ず前の手順で取得した拇印を宣言してください。

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. 失効した証明書のリストに拇印を追加します。 拇印が追加されると、"Succeeded" と表示されます。

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. 証明書失効リストに拇印が追加されたことを確認します。

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. 拇印が追加された後は、証明書を接続に使用することができなくなります。 この証明書を使用して接続を試みたクライアントには、証明書が無効になっていることを示すメッセージが表示されます。

**回復するには:**

失効したクライアント証明書のリストから拇印を削除することで、クライアント証明書を回復できます。

1. 変数を宣言します。 回復する証明書用の適切な拇印を宣言していることを確認してください。

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. 証明書の失効リストから証明書の拇印を削除します。

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. 失効リストから拇印が削除されているかどうかを確認します。

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>ポイント対サイトに関する FAQ

ポイント対サイトの追加情報については、[VPN Gateway のポイント対サイトに関する FAQ](vpn-gateway-vpn-faq.md#P2S)に関するページを参照してください

## <a name="next-steps"></a>次のステップ

接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](../index.yml) に関するページを参照してください。 ネットワークと仮想マシンの詳細については、「[Azure と Linux の VM ネットワークの概要](../virtual-machines/network-overview.md)」を参照してください。

P2S のトラブルシューティング情報については、[Azure ポイント対サイト接続の問題のトラブルシューティング](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)に関するページを参照してください。