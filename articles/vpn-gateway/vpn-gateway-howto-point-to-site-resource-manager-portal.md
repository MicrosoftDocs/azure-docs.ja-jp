---
title: ポイント対サイト接続とネイティブ Azure 証明書認証を使用してコンピューターを Azure 仮想ネットワークに接続する:Azure Portal |Microsoft Docs
description: Azure Virtual Network に対し、P2S と自己署名証明書 (または CA によって発行された証明書) を使用して安全に Windows、Mac OS X、および Linux クライアントを接続します。 この記事では Azure Portal を使用します。
services: vpn-gateway
author: cherylmc
tags: azure-resource-manager
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 6/18/2019
ms.author: cherylmc
ms.openlocfilehash: 07bcf50a816c090ccef846909dff671486e514c4
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203061"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>ネイティブ Azure 証明書認証を使用した VNet へのポイント対サイト接続の構成:Azure ポータル

この記事では、Windows、Linux、または Mac OS X が実行されている個々のクライアントを Azure VNet に対して安全に接続する方法を紹介します。 ポイント対サイト VPN 接続は、自宅や会議室でのテレワークなど、リモートの場所から VNet に接続する場合に便利です。 VNet への接続を必要とするクライアントがごく少ない場合は、サイト対サイト VPN の代わりに P2S を使用することもできます。 ポイント対サイト接続に、VPN デバイスや公開 IP アドレスは必要ありません。 P2S により、SSTP (Secure Socket トンネリング プロトコル) または IKEv2 経由の VPN 接続が作成されます。 ポイント対サイト VPN について詳しくは、「[ポイント対サイト VPN について](point-to-site-about.md)」を参照してください。

![コンピューターを Azure VNet に接続する - ポイント対サイト接続の図](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)


## <a name="architecture"></a>アーキテクチャ

ポイント対サイトのネイティブ Azure 証明書認証接続には、以下のものが必要となります。これらの要素をこの演習で構成していきます。

* RouteBased VPN ゲートウェイ。
* Azure にアップロードされた、ルート証明書の公開キー (.cer ファイル)。 証明書をアップロードすると、その証明書は信頼された証明書と見なされ、認証に使用されます。
* ルート証明書から生成されたクライアント証明書。 VNet に接続する予定の各クライアント コンピューターにインストールされるクライアント証明書です。 この証明書はクライアントの認証に使用されます。
* VPN クライアント構成。 VPN クライアント構成ファイルには、クライアントが VNet に接続するために必要な情報が含まれています。 このファイルを使用すると、オペレーティング システムにネイティブな既存の VPN クライアントが構成されます。 接続する各クライアントは、構成ファイルの設定を使って構成する必要があります。

#### <a name="example"></a>値の例

次の値を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。

* **VNet 名:** VNet1
* **[アドレス空間]:** 192.168.0.0/16<br>この例では、1 つのアドレス空間のみを使用します。 VNet には、複数のアドレス空間を使用することができます。
* **サブネット名:** FrontEnd
* **サブネットのアドレス範囲:** 192.168.1.0/24
* **サブスクリプション:** サブスクリプションが複数ある場合は、適切なサブスクリプションを使用していることを確認します。
* **[リソース グループ]:** TestRG
* **[場所]:** East US
* **GatewaySubnet:** 192.168.200.0/24<br>
* **DNS サーバー**: (オプション) 名前解決に利用する DNS サーバーの IP アドレス。
* **仮想ネットワーク ゲートウェイ名:** VNet1GW
* **ゲートウェイの種類:** VPN
* **VPN の種類:** ルート ベース
* **パブリック IP アドレス名:** VNet1GWpip
* **[接続の種類]** : ポイント対サイト
* **クライアント アドレス プール:** 172.16.201.0/24<br>このポイント対サイト接続を利用して VNet に接続する VPN クライアントは、クライアント アドレス プールから IP アドレスを受け取ります。

## <a name="createvnet"></a>1.仮想ネットワークの作成

