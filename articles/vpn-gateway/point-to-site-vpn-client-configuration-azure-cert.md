---
title: Azure 証明書認証用の P2S VPN クライアント構成ファイルを作成しインストールする:Azure
description: P2S 証明書認証のために、Windows、Linux、Linux (strongSwan)、および Mac OS X VPN クライアント構成ファイルを作成してインストールします。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/12/2019
ms.author: cherylmc
ms.openlocfilehash: 095c7c4bf2a0fb08c0a7fe7e0a8118e76732c9c7
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961602"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>ネイティブ Azure 証明書認証の P2S 構成のための VPN クライアント構成ファイルを作成およびインストールする

VPN クライアント構成ファイルは、ZIP ファイルに含まれています。 構成ファイルでは、Windows、Mac IKEv2 VPN、Linux のネイティブ クライアントが、ネイティブ Azure 証明書認証を使用するポイント対サイト接続を介して VNet に接続するために必要な設定を提供します。

クライアント構成ファイルは、VNet の VPN 構成に固有です。 VPN プロトコルの種類や認証の種類など、VPN クライアント構成ファイルの生成後にポイント対サイト VPN 構成に対する変更があった場合は、ユーザー デバイス用に新しい VPN クライアント構成ファイルを生成してください。 

* ポイント対サイト接続について詳しくは、「[ポイント対サイト VPN について](point-to-site-about.md)」を参照してください。
* OpenVPN の取扱説明については、[P2S 向け OpenVPN を構成する](vpn-gateway-howto-openvpn.md)方法に関するページと [OpenVPN クライアントを構成する](vpn-gateway-howto-openvpn-clients.md)方法に関するページを参照してください。

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>VPN クライアント構成ファイルの生成

開始する前に、接続するすべてのユーザーでは、有効な証明書がユーザーのデバイスにインストールされていることを確認してください。 クライアント証明書のインストールの詳細については、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページを参照してください。

PowerShell または Azure Portal を使用してクライアント構成ファイルを生成することができます。 どちらの方法でも、同じ zip ファイルが返されます。 そのファイルを解凍して、次のフォルダーを表示します。

  * **WindowsAmd64** および **WindowsX86**。Windows の 32 ビットと 64 ビットのインストーラー パッケージがそれぞれに含まれています。 **WindowsAmd64** インストーラー パッケージは、Amd だけでなく、サポートされている 64 ビットの Windows クライアントを対象としています。
  * **Generic**。これには、独自の VPN クライアント構成の作成に使用される全般的な情報が含まれています。 Generic フォルダーが提供されるのは、IKEv2 または SSTP+IKEv2 がゲートウェイ上で構成された場合です。 構成されているのが SSTP のみの場合、Generic フォルダーは存在しません。

### <a name="zipportal"></a>Azure Portal を使用してルールを生成する

1. Azure Portal で、接続する仮想ネットワークの仮想ネットワーク ゲートウェイに移動します。
2. 仮想ネットワーク ゲートウェイ ページで、 **[ポイント対サイトの構成]** をクリックします。
3. [ポイント対サイトの構成] ページの上部で **[VPN クライアントのダウンロード]** をクリックします。 クライアント構成パッケージが生成されるまでに数分かかります。
4. お使いのブラウザーは、クライアント構成の zip ファイルが使用可能なことを示します。 ゲートウェイと同じ名前が付いています。 そのファイルを解凍して、フォルダーを表示します。

### <a name="zipps"></a>PowerShell を使用してファイルを生成する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. VPN クライアント構成ファイルを生成する際、"-AuthenticationMethod" の値は "EapTls" です。 次のコマンドを使用して、VPN クライアント構成ファイルを生成します。

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. ブラウザーに URL をコピーして、zip ファイルをダウンロードし、ファイルを解凍してフォルダーを表示します。

## <a name="installwin"></a>Windows

バージョンがクライアントのアーキテクチャと一致する限り、各 Windows クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。 サポートされているクライアント オペレーティング システムの一覧については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md#P2S)」のポイント対サイトに関するセクションを参照してください。

>[!NOTE]
>接続元の Windows クライアント コンピューターの管理者権限が必要です。
>
>

