---
title: "ポイント対サイトと証明書認証を使用してコンピューターを仮想ネットワークに接続する: Azure Portal クラシック | Microsoft Docs"
description: "Azure Portal を使用してポイント対サイト VPN ゲートウェイ接続を作成することで、クラシック Azure Virtual Network に安全に接続します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: cherylmc
ms.openlocfilehash: 7805e7c91c49fe1ef2d92b64c62bbfd15ab492b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-certificate-authentication-classic-azure-portal"></a>証明書認証 (クラシック) を使用した VNet へのポイント対サイト接続の構成 (クラシック): Azure Portal

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

この記事では、Azure Portal を使用して、ポイント対サイト接続を備えた VNet をクラシック デプロイメント モデルで作成する手順を説明します。 この構成では、証明書を使用して接続クライアントを認証します。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

ポイント対サイト (P2S) VPN ゲートウェイでは、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。 ポイント対サイト VPN 接続は、自宅や会議室でのテレワークなど、リモートの場所から VNet に接続する場合に便利です。 P2S VPN は、VNet への接続が必要なクライアントがごく少ない場合に、サイト対サイト VPN の代わりに使用するソリューションとしても便利です。 

P2S は、Secure Socket トンネリング プロトコル (SSTP) を使用します。これは、SSL ベースの VPN プロトコルです。 P2S VPN 接続は、クライアント コンピューターから接続を開始することによって確立されます。


![ポイント対サイトのダイアログ](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)


ポイント対サイトの証明書認証接続には、以下のものが必要です。

* Dynamic VPN ゲートウェイ。
* Azure にアップロードされた、ルート証明書の公開キー (.cer ファイル)。 これは信頼された証明書と見なされ、認証に使用されます。
* ルート証明書から生成され、接続する各クライアント コンピューターにインストールされたクライアント証明書。 この証明書はクライアントの認証に使用されます。
* VPN クライアント構成パッケージが生成され、接続するすべてのクライアント コンピューターにインストールされていること。 このクライアント構成パッケージによって構成されるのは、既にオペレーティング システム上で動作し、VNet への接続に必要な情報を備えているネイティブ VPN クライアントです。

ポイント対サイト接続には、VPN デバイスやオンプレミスの公開 IP アドレスは必要ありません。 VPN 接続は、SSTP (Secure Socket トンネリング プロトコル) 経由で作成されます。 サーバー側でのサポート対象の SSTP バージョンは、1.0、1.1、1.2 です。 使用するバージョンはクライアントによって決まります。 Windows 8.1 以降の場合、SSTP では既定で 1.2 が使用されます。 