最初に Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。
[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-p2s-vnet-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>2.ゲートウェイ サブネットの追加

仮想ネットワークをゲートウェイに接続する前に、まず、接続先の仮想ネットワークのゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サービスでは、ゲートウェイ サブネット内に指定された IP アドレスを使用します。 将来的な構成要件も見越して十分な IP アドレスを確保するために、可能であれば、/28 または /27 の CIDR ブロックを使用してゲートウェイ サブネットを作成します。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-p2s-rm-portal-include.md)]

## <a name="dns"></a>3.DNS サーバーの指定 (省略可能)

仮想ネットワークを作成した後は、名前解決を処理するために、DNS サーバーの IP アドレスを追加できます。 DNS サーバーはこの構成ではオプションですが、名前解決が必要な場合は必須になります。 値を指定しても新しい DNS サーバーは作成されません。 指定する DNS サーバーの IP アドレスは、接続先のリソースの名前を解決できる DNS サーバーの IP アドレスである必要があります。 この例ではプライベート IP アドレスを使用していますが、これはおそらく実際の DNS サーバーの IP アドレスと一致しません。 実際には独自の値を使用してください。 指定された値は、P2S 接続や VPN のクライアントではなく、VNet にデプロイするリソースが使用します。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>4.仮想ネットワーク ゲートウェイの作成

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>Basic SKU では、IKEv2 と RADIUS 認証はサポートされません。 Mac クライアントを仮想ネットワークに接続する予定がある場合は、Basic SKU を使用しないでください。
>

## <a name="generatecert"></a>5.証明書の生成