証明書認証用にネイティブ Windows VPN クライアントを構成するには、次の手順を実行してください。

1. Windows コンピューターのアーキテクチャに対応する VPN クライアント構成ファイルを選択します。 64 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupAmd64" インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupX86" インストーラー パッケージを選択します。 
2. パッケージをダブルクリックしてインストールします。 SmartScreen ポップアップが表示された場合は、 **[詳細]** 、 **[実行]** の順にクリックしてください。
3. クライアント コンピューターで **[ネットワークの設定]** に移動し、 **[VPN]** をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。 
4. 接続を試行する前に、クライアント コンピューターにクライアント証明書をインストール済みであることを確認します。 ネイティブ Azure 証明書の認証タイプを使用する場合、認証にはクライアント証明書が必要です。 証明書の生成の詳細については、「[証明書の生成](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)」をご覧ください。 クライアント証明書のインストール方法については、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページをご覧ください。

## <a name="installmac"></a>Mac (OS X)

 Azure に接続するすべての Mac で、ネイティブの IKEv2 VPN クライアントを手動で構成する必要があります。 Azure では、ネイティブの Azure 証明書の認証用の mobileconfig ファイルは提供されません。 **Generic** には、構成に必要な情報がすべて揃っています。 ダウンロードに、Generic フォルダーが表示されない場合は、IKEv2 がトンネルの種類として選択されていない可能性があります。 VPN Gateway Basic SKU は IKEv2 をサポートしていないことに注意してください。 IKEv2 を選択したら、もう一度 zip ファイルを生成して、Generic フォルダーを取得します。<br>Generic フォルダーには、次のファイルが含まれています。

* **VpnSettings.xml**。サーバー アドレスやトンネルの種類など、重要な設定が含まれています。 
* **VpnServerRoot.cer**。P2S 接続の設定中に Azure VPN ゲートウェイを検証するために必要なルート証明書が含まれています。

証明書認証用に Mac 上でネイティブ VPN クライアントを構成するには、次の手順を実行してください。 Azure に接続するすべての Mac でこれらの手順を完了する必要があります。

