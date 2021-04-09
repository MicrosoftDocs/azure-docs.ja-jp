---
title: Azure VPN Gateway:VPN クライアント構成ファイルを作成してインストールする - P2S RADIUS 接続
description: RADIUS 認証を使用する接続用の Windows、OS X、Linux の VPN クライアント構成ファイルを作成します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e6d811e19bb19c8c8bf96764cfcca2b1294f4a85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91440062"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>P2S RADIUS 認証用の VPN クライアント構成ファイルを作成およびインストールする

仮想ネットワークにポイント対サイト (P2S) 接続するには、接続元のクライアント デバイスを構成する必要があります。 Windows、OS X、Linux のクライアント デバイスから P2S VPN 接続を作成できます。 

RADIUS 認証を使用する場合、認証には複数のオプションがあります (ユーザー名/パスワード認証、証明書認証、その他の認証の種類)。 VPN クライアント構成は、認証の種類ごとに異なります。 VPN クライアントを構成するには、必要な設定を含むクライアント構成ファイルを使用します。 この記事は、使用する RADIUS 認証の種類用の VPN クライアント構成を作成してインストールするのに役立ちます。

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

P2S RADIUS 認証の構成ワークフローは次のとおりです。

1. [P2S 接続用に Azure VPN ゲートウェイを設定する](point-to-site-how-to-radius-ps.md)。
2. [RADIUS サーバーを認証用に設定する](point-to-site-how-to-radius-ps.md#radius)。 
3. **選択した認証オプションの VPN クライアント構成を取得し、それを使用して VPN クライアントを設定する** (この記事)。
4. [P2S 構成を完了し、接続する](point-to-site-how-to-radius-ps.md)。

>[!IMPORTANT]
>VPN プロトコルの種類や認証の種類など、VPN クライアント構成プロファイルの生成後にポイント対サイト VPN 構成に変更があった場合は、新しい VPN クライアント構成を生成してユーザー デバイスにインストールする必要があります。
>
>

この記事のセクションを使用するには、最初に使用する認証の種類 (ユーザー名/パスワード、証明書、またはその他の認証の種類) を決定します。 各セクションに、Windows、OS X、Linux 向けの手順があります (現時点では、利用できる手順は限られています)。


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>ユーザー名/パスワード認証

ユーザー名/パスワード認証を構成して、Active Directory を使用するか、または使用しないようにできます。 いずれかのシナリオで、接続するすべてのユーザーに、RADIUS を使用して認証できるユーザー名とパスワードの資格情報があることを確認します。

ユーザー名/パスワード認証を構成する場合、EAP-MSCHAPv2 のユーザー名/パスワード認証プロトコルの構成のみを作成できます。 コマンドでは、`-AuthenticationMethod` は `EapMSChapv2` です。

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a> 1.VPN クライアント構成ファイルの生成

Azure portal または Azure PowerShell を使用して VPN クライアント構成ファイルを生成することができます。

#### <a name="azure-portal"></a>Azure portal

1. 仮想ネットワーク ゲートウェイに移動します。
2. **[ポイント対サイトの構成]** をクリックします。
3. **[VPN クライアントのダウンロード]** をクリックします。
4. クライアントを選択し、要求された情報をすべて入力します。
5. **[ダウンロード]** をクリックして .zip ファイルを生成します。
6. .zip ファイルが (通常であれば [ダウンロード] フォルダーに) ダウンロードされます。

#### <a name="azure-powershell"></a>Azure PowerShell

ユーザー名/パスワード認証で使用する VPN クライアント構成ファイルを生成します。 VPN クライアント構成ファイルの生成には、次のコマンドを使用します。

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
コマンドを実行すると、リンクが返されます。 このリンクをコピーして Web ブラウザーに貼り付け、**VpnClientConfiguration.zip** をダウンロードします。 そのファイルを解凍して、次のフォルダーを表示します。 
 
* **WindowsAmd64** および **WindowsX86**: これらのフォルダーにはそれぞれ、Windows の 64 ビットと 32 ビットのインストーラー パッケージが含まれています。 
* **Generic**: このフォルダーには、独自の VPN クライアント構成を作成するのに使用する、全般的な情報が含まれています。 このフォルダーは、ユーザー名/パスワード認証の構成には不要です。
* **Mac**: 仮想ネットワーク ゲートウェイの作成時に IKEv2 を構成した場合は、**mobileconfig** ファイルを含む **Mac** という名前のフォルダーが表示されます。 このファイルを使用して、Mac クライアントを構成します。

クライアント構成ファイルを既に作成してある場合は、`Get-AzVpnClientConfiguration` コマンドレットを使用して取得できます。 ただし、VPN プロトコルの種類や認証の種類など、P2S VPN 構成に変更を加える場合、構成は自動的に更新されません。 `New-AzVpnClientConfiguration` コマンドレットを実行して、新しい構成のダウンロードを作成する必要があります。

以前に生成されたクライアント構成ファイルを取得するには、次のコマンドを使用します。

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2.VPN クライアントの構成

次の VPN クライアントを構成できます。

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [strongSwan を使用する Linux](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Windows VPN クライアント設定

バージョンがクライアントのアーキテクチャと一致する限り、各 Windows クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。 サポートされているクライアント オペレーティング システムの一覧については、[FAQ](vpn-gateway-vpn-faq.md#P2S) を参照してください。

証明書認証用にネイティブ Windows VPN クライアントを構成するには、次の手順を実行してください。

1. Windows コンピューターのアーキテクチャに対応する VPN クライアント構成ファイルを選択します。 64 ビットのプロセッサ アーキテクチャの場合は、**VpnClientSetupAmd64** インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、**VpnClientSetupX86** インストーラー パッケージを選択します。 
2. パッケージをインストールするには、ダブルクリックします。 SmartScreen ポップアップが表示されたら、 **[詳細]**  >  **[実行]** の順に選択します。
3. クライアント コンピューターで **[ネットワークの設定]** に移動し、 **[VPN]** を選択します。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Mac (OS X) VPN クライアント設定

1. **VpnClientSetup mobileconfig** ファイルを選択し、各ユーザーに送信します。 電子メールや別の方法を使用できます。

2. Mac で **mobileconfig** ファイルを探します。

   ![mobileconfig ファイルの場所](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. オプションの手順 - カスタム DNS を指定する場合は、**mobileconfig** ファイルに次の行を追加します。

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        </array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. プロファイルをダブルクリックしてインストールし、 **[続ける]** を選択します。 プロファイル名は、仮想ネットワークの名前と同じです。

   ![インストール メッセージ](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. **[続ける]** を選択してプロファイルの送信者を信頼し、インストールを続行します。

   ![Confirmation message](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. プロファイルのインストール中に、VPN 認証用のユーザー名とパスワードを指定するオプションがあります。 この情報の入力は必須ではありません。 指定した場合は、その情報が保存され、接続の開始時に自動的に使用されます。   **[インストール]** を選択して続行します。

   ![VPN のためのユーザー名とパスワード ボックス](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. コンピューターにプロファイルをインストールするために必要な特権のユーザー名とパスワードを入力します。 **[OK]** を選択します。

   ![プロファイルのインストールのためのユーザー名とパスワード ボックス](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. プロファイルのインストールが完了すると、 **[プロファイル]** ダイアログ ボックスに表示されます。 このダイアログ ボックスは、後で **[システム環境設定]** から開くこともできます。

   !["プロファイル" ダイアログ ボックス](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. VPN 接続にアクセスするには、 **[システム環境設定]** から **[ネットワーク]** ダイアログ ボックスを開きます。

   ![システム環境設定のアイコン](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. VPN 接続は、**IkeV2-VPN** として表示されます。 この名前は、**mobileconfig** ファイルを更新することで変更できます。

    ![VPN 接続の詳細](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. **[認証設定]** を選択します。 一覧で **[ユーザー名]** を選択し、資格情報を入力します。 以前に資格情報を入力した場合は、一覧で **[ユーザー名]** が自動的に選択され、ユーザー名とパスワードがあらかじめ入力されています。 **[OK]** を選択して設定を保存します。

    ![[ユーザー名] が選択されている [認証設定] ドロップダウンを示すスクリーンショット。](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. **[ネットワーク]** ダイアログ ボックスに戻り、 **[適用]** を選択して、変更を保存します。 接続を開始するには、 **[接続]** を選択します。

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>strongSwan を使用した Linux VPN クライアントの設定

次の手順は、Ubuntu 17.0.4 上で strongSwan 5.5.1 を使用して作成されました。 実際の画面は、使用している Linux と strongSwan のバージョンによって異なる場合があります。

1. **[ターミナル]** を起動し、例のコマンドを実行して **strongSwan** とその Network Manager をインストールします。 `libcharon-extra-plugins` に関連するエラーが発生する場合、これを `strongswan-plugin-eap-mschapv2` に置き換えます。

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. **ネットワーク マネージャー** アイコン (上矢印/下矢印) を選択して、 **[接続の編集]** を選択します。

   ![ネットワーク マネージャーでの "接続の編集" の選択](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. **[追加]** ボタンを選択して、新しい接続を作成します。

   ![接続の "追加" ボタン](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. ドロップダウン メニューから **[IPsec/IKEv2 (strongswan)]** を選択して、 **[作成]** を選択します。 この手順で使用する接続の名前を変更できます。

   ![接続の種類の選択](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. ダウンロード クライアント構成ファイルの **Generic** フォルダーから **VpnSettings.xml** ファイルを開きます。 `VpnServer` というタグを探して、その `azuregateway` で始まり `.cloudapp.net` で終わる名前をコピーします。

   ![VpnSettings.xml ファイルの内容](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. この名前を、 **[ゲートウェイ]** セクションの、新しい VPN 接続の **[アドレス]** フィールドに貼り付けます。 次に、 **[証明書]** フィールドの最後のフォルダー アイコンを選択して、**Generic** フォルダーに移動し、**VpnServerRoot** ファイルを選択します。
7. 接続の **[クライアント]** セクションで、 **[認証]** に対して **[EAP]** を選び、ユーザー名とパスワードを入力します。 この情報を保存するには、右側のロック アイコンを選択する必要がある場合があります。 次に、 **[保存]** を選択します。

   ![接続設定の編集](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. **ネットワーク マネージャー** アイコン (上矢印/下矢印) を選択して、 **[VPN 接続]** にマウス ポインターを合わせます。 作成した VPN 接続が表示されます。 接続を開始するには、これを選択します。

   ![ネットワーク マネージャーでの "VPN Radius" 接続](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>証明書認証
 
EAP-TLS プロトコルを使用する RADIUS 証明書認証用の VPN クライアント構成ファイルを作成できます。 通常、企業が発行した証明書を使用して、VPN のユーザーを認証します。 接続するすべてのユーザーのデバイスに証明書がインストールされていることと、その証明書が RADIUS サーバーで検証できることを確認します。

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

コマンドでは、`-AuthenticationMethod` は `EapTls` です。 証明書認証時に、クライアントはその証明書を検証することで RADIUS サーバーを検証します。 `-RadiusRootCert` は、RADIUS サーバーの検証に使用されるルート証明書を含む .cer ファイルです。

各 VPN クライアント デバイスには、インストールされたクライアント証明書が必要です。 場合によっては、Windows デバイスに複数のクライアント証明書が存在することがあります。 そのため、認証中に、すべての証明書の一覧を示すポップアップ ダイアログ ボックスが表示される場合があります。 その後、ユーザーは、使用する証明書を選択する必要があります。 適切な証明書は、クライアント証明書からチェーンする必要のあるルート証明書を指定することで除外できます。 

`-ClientRootCert` は、ルート証明書を含む .cer ファイルです。 これは、省略可能なパラメーターです。 接続元にするデバイスがクライアント証明書を 1 つしか持っていない場合は、このパラメーターを指定する必要はありません。

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1.VPN クライアント構成ファイルの生成

証明書認証で使用する VPN クライアント構成ファイルを生成します。 VPN クライアント構成ファイルの生成には、次のコマンドを使用します。
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

コマンドを実行すると、リンクが返されます。 このリンクをコピーして Web ブラウザーに貼り付け、VpnClientConfiguration.zip をダウンロードします。 そのファイルを解凍して、次のフォルダーを表示します。

* **WindowsAmd64** および **WindowsX86**: これらのフォルダーにはそれぞれ、Windows の 64 ビットと 32 ビットのインストーラー パッケージが含まれています。 
* **GenericDevice**: このフォルダーには、独自の VPN クライアント構成を作成するのに使用する、全般的な情報が含まれています。

クライアント構成ファイルを既に作成してある場合は、`Get-AzVpnClientConfiguration` コマンドレットを使用して取得できます。 ただし、VPN プロトコルの種類や認証の種類など、P2S VPN 構成に変更を加える場合、構成は自動的に更新されません。 `New-AzVpnClientConfiguration` コマンドレットを実行して、新しい構成のダウンロードを作成する必要があります。

以前に生成されたクライアント構成ファイルを取得するには、次のコマンドを使用します。

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2.VPN クライアントの構成

次の VPN クライアントを構成できます。

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (サポート対象、まだ記事の手順はありません)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Windows VPN クライアント設定

1. 構成パッケージを選択し、クライアント デバイスにインストールします。 64 ビットのプロセッサ アーキテクチャの場合は、**VpnClientSetupAmd64** インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、**VpnClientSetupX86** インストーラー パッケージを選択します。 SmartScreen ポップアップが表示されたら、 **[詳細]**  >  **[実行]** の順に選択します。 パッケージを保存して、他のクライアント コンピューターにインストールすることもできます。
2. 各クライアントには、認証のためにクライアント証明書が必要です。 クライアント証明書をインストールします。 クライアント証明書については、[ポイント対サイトのクライアント証明書](vpn-gateway-certificates-point-to-site.md)に関するページを参照してください。 生成された証明書をインストールするには、「[Install a certificate on Windows clients](point-to-site-how-to-vpn-client-install-azure-cert.md)」 (Windows クライアントに証明書をインストールする) を参照してください。
3. クライアント コンピューターで **[ネットワークの設定]** に移動し、 **[VPN]** を選択します。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Mac (OS X) VPN クライアント設定

Azure 仮想ネットワークに接続するすべての Mac デバイスごとに、個別のプロファイルを作成する必要があります。 これは、これらのデバイスでは、認証用のユーザー証明書をプロファイルで指定する必要があるためです。 **Generic** フォルダーには、プロファイルの作成に必要な情報がすべて揃っています。

* **VpnSettings.xml** には、サーバー アドレスやトンネルの種類など、重要な設定が含まれています。
* **VpnServerRoot.cer** には、P2S 接続の設定中に VPN ゲートウェイを検証するために必要なルート証明書が含まれています。
* **RadiusServerRoot.cer** には、認証時に RADIUS サーバーの検証に必要なルート証明書が含まれています。

証明書認証用に Mac 上でネイティブ VPN クライアントを構成するには、次の手順を実行してください。

1. **VpnServerRoot** および **RadiusServerRoot** ルート証明書を Mac にインポートします。 各ファイルを Mac にコピーし、ダブルクリックして、 **[追加]** を選択します。

   ![VpnServerRoot 証明書の追加](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![RadiusServerRoot 証明書の追加](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. 各クライアントには、認証のためにクライアント証明書が必要です。 クライアント デバイスにクライアント証明書をインストールします。
3. **[ネットワーク環境設定]** の下で **[ネットワーク]** ダイアログ ボックスを開きます。 **[+]** を選択して、Azure 仮想ネットワークへの P2S 接続用に、新しい VPN クライアント接続プロファイルを作成します。

   **[インターフェイス]** の値は **VPN**、 **[VPN タイプ]** の値は **IKEv2** です。 **[サービス名]** ボックスにプロファイルの名前を指定し、 **[作成]** を選択して VPN クライアント接続プロファイルを作成します。

   ![インターフェイスおよびサービス名の情報](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. **Generic** フォルダーの **VpnSettings.xml** ファイルから、**VpnServer** タグの値をコピーします。 この値を、プロファイルの **[サーバー アドレス]** ボックスと **[リモート ID]** ボックスに貼り付けます。 **[ローカル ID]** ボックスを空白のままにします。

   ![サーバー情報](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. **[認証設定]** を選択し、 **[証明書]** を選択します。 

   ![[認証設定]](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. **[選択]** をクリックして、認証に使用する証明書を選択します。

   ![認証用の証明書の選択](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **[Choose An Identity]\(ID の選択\)** では、選択できる証明書の一覧が表示されます。 適切な証明書を選択し、 **[続ける]** を選択します。

   !["ID の選択" 一覧](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. **[ローカル ID]** ボックスに、(手順 6 の) 証明書の名前を指定します。 この例では、**ikev2Client.com** です。 次に、 **[適用]** ボタンを選択して変更を保存します。

   !["ローカル ID" ボックス](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. **[ネットワーク]** ダイアログ ボックスで、 **[適用]** を選択して、すべての変更を保存します。 次に、 **[接続]** を選択して、Azure 仮想ネットワークへの P2S 接続を開始します。

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>他の認証の種類またはプロトコルを使用する

別の認証の種類 (OTP など) を使用するか、別の認証プロトコル (EAP-MSCHAPv2 の代わりに PEAP-MSCHAPv2 など) を使用するには、独自の VPN クライアント構成プロファイルを作成する必要があります。 プロファイルを作成するには、仮想ネットワーク ゲートウェイの IP アドレス、トンネルの種類、分割トンネルのルートなどの情報が必要です。 この情報は、次の手順を実行することで取得できます。

1. `Get-AzVpnClientConfiguration` コマンドレットを使用して、EapMSChapv2 用の VPN クライアント構成を生成します。

2. VpnClientConfiguration.zip ファイルを解凍し、**GenericDevice** フォルダーを探します。 64 ビットおよび 32 ビットのアーキテクチャ用の Windows インストーラーが含まれているフォルダーは、無視します。
 
3. **GenericDevice** フォルダーには、**VpnSettings** という XML ファイルが含まれています。 このファイルには、必要な情報がすべて含まれています。

   * **VpnServer**: Azure VPN Gateway の FQDN。 これは、クライアントの接続先となるアドレスです。
   * **VpnType**: 接続に使用するトンネルの種類。
   * **Routes**: Azure 仮想ネットワーク宛てのトラフィックのみが P2S トンネル経由で送信されるように、プロファイル内で構成する必要があるルート。
   
   **GenericDevice** フォルダーには、**VpnServerRoot** という .cer ファイルも含まれています。 このファイルには、P2S 接続の設定中に Azure VPN ゲートウェイを検証するために必要なルート証明書が含まれています。 Azure 仮想ネットワークに接続するすべてのデバイスに、証明書をインストールします。

## <a name="next-steps"></a>次のステップ

[P2S 構成を完了する](point-to-site-how-to-radius-ps.md)ための記事に戻ります。

P2S のトラブルシューティング情報については、[Azure ポイント対サイト接続のトラブルシューティング](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)に関するページを参照してください。
