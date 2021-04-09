---
title: 'P2S VPN クライアント構成ファイルを作成してインストールする: 証明書認証'
titleSuffix: Azure VPN Gateway
description: P2S 証明書認証のために、Windows、Linux、Linux (strongSwan)、および macOS X VPN クライアント構成ファイルを作成してインストールします。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/11/2020
ms.author: cherylmc
ms.openlocfilehash: c7b186aa1a6f63b1bc3e9dbefa5001faac967762
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94556166"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>ネイティブ Azure 証明書認証の P2S 構成のための VPN クライアント構成ファイルを作成およびインストールする

VPN クライアント構成ファイルは、ZIP ファイルに含まれています。 構成ファイルでは、Windows、Mac IKEv2 VPN、Linux のネイティブ クライアントが、ネイティブ Azure 証明書認証を使用するポイント対サイト接続を介して仮想ネットワークに接続するために必要な設定を提供します。

クライアント構成ファイルは、仮想ネットワークの VPN 構成に固有です。 VPN プロトコルの種類や認証の種類など、VPN クライアント構成ファイルの生成後にポイント対サイト VPN 構成に対する変更があった場合は、ユーザー デバイス用に新しい VPN クライアント構成ファイルを生成してください。

* ポイント対サイト接続について詳しくは、「[ポイント対サイト VPN について](point-to-site-about.md)」を参照してください。
* OpenVPN の取扱説明については、[P2S 向け OpenVPN を構成する](vpn-gateway-howto-openvpn.md)方法に関するページと [OpenVPN クライアントを構成する](vpn-gateway-howto-openvpn-clients.md)方法に関するページを参照してください。

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>VPN クライアント構成ファイルの生成

開始する前に、接続するすべてのユーザーでは、有効な証明書がユーザーのデバイスにインストールされていることを確認してください。 クライアント証明書のインストールの詳細については、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページを参照してください。

PowerShell または Azure Portal を使用してクライアント構成ファイルを生成することができます。 どちらの方法でも、同じ zip ファイルが返されます。 そのファイルを解凍して、次のフォルダーを表示します。

* **WindowsAmd64** および **WindowsX86**。Windows の 32 ビットと 64 ビットのインストーラー パッケージがそれぞれに含まれています。 **WindowsAmd64** インストーラー パッケージは、Amd だけでなく、サポートされている 64 ビットの Windows クライアントを対象としています。
* **Generic**。これには、独自の VPN クライアント構成の作成に使用される全般的な情報が含まれています。 Generic フォルダーが提供されるのは、IKEv2 または SSTP+IKEv2 がゲートウェイ上で構成された場合です。 構成されているのが SSTP のみの場合、Generic フォルダーは存在しません。

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Azure Portal を使用してルールを生成する

1. Azure Portal で、接続する仮想ネットワークの仮想ネットワーク ゲートウェイに移動します。
1. 仮想ネットワーク ゲートウェイ ページで、 **[ポイント対サイトの構成]** を選択します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="VPN クライアントをダウンロードする":::
1. [ポイント対サイトの構成] ページの上部で **[VPN クライアントのダウンロード]** を選択します。 クライアント構成パッケージが生成されるまでに数分かかります。
1. お使いのブラウザーは、クライアント構成の zip ファイルが使用可能なことを示します。 ゲートウェイと同じ名前が付いています。 そのファイルを解凍して、フォルダーを表示します。

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>PowerShell を使用してファイルを生成する

1. VPN クライアント構成ファイルを生成する際、"-AuthenticationMethod" の値は "EapTls" です。 次のコマンドを使用して、VPN クライアント構成ファイルを生成します。

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. ブラウザーに URL をコピーして、zip ファイルをダウンロードし、ファイルを解凍してフォルダーを表示します。

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Azure に接続するすべての Mac で、ネイティブの IKEv2 VPN クライアントを手動で構成する必要があります。 Azure では、ネイティブの Azure 証明書の認証用の mobileconfig ファイルは提供されません。 **Generic** には、構成に必要な情報がすべて揃っています。 ダウンロードに、Generic フォルダーが表示されない場合は、IKEv2 がトンネルの種類として選択されていない可能性があります。 VPN Gateway Basic SKU は IKEv2 をサポートしていないことに注意してください。 IKEv2 を選択したら、もう一度 zip ファイルを生成して、Generic フォルダーを取得します。<br>Generic フォルダーには、次のファイルが含まれています。

