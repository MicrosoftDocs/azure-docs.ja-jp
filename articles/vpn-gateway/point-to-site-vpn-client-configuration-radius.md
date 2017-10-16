---
title: "P2S RADIUS 接続用の VPN クライアント構成ファイルを作成およびインストールする: PowerShell: Azure | Microsoft Docs"
description: "この記事は、RADIUS 認証を使用するポイント対サイト接続用の VPN クライアント構成ファイルを作成およびインストールする際に役立ちます。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.openlocfilehash: c56a9b06a11d25cf046a0faeb29af1d78d9a4a89
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication-preview"></a>P2S RADIUS 認証用の VPN クライアント構成ファイルを作成およびインストールする (プレビュー)

VPN クライアント構成ファイルは、ZIP ファイルに含まれています。 構成ファイルでは、Windows または Mac のネイティブ IKEv2 VPN クライアントがポイント対サイトを介して VNet に接続するために必要な設定を提供します。 RADIUS サーバーには複数の認証オプションが用意されているため、オプションごとに VPN クライアント構成が異なります。

### <a name="workflow"></a>ワークフロー

  1. P2S 接続用に Azure VPN ゲートウェイを設定する。
  2. RADIUS サーバーを認証用に設定する。 
  3. 選択した認証オプションの VPN クライアント構成を取得し、それを使用して Windows デバイスで VPN クライアントを設定する。 これにより、P2S 接続経由で Azure VNet に接続できます。

>[!IMPORTANT]
>VPN プロトコルの種類や認証の種類など、VPN クライアント構成プロファイルの生成後にポイント対サイト VPN 構成に対する変更があった場合は、新しい VPN クライアント構成を生成してユーザー デバイスにインストールする必要があります。
>
>

## <a name="adeap"></a>ユーザー名/パスワード認証

* **AD 認証:** 一般的なシナリオは、AD ドメイン認証です。 このシナリオでは、ユーザーはドメインの資格情報を使用して Azure VNet に接続します。 RADIUS AD 認証用の VPN クライアント構成ファイルを作成できます。

* **AD を使用しない認証:** AD を使用せずに、ユーザー名/パスワードによる RADIUS 認証シナリオを構成することもできます。

接続するすべてのユーザーに、RADIUS を使用して認証できるユーザー名とパスワードの資格情報があることを確認します。 作成できるのは、EAP-MSCHAPv2 のユーザー名/パスワード認証プロトコルの構成のみです。 "-AuthenticationMethod" は、"EapMSChapv2" として指定されます。

### <a name="usernamefiles"></a>VPN クライアント構成ファイルの生成

次のコマンドを使用して VPN クライアント構成を作成します。

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
このコマンドを実行すると、リンクが返されます。 このリンクをコピーして Web ブラウザーに貼り付け、"VpnClientConfiguration.zip" という ZIP ファイルをダウンロードします。 ファイルを解凍して、次のフォルダーを表示します。 
 
* "WindowsAmd64" および "WindowsX86" という名前のフォルダー。 これらのフォルダーにはそれぞれ、Windows の 64 ビットと 32 ビットのインストーラー パッケージが含まれています。 
* "GenericDevice" という名前のフォルダー。 このフォルダーには、独自の VPN クライアント構成の作成に使用される全般的な情報が含まれています。 このフォルダーは無視してください。
* 仮想ネットワーク ゲートウェイの作成時に IKEv2 を構成した場合は、"mobileconfig" という名前のファイルを含む "Mac" という名前のフォルダーが表示されます。 このファイルは、Mac クライアントの構成に使用されます。

既に作成済みのクライアント構成ファイルを取得できます。 "Get-AzureRmVpnClientConfiguration" コマンドレットを実行すると、VpnClientConfiguration.zip ファイルをダウンロードできる URL (リンク) が返されます。 VPN プロトコルの種類や認証の種類など、P2S VPN 構成に変更を加えた場合、構成は自動的に更新されません。 "New-AzureRmVpnClientConfiguration" コマンドレットを実行して構成を作成し直す必要があります。

