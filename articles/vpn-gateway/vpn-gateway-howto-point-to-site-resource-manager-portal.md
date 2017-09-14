---
title: "ポイント対サイトと証明書認証を使用してコンピューターを仮想ネットワークに接続する: Azure Portal | Microsoft Docs"
description: "証明書認証を使用してポイント対サイト VPN ゲートウェイ接続を作成することで、コンピューターを Azure Virtual Network に安全に接続します。 この記事は、Resource Manager デプロイメント モデルに適用されます。また、ここでは Azure Portal が使用されます。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: cc9018d95ffce3b5b4a5ee20d5c78a2122e0223e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/31/2017

---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-certificate-authentication-azure-portal"></a>証明書認証を使用した VNet へのポイント対サイト接続の構成: Azure Portal

この記事では、Azure Portal を使用して、ポイント対サイト接続を備えた VNet を Resource Manager デプロイメント モデルで作成する手順を説明します。 この構成では、証明書を使用して接続クライアントを認証します。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

ポイント対サイト (P2S) VPN ゲートウェイでは、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。 ポイント対サイト VPN 接続は、自宅や会議室でのテレワークなど、リモートの場所から VNet に接続する場合に便利です。 P2S VPN は、VNet への接続が必要なクライアントがごく少ない場合に、サイト対サイト VPN の代わりに使用するソリューションとしても便利です。 

P2S は、Secure Socket トンネリング プロトコル (SSTP) を使用します。これは、SSL ベースの VPN プロトコルです。 P2S VPN 接続は、クライアント コンピューターから接続を開始することによって確立されます。

![ポイント対サイトのダイアログ](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

ポイント対サイトの証明書認証接続には、以下のものが必要です。

* RouteBased VPN ゲートウェイ。
* Azure にアップロードされた、ルート証明書の公開キー (.cer ファイル)。 証明書をアップロードすると、その証明書は信頼された証明書と見なされ、認証に使用されます。
* ルート証明書から生成され、VNet に接続する各クライアント コンピューターにインストールされたクライアント証明書。 この証明書はクライアントの認証に使用されます。
* VPN クライアント構成パッケージ。 VPN クライアント構成パッケージには、クライアントが VNet に接続するために必要な情報が含まれています。 このパッケージを使用すると、Windows オペレーティング システムにネイティブな既存の VPN クライアントが構成されます。 接続する各クライアントは、構成パッケージを使用して構成する必要があります。

ポイント対サイト接続には、VPN デバイスやオンプレミスの公開 IP アドレスは必要ありません。 VPN 接続は、SSTP (Secure Socket トンネリング プロトコル) 経由で作成されます。 サーバー側でのサポート対象の SSTP バージョンは、1.0、1.1、1.2 です。 使用するバージョンはクライアントによって決まります。 Windows 8.1 以降の場合、SSTP では既定で 1.2 が使用されます。

ポイント対サイト接続の詳細については、この記事の最後にある「[ポイント対サイト接続に関してよく寄せられる質問](#faq)」を参照してください。

#### <a name="example"></a>値の例

次の値を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。

* **VNet 名:** VNet1
* **アドレス空間:** 192.168.0.0/16<br>この例では、1 つのアドレス空間のみを使用します。 VNet には、複数のアドレス空間を使用することができます。
* **サブネット名:** FrontEnd
* **サブネットのアドレス範囲:** 192.168.1.0/24
* **サブスクリプション:** サブスクリプションが複数ある場合は、適切なサブスクリプションを使用していることを確認します。
* **リソース グループ:** TestRG
* **場所:** 米国東部
* **GatewaySubnet:** 192.168.200.0/24<br>
* **DNS サーバー**: (オプション) 名前解決に利用する DNS サーバーの IP アドレス。
* **仮想ネットワーク ゲートウェイ名:** VNet1GW
* **ゲートウェイの種類:** VPN
* **VPN の種類:** ルート ベース
* **パブリック IP アドレス名:** VNet1GWpip
* **接続の種類:** ポイント対サイト
* **クライアント アドレス プール:** 172.16.201.0/24<br>このポイント対サイト接続を利用して VNet に接続する VPN クライアントは、クライアント アドレス プールから IP アドレスを受け取ります。

## <a name="createvnet"></a>1.仮想ネットワークの作成

最初に Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-p2s-vnet-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>2.ゲートウェイ サブネットの追加

仮想ネットワークをゲートウェイに接続する前に、まず、接続先の仮想ネットワークのゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サービスでは、ゲートウェイ サブネット内に指定された IP アドレスを使用します。 将来的な構成要件も見越して十分な IP アドレスを確保するために、可能であれば、/28 または /27 の CIDR ブロックを使用してゲートウェイ サブネットを作成します。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-p2s-rm-portal-include.md)]