ポイント対サイト接続の詳細については、この記事の最後にある「[ポイント対サイト接続に関してよく寄せられる質問](#faq)」を参照してください。

### <a name="example-settings"></a>設定例

次の値を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。

* **名前: VNet1**
* **アドレス空間: 192.168.0.0/16**<br>この例では、1 つのアドレス空間のみを使用します。 VNet には、複数のアドレス空間を使用することができます。
* **サブネット名: FrontEnd**
* **サブネットのアドレス範囲: 192.168.1.0/24**
* **サブスクリプション:** サブスクリプションが複数ある場合は、適切なサブスクリプションを使用していることを確認します。
* **リソース グループ: TestRG**
* **場所: 米国東部**
* **接続の種類: ポイント対サイト**
* **クライアント アドレス空間: 172.16.201.0/24**。 このポイント対サイト接続を利用して VNet に接続する VPN クライアントは、指定したプールから IP アドレスを受け取ります。
* **GatewaySubnet: 192.168.200.0/24**。 ゲートウェイ サブネット名は、"GatewaySubnet" にする必要があります。
* **サイズ:** 使用するゲートウェイ SKU を選択します。
* **ルーティングの種類: 動的**

## <a name="vnetvpn"></a>1.仮想ネットワークと VPN ゲートウェイを作成する

最初に Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

### <a name="createvnet"></a>パート 1: 仮想ネットワークの作成

まだ仮想ネットワークがない場合は作成します。 スクリーンショットは例として示されています。 サンプルの値は必ず実際の値に変更してください。 Azure Portal を使用して VNet を作成するには、以下の手順に従ってください。

1. ブラウザーから [Azure Portal](http://portal.azure.com) に移動します。必要であれば Azure アカウントでサインインします。
2. **[新規]**をクリックします。 **[Marketplace を検索]** フィールドに「仮想ネットワーク」と入力します。 検索結果の一覧から **[仮想ネットワーク]** を探してクリックし、**[仮想ネットワーク]** ページを開きます。

  ![[仮想ネットワーク] ページの検索](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. [仮想ネットワーク] ページの下の方にある **[デプロイ モデルの選択]** の一覧で、**[クラシック]** を選択し、**[作成]** をクリックします。

  ![デプロイメント モデルの選択](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. **[仮想ネットワークの作成]** ページで、VNet の設定を構成します。 このページでは、最初のアドレス空間と 1 つのサブネット アドレスの範囲を追加します。 VNet の作成が完了したら、戻って、さらにサブネットとアドレス空間を追加できます。

  ![[仮想ネットワークの作成] ページ](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. **サブスクリプション** が正しいものであることを確認します。 ドロップダウンを使用して、サブスクリプションを変更できます。
6. **[リソース グループ]** をクリックし、既存のリソース グループを選択するか、新しいリソース グループの名前を入力して新しく作成します。 新しいリソース グループを作成する場合は、計画した構成値に基づいて、リソース グループに名前を付けます。 リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md#resource-groups)」を参照してください。
7. 次に、VNet の **[場所]** 設定を選択します。 この場所の設定によって、この VNet にデプロイしたリソースの配置先が決まります。
8. ダッシュボードで VNet を簡単に検索できるようにするには、**[ダッシュボードにピン留めする]** を選択します。その後、**[作成]** をクリックします。

  ![[ダッシュボードにピン留めする]](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. [作成] をクリックした後で、VNet の進捗状況を反映するタイルがダッシュボードに表示されます。 タイルは、VNet の作成が進むに従って変化します。

  ![仮想ネットワーク タイルの作成](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. 仮想ネットワークが作成されると、Azure クラシック ポータルの [ネットワーク] ページの **[状態]** に **[作成済み]** と表示されます。
11. DNS サーバーを追加します (省略可)。 仮想ネットワークを作成した後は、名前解決に使用する DNS サーバーの IP アドレスを追加できます。 指定する DNS サーバーの IP アドレスは、VNet 内のリソースの名前を解決できる DNS サーバーのアドレスである必要があります。<br>DNS サーバーを追加するには、仮想ネットワークの設定を開き、[DNS サーバー] をクリックして、実際に使用する DNS サーバーの IP アドレスを追加します。

### <a name="gateway"></a>パート 2: ゲートウェイ サブネットと動的ルーティング ゲートウェイの作成

この手順では、ゲートウェイ サブネットと動的ルーティング ゲートウェイを作成します。 クラシック デプロイメント モデルの Azure Portal では、ゲートウェイ サブネットとゲートウェイを同じ構成ページで作成できます。

1. ポータルで、ゲートウェイを作成する仮想ネットワークに移動します。
2. 仮想ネットワークのページの **[概要]** ページにある [VPN 接続] セクションで、**[ゲートウェイ]** をクリックします。

  ![クリックしてゲートウェイを作成する](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. **[新しい VPN 接続]** ページで、**[ポイント対サイト]** を選択します。

  ![接続の種類 (ポイント対サイト)](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. **[クライアント アドレス空間]** で、IP アドレスの範囲を追加します。 これは、VPN クライアントが接続時に受け取る IP アドレスの範囲です。 接続元であるオンプレミスの場所、または接続先とする VNet と重複しないプライベート IP アドレス範囲を使用してください。 自動入力される範囲は削除してから、使用するプライベート IP アドレス範囲を追加することができます。

  ![クライアント アドレス空間](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. **[ゲートウェイをすぐに作成する]** チェック ボックスをオンにします。

  ![ゲートウェイをすぐに作成する](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. **[ゲートウェイの構成 (オプション)]** をクリックして、**[ゲートウェイの構成]** ページを開きます。

  ![[ゲートウェイの構成 (オプション)] をクリック](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. **[Subnet Configure required settings (サブネット構成の必須設定)]** をクリックして、**ゲートウェイ サブネット**を追加します。 /29 と同程度の小規模なゲートウェイ サブネットを作成することはできますが、少なくとも /28 または /27 以上を選択してさらに多くのアドレスが含まれる大規模なサブネットを作成することをお勧めします。 これにより、十分な数のアドレスが、将来的に必要になる可能性のある追加の構成に対応できるようになります。 ゲートウェイ サブネットを使用する場合は、ゲートウェイ サブネットにネットワーク セキュリティ グループ (NSG) を関連付けないようにしてください。 このサブネットにネットワーク セキュリティ グループを関連付けると、VPN ゲートウェイが正常に動作しなくなることがあります。

  ![GatewaySubnet の追加](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. ゲートウェイの **[サイズ]** を選択します。 このサイズは、ご使用の仮想ネットワーク ゲートウェイの SKU です。 ポータルでは、既定の SKU は **[Basic]** です。 ゲートウェイ SKU の詳細については、「[VPN Gateway の設定について](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。

  ![ゲートウェイのサイズ](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. ゲートウェイの **[ルーティングの種類]** を選択します。 P2S 構成には、**動的**なルーティングの種類が必要です。 このページの構成を終了したら、**[OK]** をクリックします。

  ![ルーティングの種類の構成](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. **[新しい VPN 接続]** ページで、ページの下部にある **[OK]** をクリックして、仮想ネットワーク ゲートウェイの作成を開始します。 選択するゲートウェイ SKU によっては、VPN ゲートウェイで処理が完了するまでに最大で 45 分かかる場合があります。

## <a name="generatecerts"></a>2.証明書の作成

証明書は、ポイント対サイト VPN の VPN クライアントを認証するために、Azure によって使用されます。 そのため、ルート証明書の公開キー情報を Azure にアップロードします。 その後、その公開キーは "信頼された" と見なされます。 信頼されたルート証明書からクライアント証明書を生成し、それを各クライアント コンピューターの [証明書 - 現在のユーザー] の [個人] 証明書ストアにインストールする必要があります。 この証明書は、クライアントで VNet への接続を開始するときに、そのクライアントを認証するために使用されます。 

自己署名証明書を使用する場合は、特定のパラメーターを使って証明書を作成する必要があります。 自己署名証明書は、[PowerShell と Windows 10](vpn-gateway-certificates-point-to-site.md) を使った手順のほか、[MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) を使って作成することができます。 自己署名ルート証明書を扱うときや、自己署名ルート証明書からクライアント証明書を生成するときは、これらの説明に記載されている手順に従うことが大切です。 説明どおりに作成しないと、証明書が P2S 接続に適合せず、接続エラーが発生します。

### <a name="cer"></a>パート 1: ルート証明書に使用する公開キー (.cer) の取得

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="genclientcert"></a>パート 2: クライアント証明書の生成

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>3.ルート証明書 .cer ファイルのアップロード

ゲートウェイが作成された後は、信頼されたルート証明書の .cer ファイル (公開キー情報を含むもの) を Azure にアップロードできます。 ルート証明書の秘密キーは、Azure にアップロードしません。 .cer ファイルがアップロードされると、Azure ではそれを使用し、信頼されたルート証明書から生成されたクライアント証明書がインストールされているクライアントを認証できます。 その後は必要に応じて、最大で合計 20 個になるまで信頼されたルート証明書を追加でアップロードできます。  

1. VNet のページの **[VPN 接続]** セクションで**クライアント**のグラフィックをクリックして、**[ポイント対サイト VPN 接続]** ページを開きます。

  ![クライアント](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. **[ポイント対サイト接続]** ページで **[証明書の管理]** をクリックして、**[証明書]** ページを開きます。<br>

  ![[証明書] ページ](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. **[証明書]** ページで **[アップロード]** をクリックして、**[証明書のアップロード]** ページを開きます。<br>

    ![[証明書のアップロード] ページ](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. フォルダーのグラフィックをクリックして、.cer ファイルを参照します。 ファイルを選択し、**[OK]** をクリックします。 ページを更新して、アップロードした証明書を **[証明書]** ページで確認します。

  ![証明書のアップロード](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>4.クライアントの構成

ポイント対サイト VPN を使って VNet に接続するには、ネイティブ Windows VPN クライアントを構成するためのパッケージをクライアントごとにインストールする必要があります。 構成パッケージは、仮想ネットワークに接続するために必要な設定を使って、ネイティブ Windows VPN クライアントを構成します。

バージョンがクライアントのアーキテクチャと一致すれば、各クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。 サポートされているクライアント オペレーティング システムの一覧については、この記事の最後にある「[ポイント対サイト接続に関してよく寄せられる質問](#faq)」を参照してください。

### <a name="generateconfigpackage"></a>パート 1: VPN クライアント構成パッケージの生成とインストール

1. Azure Portal で、VNet の **[概要]** ページの **[VPN 接続]** にあるクライアントのグラフィックをクリックして、**[ポイント対サイト VPN 接続]** ページを開きます。
2. **[ポイント対サイト VPN 接続]** ページの上部で、インストール先のクライアントのオペレーティング システムに対応するダウンロード パッケージをクリックします。

  * 64 ビット クライアントの場合は、**[VPN クライアント (64 ビット)]** を選択します。
  * 32 ビット クライアントの場合は、**[VPN クライアント (32 ビット)]** を選択します。

  ![VPN クライアント構成パッケージのダウンロード](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. パッケージが生成されたら、クライアント コンピューターにそれをダウンロードしてインストールします。 SmartScreen ポップアップが表示された場合は、**[詳細]**、**[実行]** の順にクリックしてください。 パッケージを保存して、他のクライアント コンピューターにインストールすることもできます。

### <a name="installclientcert"></a>パート 2: クライアント証明書のインストール

クライアント証明書の生成に使用したクライアント コンピューター以外から P2S 接続を作成する場合は、クライアント証明書をインストールする必要があります。 クライアント証明書をインストールするときに、クライアント証明書のエクスポート時に作成されたパスワードが必要になります。 通常は、証明書をダブルクリックするだけでインストールできます。 詳細については、「[エクスポートしたクライアント証明書のインストール](vpn-gateway-certificates-point-to-site.md#install)」を参照してください。

## <a name="connect"></a>5.Azure への接続

### <a name="connect-to-your-vnet"></a>VNet への接続

1. VNet に接続するには、クライアント コンピューターで [VPN 接続] に移動し、作成した VPN 接続を見つけます。 仮想ネットワークと同じ名前が付いています。 **[接続]**をクリックします。 証明書を使用することを示すポップアップ メッセージが表示される場合があります。 その場合、 **[続行]** をクリックして、昇格された特権を使用します。
2. **接続**の状態ページで、**[接続]** をクリックして接続を開始します。 **[証明書の選択]** 画面が表示された場合は、表示されているクライアント証明書が接続に使用する証明書であることを確認します。 そうでない場合は、ドロップダウン矢印を使用して適切な証明書を選択し、 **[OK]**をクリックします。

  ![VPN client connection](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. 接続が確立されました。

  ![確立された接続](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

#### <a name="troubleshooting-p2s-connections"></a>P2S 接続のトラブルシューティング

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="verifyvpnconnect"></a>VPN 接続の確認

1. VPN 接続がアクティブであることを確認するには、管理者特権でのコマンド プロンプトを開いて、 *ipconfig/all*を実行します。
2. 結果を表示します。 受信した IP アドレスが、VNet の作成時に指定したポイント対サイト接続アドレス範囲内のアドレスのいずれかであることに注意してください。 結果は、次の例のようになります。

  ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
  ```

## <a name="connectVM"></a>仮想マシンへの接続

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add"></a>信頼されたルート証明書を追加または削除する

信頼されたルート証明書を Azure に追加したり、Azure から削除したりできます。 ルート証明書を削除すると、そのルートから証明書を生成したクライアントは認証が無効となり、接続できなくなります。 クライアントの認証と接続を正常に実行できるようにするには、Azure に信頼されている (Azure にアップロードされている) ルート証明書から生成した新しいクライアント証明書をインストールする必要があります。

### <a name="addtrustedroot"></a>信頼されたルート証明書を追加するには

信頼されたルート証明書 .cer ファイルを最大 20 個まで Azure に追加できます。 手順については、「[セクション 3: ルート証明書 .cer ファイルのアップロード](#upload)」を参照してください。

### <a name="removetrustedroot"></a>信頼されたルート証明書を削除するには

1. VNet のページの **[VPN 接続]** セクションで**クライアント**のグラフィックをクリックして、**[ポイント対サイト VPN 接続]** ページを開きます。

  ![クライアント](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. **[ポイント対サイト接続]** ページで **[証明書の管理]** をクリックして、**[証明書]** ページを開きます。<br>

  ![[証明書] ページ](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. **[証明書]** ページで、削除する証明書の横にある省略記号をクリックし、**[削除]** をクリックします。

  ![ルート証明書の削除](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>

## <a name="revokeclient"></a>クライアント証明書の失効

クライアント証明書は失効させることができます。 証明書失効リストを使用すると、個々のクライアント証明書に基づくポイント対サイト接続を選択して拒否することができます。 これは、信頼されたルート証明書を削除する場合とは異なります。 信頼されたルート証明書 .cer を Azure から削除すると、失効したルート証明書によって生成または署名されたすべてのクライアント証明書のアクセス権が取り消されます。 ルート証明書ではなくクライアント証明書を失効させることで、ルート証明書から生成されたその他の証明書は、その後もポイント対サイト接続の認証に使用できます。

一般的な方法としては、ルート証明書を使用してチームまたは組織レベルでアクセスを管理し、失効したクライアント証明書を使用して、個々のユーザーの細かいアクセス制御を構成します。

### <a name="revokeclientcert"></a>クライアント証明書を失効させるには

失効リストに拇印を追加することで、クライアント証明書を失効させることができます。

1. クライアント証明書の拇印を取得します。 詳細については、「[方法: 証明書のサムプリントを取得する](https://msdn.microsoft.com/library/ms734695.aspx)」を参照してください。
2. 情報をテキスト エディターにコピーし、文字列が 1 つにつながるようにスペースをすべて削除します。
3. **"クラシック仮想ネットワーク名"、[ポイント対サイト VPN 接続]、[証明書]** ページの順に移動し、**[失効リスト]** をクリックして [失効リスト] ページを開きます。 
4. **[失効リスト]** ページで、**[+証明書の追加]** をクリックして **[失効リストに証明書を追加する]** ページを開きます。
5. **[失効リストに証明書を追加する]** ページで、スペースのない 1 行の連続したテキストとして証明書の拇印を貼り付けます。 ページの下部にある **[OK]** をクリックします。
6. 更新が完了した後は、証明書を接続に使用することができなくなります。 この証明書を使用して接続を試みたクライアントには、証明書が無効になっていることを示すメッセージが表示されます。

## <a name="faq"></a>ポイント対サイト接続に関してよく寄せられる質問

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-include.md)]

## <a name="next-steps"></a>次のステップ
接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) に関するページを参照してください。 ネットワークと仮想マシンの詳細については、「[Azure と Linux の VM ネットワークの概要](../virtual-machines/linux/azure-vm-network-overview.md)」を参照してください。