1. **VpnServerRoot** ルート証明書を Mac にインポートします。 これを行うには、ファイルを Mac にコピーしてダブルクリックします。 **[追加]** をクリックしてインポートします。

   ![証明書の追加](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >証明書をダブルクリックしても、 **[追加]** ダイアログが表示されない場合がありますが、証明書は正しいストアにインストールされています。 証明書のカテゴリの下にあるログイン キーチェーンで証明書を確認できます。
    >
  
2. P2S 設定を構成したときに、Azure にアップロードしたルート証明書によって発行されたクライアント証明書が、インストール済みであることを確認します。 これは、前の手順でインストールした VPNServerRoot とは異なります。 クライアント証明書は認証に使用され、必須です。 証明書の生成の詳細については、「[証明書の生成](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)」をご覧ください。 クライアント証明書のインストール方法については、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページをご覧ください。
3. **[ネットワーク]** ダイアログを開き、 **[ネットワーク環境設定]** で **[+]** をクリックして、Azure VNet への P2S 接続用に新しい VPN クライアント接続プロファイルを作成します。

   **[インターフェイス]** の値は "VPN"、 **[VPN タイプ]** の値は "IKEv2" です。 **[サービス名]** フィールドにプロファイルの名前を指定し、 **[作成]** をクリックして VPN クライアント接続プロファイルを作成します。

   ![ネットワーク](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. **Generic** フォルダーの **VpnSettings.xml** ファイルから、**VpnServer** タグの値をコピーします。 この値をプロファイルの **[サーバー アドレス]** フィールドと **[リモート ID]** フィールドに貼り付けます。

   ![サーバー情報](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. **[認証設定]** をクリックし、 **[証明書]** を選択します。 

   ![認証設定](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. **[選択]** をクリックして、 認証に使用するクライアント証明書を選択します。 これは、手順 2 でインストールした証明書です。

   ![証明書](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **[Choose An Identity]\(ID の選択\)** では、選択できる証明書の一覧が表示されます。 適切な証明書を選択し、 **[続ける]** をクリックします。

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. **[ローカル ID]** フィールドに、(手順 6 の) 証明書の名前を指定します。 この例では、"ikev2Client.com" です。 次に、 **[適用]** ボタンをクリックして変更を保存します。

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. **[ネットワーク]** ダイアログで、 **[適用]** をクリックしてすべての変更を保存します。 次に、 **[接続]** をクリックして、Azure VNet への P2S 接続を開始します。

## <a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="installstrongswan"></a>strongSwan のインストール

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="genlinuxcerts"></a>証明書の生成

まだ証明書を生成していない場合は、次の手順を使用します。

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install"></a>インストールと構成

次の手順は、Ubuntu 18.0.4 上で作成されました。 Ubuntu 16.0.10 は、strongSwan GUI をサポートしていません。 Ubuntu 16.0.10 を使う場合は、[コマンド ライン](#linuxinstallcli)を使う必要があります。 Linux および strongSwan のバージョンによっては、次に示す例が実際に表示される画面と一致しない可能性があります。

1. **[端末]** を起動し、例のコマンドを実行して **strongSwan** とその Network Manager をインストールします。

   ```
   sudo apt install network-manager-strongswan
   ```
2. **[Settings]\(設定\)** 、 **[Network]\(ネットワーク\)** の順に選択します。

   ![接続を編集する](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. **+** ボタンをクリックして、新しい接続を作成します。

   ![接続を追加する](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. メニューから **[IPsec/IKEv2 (strongswan)]** を選び、ダブルクリックします。 この手順で使用する接続の名前を変更できます。

   ![接続の種類を選ぶ](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. ダウンロード クライアント構成ファイルに含まれる **Generic** フォルダーから **VpnSettings.xml** ファイルを開きます。 **VpnServer** というタグを検索して、"azuregateway" で始まり ".cloudapp.net" で終わる名前をコピーします。

   ![名前をコピーする](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. この名前を、 **[ゲートウェイ]** セクションの、新しい VPN 接続の **[アドレス]** フィールドに貼り付けます。 次に、 **[証明書]** フィールドの最後のフォルダー アイコンを選択して、**Generic** フォルダーに移動し、**VpnServerRoot** ファイルを選択します。
7. 接続の **[クライアント]** セクションの **[認証]** で、 **[Certificate/private key]\(証明書/秘密キー\)** を選びます。 **[証明書]** と **[秘密キー]** で、前に作成した証明書および秘密キーを選びます。 **[オプション]** で、 **[Request an inner IP address]\(内部 IP アドレスを要求する\)** をオンにします。 **[追加]** をクリックします。

   ![内部 IP アドレスを要求する](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. 接続を **[On]\(オン\)** にします。

## <a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>strongSwan のインストール

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>証明書の生成

まだ証明書を生成していない場合は、次の手順を使用します。

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>インストールと構成

1. Azure portal から VPNClient パッケージをダウンロードします。
2. ファイルを抽出します。
3. VpnServerRoot.cer を **Generic** フォルダーから /etc/ipsec.d/cacerts にコピーするか移動します。
4. cp client.p12 を /etc/ipsec.d/private/ にコピーするか移動します。 このファイルは、Azure VPN Gateway のクライアント証明書です。
5. VpnSettings.xml ファイルを開き、`<VpnServer>` の値をコピーします。 次の手順でこの値を使用します。
6. 以下のサンプルの値を調整し、/etc/ipsec.conf 構成にこのサンプルを追加します。
  
   ```
   conn azure
   keyexchange=ikev2
   type=tunnel
   leftfirewall=yes
   left=%any
   leftauth=eap-tls
   leftid=%client # use the DNS alternative name prefixed with the %
   right= Enter the VPN Server value here# Azure VPN gateway address
   rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
   rightsubnet=0.0.0.0/0
   leftsourceip=%config
   auto=add
   ```
6. 次のコードを */etc/ipsec.secrets* に追加します。

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. 次のコマンドを実行します。

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>次の手順

[P2S 構成を完了する](vpn-gateway-howto-point-to-site-rm-ps.md)ための記事に戻ります。

P2S 接続のトラブルシューティングを行うには、次の記事を参照してください。

  * [Azure ポイント対サイト接続のトラブルシューティング](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Mac OS X VPN クライアントからの VPN 接続のトラブルシューティング](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