* **VpnSettings.xml**。サーバー アドレスやトンネルの種類など、重要な設定が含まれています。 
* **VpnServerRoot.cer**。P2S 接続の設定中に Azure VPN ゲートウェイを検証するために必要なルート証明書が含まれています。

証明書認証用に Mac 上でネイティブ VPN クライアントを構成するには、次の手順を実行してください。 Azure に接続するすべての Mac でこれらの手順を完了する必要があります。

1. **VpnServerRoot** ルート証明書を Mac にインポートします。 これを行うには、ファイルを Mac にコピーしてダブルクリックします。 **[追加]** を選択してインポートします。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-certificate.png" alt-text="[証明書] ページを示すスクリーンショット":::
  
    >[!NOTE]
    >証明書をダブルクリックしても、 **[追加]** ダイアログが表示されない場合がありますが、証明書は正しいストアにインストールされています。 証明書のカテゴリの下にあるログイン キーチェーンで証明書を確認できます。
    >
  
1. P2S 設定を構成したときに、Azure にアップロードしたルート証明書によって発行されたクライアント証明書が、インストール済みであることを確認します。 これは、前の手順でインストールした VPNServerRoot とは異なります。 クライアント証明書は認証に使用され、必須です。 証明書の生成の詳細については、「[証明書の生成](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)」をご覧ください。 クライアント証明書のインストール方法については、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページをご覧ください。
1. **[ネットワーク]** ダイアログを開き、 **[ネットワーク環境設定]** で **[+]** を選択して、Azure 仮想ネットワークへの P2S 接続用に新しい VPN クライアント接続プロファイルを作成します。

   **[インターフェイス]** の値は "VPN"、 **[VPN タイプ]** の値は "IKEv2" です。 **[サービス名]** フィールドにプロファイルの名前を指定し、 **[作成]** を選択して VPN クライアント接続プロファイルを作成します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/network.png" alt-text="スクリーンショットには [ネットワーク] ウィンドウが表示され、インターフェイス、VPN の種類の選択、サービス名の入力オプションが表示されています":::
1. **Generic** フォルダーの **VpnSettings.xml** ファイルから、**VpnServer** タグの値をコピーします。 この値をプロファイルの **[サーバー アドレス]** フィールドと **[リモート ID]** フィールドに貼り付けます。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server.png" alt-text="サーバー情報を示すスクリーンショット。":::
1. **[認証設定]** を選択し、 **[証明書]** を選択します。 **Catalina** の場合は、 **[なし]** を選択し、 **[証明書]** を選択します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-settings.png" alt-text="認証設定を示すスクリーンショット。":::

   Catalina の場合は、 **[None]\(なし\)** を選択し、 **[Certificate]\(証明書\)** を選択します。 正しい証明書を **選択** してください。
   
   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="スクリーンショットには [ネットワーク] ウィンドウが表示され、[認証設定] に [なし] が選択され、[証明書] が選択されています。":::

1. **[選択]** をクリックして、 認証に使用するクライアント証明書を選択します。 これは、手順 2 でインストールした証明書です。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="スクリーンショットには [ネットワーク] ウィンドウに [認証設定] が表示され、ここで証明書を選択できます。":::
1. **[Choose An Identity]\(ID の選択\)** では、選択できる証明書の一覧が表示されます。 適切な証明書を選択し、 **[続ける]** を選択します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/identity.png" alt-text="[Choose An Identity]\(ID の選択\) ダイアログ ボックスのスクリーンショット。適切な証明書を選択できます。":::