## <a name="dns"></a>3.DNS サーバーの指定 (省略可能)

仮想ネットワークを作成した後は、名前解決を処理するために、DNS サーバーの IP アドレスを追加できます。 DNS サーバーはこの構成ではオプションですが、名前解決が必要な場合は必須になります。 値を指定しても新しい DNS サーバーは作成されません。 指定する DNS サーバーの IP アドレスは、接続先のリソースの名前を解決できる DNS サーバーの IP アドレスである必要があります。 この例ではプライベート IP アドレスを使用していますが、これはおそらく実際の DNS サーバーの IP アドレスと一致しません。 実際には独自の値を使用してください。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>4.仮想ネットワーク ゲートウェイの作成

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

## <a name="generatecert"></a>5.証明書の生成

証明書は、ポイント対サイト VPN 接続を介して VNet に接続するクライアントを認証するために、Azure によって使用されます。 ルート証明書を取得したら、公開キー情報を Azure に[アップロード](#uploadfile)します。 ルート証明書は、Azure によって "信頼された" と見なされ、P2S 経由での仮想ネットワークへの接続に使用されます。 また、信頼されたルート証明書からクライアント証明書を生成し、それを各クライアント コンピューターにインストールします。 クライアント証明書は、クライアントで VNet への接続を開始するときに、そのクライアントを認証するために使用されます。 

### <a name="getcer"></a>1.ルート証明書の .cer ファイルの取得

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>2.クライアント証明書を生成

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>6.クライアント アドレス プールの追加

クライアント アドレス プールとは、指定するプライベート IP アドレスの範囲です。 ポイント対サイト VPN 経由で接続するクライアントは、この範囲内の IP アドレスを受け取ります。 接続元であるオンプレミスの場所、または接続先とする VNet と重複しないプライベート IP アドレス範囲を使用してください。

1. 仮想ネットワーク ゲートウェイが作成されたら、[仮想ネットワーク ゲートウェイ] ページの **[設定]** セクションに移動します。 **[設定]** セクションで **[ポイント対サイトの構成]** をクリックして、**[ポイント対サイトの構成]** ページを開きます。

  ![ポイント対サイトのページ](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/gatewayblade.png)
2. **[ポイント対サイトの構成]** ページでは、自動入力される範囲を削除し、使用するプライベート IP アドレス範囲を追加することができます。 設定を確認して保存するには、**[保存]** をクリックします。

  ![クライアント アドレス プール](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>7.ルート証明書の公開証明書データのアップロード

ゲートウェイが作成された後で、ルート証明書の公開キー情報を Azure にアップロードします。 公開証明書データがアップロードされたら、Azure でそれを使用し、信頼されたルート証明書から生成されたクライアント証明書がインストールされているクライアントを認証できます。 その後は、最大で合計 20 個まで、信頼されたルート証明書を追加でアップロードできます。

1. 証明書は、**[ルート証明書]** セクションの **[ポイント対サイトの構成]** ページで追加します。  
2. ルート証明書を Base 64 でエンコードされた X.509 (.cer) ファイルとしてエクスポートしたことを確認してください。 証明書をテキスト エディターで開くことができるように、この形式でエクスポートする必要があります。
3. 証明書をメモ帳などのテキスト エディターで開きます。 証明書データをコピーするときはに、必ず、テキストを復帰や改行のない 1 つの連続した行としてコピーしてください。 復帰や改行を確認するには、テキスト エディターのビューを "記号を表示する/すべての文字を表示する" ように変更することが必要になる場合があります。 次のセクションのみを 1 つの連続した行としてコピーします。

  ![証明書データ](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)
4. **[公開証明書データ]** フィールドに証明書データを貼り付けます。 証明書に**名前**を付けて、**[保存]** をクリックします。 最大 20 個の信頼されたルート証明書を追加できます。

  ![証明書のアップロード](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="clientconfig"></a>8.VPN クライアント構成パッケージの生成とインストール

ポイント対サイト VPN を使用して VNet に接続するには、仮想ネットワークに接続するために必要な設定とファイルを使用してネイティブ VPN クライアントを構成するクライアント構成パッケージを各クライアントにインストールする必要があります。 VPN クライアント構成パッケージは、ネイティブ Windows VPN クライアントを構成するもので、新規または別の VPN クライアントをインストールするものではありません。

バージョンがクライアントのアーキテクチャと一致すれば、各クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。 サポートされているクライアント オペレーティング システムの一覧については、この記事の最後にある「[ポイント対サイト接続に関してよく寄せられる質問](#faq)」を参照してください。

### <a name="1-generate-and-download-the-client-configuration-package"></a>1.クライアント構成パッケージを生成およびダウンロードする

1. **[ポイント対サイトの構成]** ページで **[VPN クライアントのダウンロード]** をクリックして、**[VPN クライアントのダウンロード]** ページを開きます。 パッケージの生成には 1 ～ 2 分かかります。

  ![VPN クライアントのダウンロード 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. クライアント用の適切なパッケージを選択して、**[ダウンロード]** をクリックします。 構成パッケージ ファイルを保存します。 この VPN クライアント構成パッケージは、仮想ネットワークに接続する各クライアント コンピューターにインストールします。

  ![VPN クライアントのダウンロード 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpnclient.png)

### <a name="2-install-the-client-configuration-package"></a>2.クライアント構成パッケージをインストールする

1. 仮想ネットワークに接続するコンピューターのローカルに構成ファイルをコピーします。 
2. .exe ファイルをダブルクリックしてパッケージをクライアント コンピューターにインストールします。 構成パッケージを作成したのは皆さん自身であり、署名されていないために警告が表示されることがあります。 Windows SmartScreen ポップアップが表示された場合は、**[詳細]** (左側)、**[実行]** の順にクリックして、パッケージをインストールします。
3. パッケージをクライアント コンピューターにインストールします。 Windows SmartScreen ポップアップが表示された場合は、**[詳細]** (左側)、**[実行]** の順にクリックして、パッケージをインストールします。
4. クライアント コンピューターで **[ネットワークの設定]** に移動し、**[VPN]** をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。

## <a name="installclientcert"></a>9.エクスポートしたクライアント証明書のインストール

クライアント証明書の生成に使用したクライアント コンピューター以外から P2S 接続を作成する場合は、クライアント証明書をインストールする必要があります。 クライアント証明書をインストールするときに、クライアント証明書のエクスポート時に作成されたパスワードが必要になります。 通常は、証明書をダブルクリックするだけでインストールできます。

クライアント証明書が証明書チェーン全体と一緒に .pfx としてエクスポートされている (既定値) ことを確認します。 そうでないと、ルート証明書情報がクライアント コンピューターに存在せず、クライアントは正しく認証されません。 詳細については、「[エクスポートしたクライアント証明書のインストール](vpn-gateway-certificates-point-to-site.md#install)」を参照してください。

## <a name="connect"></a>10.Azure への接続

1. VNet に接続するには、クライアント コンピューターで [VPN 接続] に移動し、作成した VPN 接続を見つけます。 仮想ネットワークと同じ名前が付いています。 **[接続]**をクリックします。 証明書を使用することを示すポップアップ メッセージが表示される場合があります。 **[続行]** をクリックして、昇格された特権を使用します。

2. **接続**の状態ページで、**[接続]** をクリックして接続を開始します。 **[証明書の選択]** 画面が表示された場合は、表示されているクライアント証明書が接続に使用する証明書であることを確認します。 そうでない場合は、ドロップダウン矢印を使用して適切な証明書を選択し、 **[OK]**をクリックします。

  ![Azure への VPN クライアントの接続](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. 接続が確立されました。

  ![確立された接続](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

#### <a name="troubleshooting-p2s-connections"></a>P2S 接続のトラブルシューティング

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify"></a>11.接続を確認する

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

## <a name="add"></a>信頼されたルート証明書を追加または削除する

信頼されたルート証明書を Azure に追加したり、Azure から削除したりできます。 ルート証明書を削除すると、そのルートから証明書を生成したクライアントは認証が無効となり、接続できなくなります。 クライアントの認証と接続を正常に実行できるようにするには、Azure に信頼されている (Azure にアップロードされている) ルート証明書から生成した新しいクライアント証明書をインストールする必要があります。

### <a name="to-add-a-trusted-root-certificate"></a>信頼されたルート証明書を追加するには

信頼されたルート証明書 .cer ファイルを最大 20 個まで Azure に追加できます。 手順については、この記事の[信頼されたルート証明書のアップロード](#uploadfile)に関するセクションを参照してください。

### <a name="to-remove-a-trusted-root-certificate"></a>信頼されたルート証明書を削除するには

1. 信頼されたルート証明書を削除するには、仮想ネットワーク ゲートウェイの **[ポイント対サイトの構成]** ページに移動します。
2. ページの **[ルート証明書]** セクションで、削除する証明書を見つけます。
3. 証明書の横にある省略記号をクリックし、[削除] をクリックします。

## <a name="revokeclient"></a>クライアント証明書の失効

クライアント証明書は失効させることができます。 証明書失効リストを使用すると、個々のクライアント証明書に基づくポイント対サイト接続を選択して拒否することができます。 これは、信頼されたルート証明書を削除することとは異なります。 信頼されたルート証明書 .cer を Azure から削除すると、失効したルート証明書によって生成または署名されたすべてのクライアント証明書のアクセス権が取り消されます。 ルート証明書ではなくクライアント証明書を失効させることで、ルート証明書から生成されたその他の証明書は、その後も認証に使用できます。

一般的な方法としては、ルート証明書を使用してチームまたは組織レベルでアクセスを管理し、失効したクライアント証明書を使用して、個々のユーザーの細かいアクセス制御を構成します。

### <a name="to-revoke-a-client-certificate"></a>クライアント証明書を失効させるには

失効リストに拇印を追加することで、クライアント証明書を失効させることができます。

1. クライアント証明書の拇印を取得します。 詳細については、「[方法: 証明書のサムプリントを取得する](https://msdn.microsoft.com/library/ms734695.aspx)」を参照してください。
2. 情報をテキスト エディターにコピーし、文字列が 1 つにつながるようにスペースをすべて削除します。
3. 仮想ネットワーク ゲートウェイの **[ポイント対サイトの構成]** ページに移動します。 このページは、[信頼されたルート証明書のアップロード](#uploadfile)に使用したものです。
4. **[失効した証明書]** セクションで、証明書のフレンドリ名を入力します (証明書 CN にする必要はありません)。
5. 拇印の文字列をコピーして **[拇印]** フィールドに貼り付けます。
6. 拇印が検証され、自動的に失効リストに追加されます。 リストが更新されていることを示すメッセージが画面に表示されます。 
7. 更新が完了した後は、証明書を接続に使用することができなくなります。 この証明書を使用して接続を試みたクライアントには、証明書が無効になっていることを示すメッセージが表示されます。

## <a name="faq"></a>ポイント対サイト接続に関してよく寄せられる質問

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-include.md)]

## <a name="next-steps"></a>次のステップ
接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) に関するページを参照してください。 ネットワークと仮想マシンの詳細については、「[Azure と Linux の VM ネットワークの概要](../virtual-machines/linux/azure-vm-network-overview.md)」を参照してください。