以前に生成されたクライアント構成ファイルを取得するには、次のコマンドを使用します。

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW"
```
 
### <a name="adwincli"></a>Windows VPN クライアントの設定

バージョンがクライアントのアーキテクチャと一致する限り、各 Windows クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。 サポートされているクライアント オペレーティング システムの一覧については、[FAQ](vpn-gateway-vpn-faq.md#P2S) に関するページのポイント対サイトに関するセクションを参照してください。

証明書認証用にネイティブ Windows VPN クライアントを構成するには、次の手順を実行してください。

1. Windows コンピューターのアーキテクチャに対応する VPN クライアント構成ファイルを選択します。 64 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupAmd64" インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupX86" インストーラー パッケージを選択します。 
2. パッケージをダブルクリックしてインストールします。 SmartScreen ポップアップが表示された場合は、**[詳細]**、**[実行]** の順にクリックしてください。
3. クライアント コンピューターで **[ネットワークの設定]** に移動し、**[VPN]** をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。 

### <a name="admaccli"></a>Mac (OSX) VPN クライアントの設定

1. **VpnClientSetup mobileconfig** ファイルを選択し、各ユーザーに送信します。 これを行うには、電子メールや別の方法を使用できます。

2. Mac で **mobileconfig** ファイルを探します。

  ![mobilconfig ファイルの検索](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. プロファイルをダブルクリックしてインストールし、**[続ける]** をクリックします。 プロファイル名は VNet の名前と同じです。

  ![インストール](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. **[続ける]** をクリックしてプロファイルの送信者を信頼し、インストールを続行します。

  ![続ける](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. プロファイルのインストール中に、VPN 認証に使用されるユーザー名とパスワードを指定するオプションが表示されます。 この情報の入力は必須ではありません。 指定した場合は、その情報が保存され、接続の開始時に自動的に使用されます。 **[インストール]** をクリックして続行します。

  ![[設定]](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. コンピューターにプロファイルをインストールするために必要な特権のユーザー名とパスワードを入力します。 **[OK]**をクリックします。

  ![ユーザー名とパスワード](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. インストールが完了すると、プロファイルは **[プロファイル]** ダイアログ ボックスに表示されます。 このダイアログは、後で **[システム環境設定]** から開くこともできます。

  ![システム環境設定]](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. VPN 接続にアクセスするには、**[システム環境設定]** から **[ネットワーク]** ダイアログを開きます。

  ![ネットワーク](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. VPN 接続は、"**IkeV2-VPN**" として表示されます。 この名前は、**mobileconfig** ファイルを更新することで変更できます。

  ![connection](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. **[認証設定]**をクリックします。 ドロップダウンで **[ユーザー名]** を選択し、資格情報を入力します。 以前に資格情報を入力した場合は、ドロップダウンで **[ユーザー名]** が自動的に選択され、ユーザー名とパスワードがあらかじめ入力されています。 **[OK]** をクリックして設定を保存します。 [ネットワーク] ダイアログ ボックスに戻ります。

  ![authenticate](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. **[適用]** をクリックして変更を保存します。 接続を開始するには、**[接続]** をクリックします。

## <a name="certeap"></a>証明書認証
 
EAP-TLS プロトコルを使用する RADIUS 証明書認証用の VPN クライアント構成ファイルを作成できます。 通常、企業が発行した証明書を使用して、VPN のユーザーを認証します。 接続するすべてのユーザーでユーザーのデバイスに証明書がインストールされていることと、その証明書が RADIUS サーバーで検証できることを確認します。
 
* "-AuthenticationMethod" は "EapTls" です。
* 証明書認証時に、クライアントはその証明書を検証することで RADIUS サーバーを検証します。 -RadiusRootCert は、RADIUS サーバーの検証に使用されるルート証明書を含む .cer ファイルです。  
* 場合によっては、Windows デバイスに複数のクライアント証明書が存在することがあります。 そのため、認証中に、すべての証明書の一覧を示すポップアップ ダイアログが表示される場合があります。 その後、ユーザーは、使用する証明書を選択する必要があります。 適切な証明書は、クライアント証明書からチェーンする必要のあるルート証明書を指定することで除外できます。 "-ClientRootCert" は、ルート証明書を含む .cer ファイルです。これは、省略可能なパラメーターです。 接続元のデバイスにクライアント証明書が 1 つしかない場合、このパラメーターを指定する必要はありません。

### <a name="certfiles"></a>VPN クライアント構成ファイルの生成

次のコマンドを使用して VPN クライアント構成を作成します。
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root>
```

このコマンドレットの結果でリンクが返されます。 このリンクをコピーして Web ブラウザーに貼り付け、"VpnClientConfiguration.zip" という ZIP ファイルをダウンロードします。 ファイルを解凍して、次のフォルダーを表示します。

* "WindowsAmd64" および "WindowsX86" という名前のフォルダー。 これらのフォルダーにはそれぞれ、Windows の 64 ビットと 32 ビットのインストーラー パッケージが含まれています。 
* "GenericDevice" という名前のフォルダー。 このフォルダーには、独自の VPN クライアント構成の作成に使用される全般的な情報が含まれています。

既に作成済みのクライアント構成ファイルを取得できます。 "Get-AzureRmVpnClientConfiguration" コマンドレットを実行すると、VpnClientConfiguration.zip ファイルをダウンロードできる URL (リンク) が返されます。 VPN プロトコルの種類や認証の種類など、P2S VPN 構成に変更を加えた場合、構成は自動的に更新されません。 "New-AzureRmVpnClientConfiguration" コマンドレットを実行して構成を作成し直す必要があります。