1. **[ローカル ID]** フィールドに、(手順 6 の) 証明書の名前を指定します。 この例では `ikev2Client.com` です。 次に、 **[適用]** を選択して変更を保存します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/apply-connect.png" alt-text="[適用] を示すスクリーンショット。":::
1. **[ネットワーク]** ダイアログで、 **[適用]** を選択して、すべての変更を保存します。 次に、 **[接続]** を選択して、Azure 仮想ネットワークへの P2S 接続を開始します。

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>strongSwan のインストール

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>証明書の生成

まだ証明書を生成していない場合は、次の手順を使用します。

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>インストールと構成

次の手順は、Ubuntu 18.0.4 上で作成されました。 Ubuntu 16.0.10 は、strongSwan GUI をサポートしていません。 Ubuntu 16.0.10 を使う場合は、[コマンド ライン](#linuxinstallcli)を使う必要があります。 Linux および strongSwan のバージョンによっては、次に示す例が実際に表示される画面と一致しない可能性があります。

1. **[ターミナル]** を起動し、例のコマンドを実行して **strongSwan** とその Network Manager をインストールします。

   ```
   sudo apt install network-manager-strongswan
   ```
1. **[Settings]\(設定\)** 、 **[Network]\(ネットワーク\)** の順に選択します。 **+** ボタンを選択して、新しい接続を作成します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="[ネットワーク接続] ページを示すスクリーンショット。":::

1. メニューから **[IPsec/IKEv2 (strongswan)]** を選び、ダブルクリックします。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="[VPN の追加] ページを示すスクリーンショット。":::

1. **[VPN の追加]** ページで、VPN 接続の名前を追加します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="接続の種類の選択を示すスクリーンショット。":::
1. ダウンロード クライアント構成ファイルに含まれる **Generic** フォルダーから **VpnSettings.xml** ファイルを開きます。 **VpnServer** というタグを検索して、"azuregateway" で始まり ".cloudapp.net" で終わる名前をコピーします。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="データのコピーを示すスクリーンショット。":::
1. この名前を、 **[ゲートウェイ]** セクションの、新しい VPN 接続の **[アドレス]** フィールドに貼り付けます。 次に、 **[証明書]** フィールドの最後のフォルダー アイコンを選択して、**Generic** フォルダーに移動し、**VpnServerRoot** ファイルを選択します。
1. 接続の **[クライアント]** セクションの **[認証]** で、 **[Certificate/private key]\(証明書/秘密キー\)** を選びます。 **[証明書]** と **[秘密キー]** で、前に作成した証明書および秘密キーを選びます。 **[オプション]** で、 **[Request an inner IP address]\(内部 IP アドレスを要求する\)** をオンにします。 その後、 **[追加]** を選択します。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="[内部 IP アドレスを要求する] を示すスクリーンショット。":::

1. 接続を **[On]\(オン\)** にします。

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="コピーを示すスクリーンショット。":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>strongSwan のインストール

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>証明書の生成

まだ証明書を生成していない場合は、次の手順を使用します。

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>インストールと構成

1. Azure portal から VPNClient パッケージをダウンロードします。
1. ファイルを展開します。
1. **VpnServerRoot.cer** を **Generic** フォルダーから **/etc/ipsec.d/cacerts** にコピーするか移動します。
1. **cp client.p12** を **/etc/ipsec.d/private/** にコピーするか移動します。 このファイルは、VPN ゲートウェイのクライアント証明書です。
1. **VpnSettings.xml** ファイルを開き、`<VpnServer>` の値をコピーします。 次の手順でこの値を使用します。
1. 下のサンプルの値を調整し、 **/etc/ipsec.conf** 構成にこのサンプルを追加します。
  
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
1. 次の値を **/etc/ipsec.secrets** に追加します。

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. 次のコマンドを実行します。

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>次のステップ

作業元の記事に戻り、P2S の構成を完了します。

* [PowerShell の構成手順](vpn-gateway-howto-point-to-site-rm-ps.md)。
* [Azure portal の構成手順](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。