証明書は、ポイント対サイト VPN 接続を介して VNet に接続するクライアントを認証するために、Azure によって使用されます。 ルート証明書を取得したら、公開キー情報を Azure に[アップロード](#uploadfile)します。 ルート証明書は、Azure によって "信頼された" と見なされ、P2S 経由での仮想ネットワークへの接続に使用されます。 また、信頼されたルート証明書からクライアント証明書を生成し、それを各クライアント コンピューターにインストールします。 クライアント証明書は、クライアントで VNet への接続を開始するときに、そのクライアントを認証するために使用されます。 

### <a name="getcer"></a>1.ルート証明書の .cer ファイルの取得

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>2.クライアント証明書を生成

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>6.クライアント アドレス プールの追加

クライアント アドレス プールとは、指定するプライベート IP アドレスの範囲です。 ポイント対サイト VPN 経由で接続するクライアントは、この範囲内の IP アドレスを動的に受け取ります。 接続元であるオンプレミスの場所、または接続先とする VNet と重複しないプライベート IP アドレス範囲を使用してください。

1. 仮想ネットワーク ゲートウェイが作成されたら、[仮想ネットワーク ゲートウェイ] ページの **[設定]** セクションに移動します。 **[設定]** セクションの **[ポイント対サイトの構成]** をクリックします。

   ![ポイント対サイトのページ](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/gatewayblade.png) 
2. **[今すぐ構成]** をクリックして構成ページを開きます。

   ![今すぐ構成](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configurenow.png)
3. **ポイント対サイト**の構成ページの **[アドレス プール]** ボックスに、使用するプライベート IP アドレス範囲を追加します。 VPN クライアントには、指定した範囲から動的に IP アドレスが割り当てられます。 最小のサブネット マスクは、アクティブ/パッシブ構成の場合は 29 ビット、アクティブ/アクティブ構成の場合は 28 ビットです。 設定を確認して保存するには、 **[保存]** をクリックします。

   ![クライアント アドレス プール](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png)

   >[!NOTE]
   >このポータル ページに、トンネルの種類も認証の種類も表示されない場合、ご利用のゲートウェイで使用されているのは Basic SKU です。 Basic SKU では、IKEv2 と RADIUS 認証はサポートされません。
   >

## <a name="tunneltype"></a>7.トンネルの種類の構成

トンネルの種類を選択することができます。 トンネルのオプションには、OpenVPN、SSTP、IKEv2 があります。 Android と Linux の strongSwan クライアントおよび iOS と OSX のネイティブ IKEv2 VPN クライアントでは、接続に IKEv2 トンネルのみを使用します。 Windows クライアントでは最初に IKEv2 を試し、接続できなかった場合に SSTP を使用します。 OpenVPN クライアントを使用して、OpenVPN トンネルの種類に接続することができます。

![トンネルの種類](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunneltype.png)

## <a name="authenticationtype"></a>8.認証の種類の構成

**[Azure 証明書]** を選択します。

  ![トンネルの種類](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authenticationtype.png)

## <a name="uploadfile"></a>9.ルート証明書の公開証明書データのアップロード

その後は、最大で合計 20 個まで、信頼されたルート証明書を追加でアップロードできます。 公開証明書データがアップロードされたら、Azure でそれを使用し、信頼されたルート証明書から生成されたクライアント証明書がインストールされているクライアントを認証できます。 ルート証明書の公開キー情報を Azure にアップロードします。

1. 証明書は、 **[ルート証明書]** セクションの **[ポイント対サイトの構成]** ページで追加します。
2. ルート証明書を Base 64 でエンコードされた X.509 (.cer) ファイルとしてエクスポートしたことを確認してください。 証明書をテキスト エディターで開くことができるように、この形式でエクスポートする必要があります。
3. 証明書をメモ帳などのテキスト エディターで開きます。 証明書データをコピーするときはに、必ず、テキストを復帰や改行のない 1 つの連続した行としてコピーしてください。 復帰や改行を確認するには、テキスト エディターのビューを "記号を表示する/すべての文字を表示する" ように変更することが必要になる場合があります。 次のセクションのみを 1 つの連続した行としてコピーします。

   ![証明書データ](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png)
4. **[公開証明書データ]** フィールドに証明書データを貼り付けます。 証明書に**名前**を付けて、 **[保存]** をクリックします。 最大 20 個の信頼されたルート証明書を追加できます。

   ![証明書のアップロード](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png)
5. ページの上部にある **[保存]** をクリックして構成設定をすべて保存します。

   ![保存](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png)

## <a name="installclientcert"></a>10.エクスポートしたクライアント証明書のインストール

クライアント証明書の生成に使用したクライアント コンピューター以外から P2S 接続を作成する場合は、クライアント証明書をインストールする必要があります。 クライアント証明書をインストールするときに、クライアント証明書のエクスポート時に作成されたパスワードが必要になります。

クライアント証明書が証明書チェーン全体と一緒に .pfx としてエクスポートされている (既定値) ことを確認します。 そうでないと、ルート証明書情報がクライアント コンピューターに存在せず、クライアントは正しく認証されません。

インストールの手順については、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページを参照してください。

## <a name="clientconfig"></a>11.VPN クライアント構成パッケージの生成とインストール

VPN クライアント構成ファイルには、P2S 接続を使って VNet に接続できるようにデバイスを構成するための設定が含まれています。 VPN クライアント構成ファイルの生成とインストールに関する手順については、「[ネイティブ Azure 証明書認証の P2S 構成のための VPN クライアント構成ファイルを作成およびインストールする](point-to-site-vpn-client-configuration-azure-cert.md)」を参照してください。

## <a name="connect"></a>12.Azure への接続

### <a name="to-connect-from-a-windows-vpn-client"></a>Windows VPN クライアントから接続するには

>[!NOTE]
>接続元の Windows クライアント コンピューターの管理者権限が必要です。
>
>

1. VNet に接続するには、クライアント コンピューターで [VPN 接続] に移動し、作成した VPN 接続を見つけます。 仮想ネットワークと同じ名前が付いています。 **[接続]** をクリックします。 証明書を使用することを示すポップアップ メッセージが表示される場合があります。 **[続行]** をクリックして、昇格された特権を使用します。

2. **接続**の状態ページで、 **[接続]** をクリックして接続を開始します。 **[証明書の選択]** 画面が表示された場合は、表示されているクライアント証明書が接続に使用する証明書であることを確認します。 そうでない場合は、ドロップダウン矢印を使用して適切な証明書を選択し、 **[OK]** をクリックします。

   ![Azure への VPN クライアントの接続](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. 接続が確立されました。

   ![確立された接続](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

#### <a name="troubleshoot-windows-p2s-connections"></a>Windows の P2S 接続のトラブルシューティング

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Mac の VPN クライアントから接続するには

[ネットワーク] ダイアログ ボックスで使用するクライアント プロファイルを探し、[VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac) の設定を指定して、 **[接続]** をクリックします。

手順の詳細については、[Mac (OS X) のインストール](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac)に関するセクションを参照してください。 接続に問題がある場合は、仮想ネットワーク ゲートウェイが Basic SKU を使用していないことを確認します。 Basic SKU は Mac クライアントではサポートされていません。

  ![Mac の接続](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>接続を確認するには

ここで紹介する手順は、Windows クライアントに適用されます。

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

## <a name="connectVM"></a>仮想マシンに接続するには

ここで紹介する手順は、Windows クライアントに適用されます。

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="add"></a>信頼されたルート証明書を追加または削除するには

信頼されたルート証明書を Azure に追加したり、Azure から削除したりできます。 ルート証明書を削除すると、そのルートから証明書を生成したクライアントは認証が無効となり、接続できなくなります。 クライアントの認証と接続を正常に実行できるようにするには、Azure に信頼されている (Azure にアップロードされている) ルート証明書から生成した新しいクライアント証明書をインストールする必要があります。

### <a name="to-add-a-trusted-root-certificate"></a>信頼されたルート証明書を追加するには

信頼されたルート証明書 .cer ファイルを最大 20 個まで Azure に追加できます。 手順については、この記事の[信頼されたルート証明書のアップロード](#uploadfile)に関するセクションを参照してください。

### <a name="to-remove-a-trusted-root-certificate"></a>信頼されたルート証明書を削除するには

1. 信頼されたルート証明書を削除するには、仮想ネットワーク ゲートウェイの **[ポイント対サイトの構成]** ページに移動します。
2. ページの **[ルート証明書]** セクションで、削除する証明書を見つけます。
3. 証明書の横にある省略記号をクリックし、[削除] をクリックします。

## <a name="revokeclient"></a>クライアント証明書を失効させるには

クライアント証明書は失効させることができます。 証明書失効リストを使用すると、個々のクライアント証明書に基づくポイント対サイト接続を選択して拒否することができます。 これは、信頼されたルート証明書を削除することとは異なります。 信頼されたルート証明書 .cer を Azure から削除すると、失効したルート証明書によって生成または署名されたすべてのクライアント証明書のアクセス権が取り消されます。 ルート証明書ではなくクライアント証明書を失効させることで、ルート証明書から生成されたその他の証明書は、その後も認証に使用できます。

一般的な方法としては、ルート証明書を使用してチームまたは組織レベルでアクセスを管理し、失効したクライアント証明書を使用して、個々のユーザーの細かいアクセス制御を構成します。

### <a name="revoke-a-client-certificate"></a>クライアント証明書の失効

失効リストに拇印を追加することで、クライアント証明書を失効させることができます。

1. クライアント証明書の拇印を取得します。 詳細については、「[方法: 証明書のサムプリントを取得する](https://msdn.microsoft.com/library/ms734695.aspx)」を参照してください。
2. 情報をテキスト エディターにコピーし、文字列が 1 つにつながるようにスペースをすべて削除します。
3. 仮想ネットワーク ゲートウェイの **[ポイント対サイトの構成]** ページに移動します。 このページは、[信頼されたルート証明書のアップロード](#uploadfile)に使用したものです。
4. **[失効した証明書]** セクションで、証明書のフレンドリ名を入力します (証明書 CN にする必要はありません)。
5. 拇印の文字列をコピーして **[拇印]** フィールドに貼り付けます。
6. 拇印が検証され、自動的に失効リストに追加されます。 リストが更新されていることを示すメッセージが画面に表示されます。 
7. 更新が完了した後は、証明書を接続に使用することができなくなります。 この証明書を使用して接続を試みたクライアントには、証明書が無効になっていることを示すメッセージが表示されます。

## <a name="faq"></a>ポイント対サイト接続に関してよく寄せられる質問

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>次の手順
接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](https://docs.microsoft.com/azure/) に関するページを参照してください。 ネットワークと仮想マシンの詳細については、「[Azure と Linux の VM ネットワークの概要](../virtual-machines/linux/azure-vm-network-overview.md)」を参照してください。

P2S のトラブルシューティング情報については、[Azure ポイント対サイト接続のトラブルシューティング](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)に関するページを参照してください。