以前に生成されたクライアント構成ファイルを取得するには、次のコマンドを使用します。

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW"
```
 
### <a name="certwincli"></a>Windows VPN クライアントの設定

1. 構成パッケージを選択し、クライアント デバイスにインストールします。 64 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupAmd64" インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupX86" インストーラー パッケージを選択します。 SmartScreen ポップアップが表示された場合は、**[詳細]**、**[実行]** の順にクリックしてください。 パッケージを保存して、他のクライアント コンピューターにインストールすることもできます。
2. クライアント コンピューターで **[ネットワークの設定]** に移動し、**[VPN]** をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。

### <a name="certmaccli"></a>MAC VPN クライアントの設定

Azure VNet に接続する Mac デバイスごとに、個別のプロファイルを作成する必要があります。 これは、これらのデバイスでは、認証用のユーザー証明書をプロファイルで指定する必要があるためです。 **Generic** フォルダーには、プロファイルの作成に必要な情報がすべて揃っています。

  * **VpnSettings.xml** には、サーバー アドレスやトンネルの種類など、重要な設定が含まれています。
  * **VpnServerRoot.cer** には、P2S 接続の設定中に VPN ゲートウェイを検証するために必要なルート証明書が含まれています。
  * **RadiusServerRoot.cer** には、認証時に RADIUS サーバーの検証に必要なルート証明書が含まれています。

証明書認証用に Mac 上でネイティブ VPN クライアントを構成するには、次の手順を実行してください。

1. **VpnServerRoot** および **RadiusServerRoot** ルート証明書を Mac にインポートします。 これを行うには、ファイルを Mac にコピーしてダブルクリックします。  
**[追加]** をクリックしてインポートします。

  **VpnServerRoot の追加**

  ![証明書の追加](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  **RadiusServerRoot の追加**

  ![証明書の追加](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. **[ネットワーク]** ダイアログを開き、**[ネットワーク環境設定]** で **[+]** をクリックして、Azure VNet への P2S 接続用に新しい VPN クライアント接続プロファイルを作成します。

  **[インターフェイス]** の値は "VPN"、**[VPN タイプ]** の値は "IKEv2" です。 **[サービス名]** フィールドにプロファイルの名前を指定し、**[作成]** をクリックして VPN クライアント接続プロファイルを作成します。

  ![ネットワーク](./media/point-to-site-vpn-client-configuration-radius/network.png)
3. **Generic** フォルダーの **VpnSettings.xml** ファイルから、**VpnServer** タグの値をコピーします。 この値をプロファイルの **[サーバー アドレス]** フィールドと **[リモート ID]** フィールドに貼り付けます。 **[ローカル ID]** フィールドを空白のままにします。

  ![サーバー情報](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
4. **[認証設定]** をクリックし、**[証明書]** を選択します。 

  ![認証設定](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
5. **[選択]** をクリックして、 認証に使用する証明書を選択します。

  ![証明書](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
6. **[Choose An Identity]\(ID の選択\)** では、選択できる証明書の一覧が表示されます。 適切な証明書を選択し、**[続ける]** をクリックします。

  ![ID](./media/point-to-site-vpn-client-configuration-radius/identity.png)
7. **[ローカル ID]** フィールドに、(手順 6 の) 証明書の名前を指定します。 この例では、"ikev2Client.com" です。 次に、**[適用]** ボタンをクリックして変更を保存します。

  ![apply](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
8. **[ネットワーク]** ダイアログで、**[適用]** をクリックしてすべての変更を保存します。 次に、**[接続]** をクリックして、Azure VNet への P2S 接続を開始します。

## <a name="otherauth"></a>他の認証の種類またはプロトコル

ユーザー名/パスワードまたは証明書ではなく別の認証の種類 (OTP など) を使用するか、別の認証プロトコル (EAP-MSCHAPv2 の代わりに PEAP-MSCHAPv2 など) を使用するには、独自の VPN クライアント構成プロファイルを作成する必要があります。 プロファイルを作成するには、仮想ネットワーク ゲートウェイの IP アドレス、トンネルの種類、分割トンネルのルートなどの情報が必要です。 この情報は、次の手順を使用して取得できます。

1. "Get-AzureRmVpnClientConfiguration" コマンドレットを使用して、EapMSChapv2 用の VPN クライアント構成を生成します。 手順については、この記事の[こちらのセクション](#ccradius)を参照してください。

2. VpnClientConfiguration.zip ファイルを解凍し、GenenericDevice フォルダーを探します。 64 ビットおよび 32 ビットのアーキテクチャ用の Windows インストーラーが含まれているフォルダーは無視してください。
 
3. GenenericDevice フォルダーには、VpnSettings という XML ファイルが含まれています。 このファイルには、必要な情報がすべて含まれています。

  * VpnServer - Azure VPN ゲートウェイの FQDN。 これは、クライアントの接続先となるアドレスです。
  * VpnType - 接続に使用するトンネルの種類。
  * Routes - Azure VNet のバインドされたトラフィックのみが P2S トンネル経由で送信されるように、プロファイル内で構成する必要があるルート。
  * GenenericDevice フォルダーには、"VpnServerRoot" という .cer ファイルも含まれています。 このファイルには、P2S 接続の設定中に Azure VPN ゲートウェイを検証するために必要なルート証明書が含まれています。 Azure VNet に接続するすべてのデバイスに証明書をインストールします。 
 
## <a name="next-steps"></a>次のステップ

[P2S 構成を完了する](point-to-site-how-to-radius-ps.md)ための記事に戻ります。