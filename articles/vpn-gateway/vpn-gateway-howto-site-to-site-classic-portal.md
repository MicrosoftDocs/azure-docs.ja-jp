---
title: "オンプレミスのネットワークを Azure 仮想ネットワークに接続する: サイト間 VPN (クラシック): ポータル | Microsoft Docs"
description: "パブリック インターネットを経由したオンプレミスのネットワークから Azure 仮想ネットワークへの IPsec 接続を作成する手順。 これらの手順は、ポータルを使用してクロスプレミスのサイト間 VPN Gateway 接続を作成する際に役立ちます。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 619ea430b13c16e8e4338413613d5798f36458ba
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Azure Portal を使用してサイト間接続を作成する (クラシック)
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [クラシック - Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [クラシック - クラシック ポータル](vpn-gateway-site-to-site-create.md)
>
>


サイト間 (S2S) VPN ゲートウェイ接続とは、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介した接続です。 この種類の接続では、オンプレミスの VPN デバイスが必要です。そのデバイスは、パブリック IP アドレスを割り当てられていて、NAT の内側に配置されていない必要があります。 サイト間接続は、クロスプレミスおよびハイブリッド構成に使用できます。

この記事では、クラシック デプロイメント モデルと Azure Portal を使用して、仮想ネットワークと、オンプレミス ネットワークに対するサイト間 VPN ゲートウェイ接続を作成する手順について説明します。 

![クロスプレミスのサイト間 VPN Gateway 接続の図](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>サイト間接続のデプロイメント モデルとデプロイ方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

以下の表は、サイト間の構成に関して現在利用できるデプロイメント モデルとデプロイ方法を示しています。 構成手順を説明した記事が利用できるようになったら、表から直接リンクできるようにします。

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>追加の構成
VNet どうしは接続しても、オンプレミスへの接続は作成しない場合は、 [VNet 間の接続の構成](virtual-networks-configure-vnet-to-vnet-connection.md)に関するページを参照してください。 既存の接続が存在する VNet にサイト間接続を追加する場合は、[VPN Gateway 接続が既に存在する VNet へのサイト間接続の追加](vpn-gateway-multi-site.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に
構成を開始する前に、以下が揃っていることを確認します。

* 互換性のある VPN デバイスおよびデバイスを構成できる人員。 「 [VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照してください。 VPN デバイスの構成に詳しくない場合や、オンプレミス ネットワーク構成の IP アドレス範囲を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。
* VPN デバイスの外部接続用パブリック IPv4 IP アドレス。 この IP アドレスを NAT の内側に割り当てることはできません。
* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](http://azure.microsoft.com/pricing/free-trial)にサインアップしてください。
* 現時点では、共有キーの指定と VPN ゲートウェイ接続の作成に PowerShell が必要です。 Azure サービス管理 (SM) PowerShell コマンドレットの最新版をインストールしてください。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。 この構成に PowerShell を使用する場合は、必ず管理者として実行するようにしてください。 

> [!NOTE]
> サイト間接続を構成するときには、VPN デバイスに公開 IPv4 IP アドレスが必要です。                                                                                                                                                                               
>
>

### <a name="values"></a>この演習のサンプル構成値
以下の手順を練習として使用する場合は、次のサンプル構成値を使用してください。

* **VNet の名前:** TestVNet1
* **アドレス空間:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (この演習では省略可能)
* **サブネット:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (この演習では省略可能)
* **GatewaySubnet:** 10.11.255.0/27
* **リソース グループ:** TestRG1
* **場所:** 米国東部
* **DNS サーバー:** 8.8.8.8 (この演習では省略可能)
* **ローカル サイト名:** Site2

## <a name="CreatVNet"></a>1.仮想ネットワークの作成

S2S 接続に使用する仮想ネットワークを作成する際には、指定するアドレス空間が、接続先のローカル サイトのクライアント アドレス空間と重複しないようにする必要があります。 サブネットの重複があると、接続が適切に動作しません。

* 既に VNet がある場合は、設定が VPN ゲートウェイの設計に適合していることを確認します。 特に、他のネットワークと重複している可能性のあるサブネットに注意してください。 

* まだ仮想ネットワークがない場合は作成します。 スクリーンショットは例として示されています。 サンプルの値は必ず実際の値に変更してください。

### <a name="to-create-a-virtual-network"></a>仮想ネットワークを作成するには

1. ブラウザーから [Azure Portal](http://portal.azure.com) に移動します。必要であれば Azure アカウントでサインインします。
2. **[新規]**をクリックします。 **[Marketplace を検索]** フィールドに「仮想ネットワーク」と入力します。 検索結果の一覧から **[仮想ネットワーク]** を探してクリックし、**[仮想ネットワーク]** ブレードを開きます。

    ![[仮想ネットワーク] ブレードの検索](./media/vpn-gateway-howto-site-to-site-classic-portal/newvnetportal700.png)
3. [仮想ネットワーク] ブレードの下の方にある **[デプロイ モデルの選択]** の一覧で、**[クラシック]** を選択し、**[作成]** をクリックします。

    ![デプロイメント モデルの選択](./media/vpn-gateway-howto-site-to-site-classic-portal/selectmodel.png)
4. **[仮想ネットワークの作成]** ブレードで、VNet の設定を構成します。 このブレードでは、最初のアドレス空間と 1 つのサブネット アドレスの範囲を追加します。 VNet の作成が完了したら、戻って、さらにサブネットとアドレス空間を追加できます。

    ![[仮想ネットワークの作成] ブレード](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "[仮想ネットワークの作成] ブレード")
5. **サブスクリプション** が正しいものであることを確認します。 ドロップダウンを使用して、サブスクリプションを変更できます。
6. **[リソース グループ]** をクリックし、既存のリソース グループを選択するか、新しいリソース グループの名前を入力して新しく作成します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md#resource-groups)」を参照してください。
7. 次に、VNet の **[場所]** 設定を選択します。 この場所の設定によって、この VNet にデプロイしたリソースの配置先が決まります。
8. ダッシュボードで VNet を簡単に検索できるようにするには、**[ダッシュボードにピン留めする]** を選択します。その後、**[作成]** をクリックします。

    ![ダッシュボードにピン留めする](./media/vpn-gateway-howto-site-to-site-classic-portal/pintodashboard150.png "ダッシュボードにピン留めする")
9. [作成] をクリックした後で、VNet の進捗状況を反映するタイルがダッシュボードに表示されます。 タイルは、VNet の作成が進むに従って変化します。

    ![仮想ネットワークの作成タイル](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "仮想ネットワークの作成")

仮想ネットワークが作成されると、Azure クラシック ポータルの [ネットワーク] ページの **[状態]** に **[作成済み]** と表示されます。

## <a name="additionaladdress"></a>2.アドレス空間の追加

仮想ネットワークを作成したら、アドレス空間を追加できます。 S2S 構成ではアドレス空間の追加は必須の手順ではありませんが、複数のアドレス空間が必要な場合、次の手順を使用してください。

1. ポータルで仮想ネットワークを見つけます。
2. その仮想ネットワークのブレードで、**[設定]** セクションの **[アドレス空間]** をクリックします。
3. [アドレス空間] ブレードで **[+追加]** をクリックし、追加のアドレス空間を入力します。
 
## <a name="dns"></a>3.DNS サーバーの指定
S2S 構成では DNS の設定は必須の手順ではありませんが、名前解決を行う場合は DNS が必要になります。

仮想ネットワークを作成した後は、名前解決を処理するために、DNS サーバーの IP アドレスを追加できます。 仮想ネットワークの設定を開き、DNS サーバーをクリックして、名前解決に使用する DNS サーバーの IP アドレスを追加します。 この設定で、DNS サーバーは作成されません。 この例の設定ではパブリック DNS サーバーを使用します。 通常は、プライベート DNS サーバーが使用されます。 リソースが通信できる DNS サーバーを必ず追加してください。

1. ポータルで仮想ネットワークを見つけます。
2. その仮想ネットワークのブレードで、**[設定]** セクションの **[DNS サーバー]** をクリックします。
3. DNS サーバーを追加します。
4. ページの上部にある **[保存]** をクリックして設定を保存します。
 
## <a name="localsite"></a>4.ローカル サイトの構成

通常、ローカル サイトとはオンプレミスの場所を指します。 ここには、接続の作成先となる VPN デバイスの IP アドレスのほか、VPN ゲートウェイ経由で VPN デバイスにルーティングされる IP アドレス範囲が含まれます。

1. ポータルで、ゲートウェイを作成する仮想ネットワークに移動します。
2. 仮想ネットワークのブレードの **[概要]** ブレードにある [VPN 接続] セクションで **[ゲートウェイ]** をクリックし、**[新しい VPN 接続]** ブレードを開きます。

    ![クリックしてゲートウェイ設定を構成する](./media/vpn-gateway-howto-site-to-site-classic-portal/beforegw125.png "クリックしてゲートウェイ設定を構成する")
3. **[新しい VPN 接続]** ブレードで、**[サイト対サイト]** を選択します。

    ![[サイト対サイト] をクリックする](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site.png "サイト対サイト")
4. **[ローカル サイト] の [必要な設定の構成]** をクリックして、**[ローカル サイト]** ブレードを開きます。 設定を構成してから **[OK]** をクリックし、設定を保存します。
    - **[名前]:** 特定しやすいようにローカル サイトに名前を付けます。
    - **[VPN ゲートウェイの IP アドレス]:** これは、オンプレミス ネットワークの VPN デバイスのパブリック IP アドレスです。 VPN デバイスには IPv4 パブリック IP アドレスが必要です。 接続先となる VPN デバイスに有効なパブリック IP アドレスを指定します。 これは NAT の内側とすることができず、Azure から到達可能でなければなりません。
    - **[クライアント アドレス空間]:** このゲートウェイ経由でオンプレミスのローカル ネットワークにルーティングする IP アドレス範囲を一覧表示します。 複数のアドレス領域の範囲を追加することができます。 ここで指定する範囲は、仮想ネットワークの接続先となる他のネットワークの範囲、または仮想ネットワーク自体のアドレス範囲と重複しないようにしてください。

    ![ローカル サイト](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "ローカル サイトの構成")

## <a name="gatewaysubnet"></a>5.ゲートウェイ サブネットの構成

VPN ゲートウェイのゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サブネットには、VPN ゲートウェイ サービスが使用する IP アドレスが含まれます。

1. **[新しい VPN 接続]** ブレードで、**[ゲートウェイをすぐに作成する]** チェック ボックスをオンにします。 [ゲートウェイの構成 (オプション)] ブレードが表示されます。 チェック ボックスをオンにしないと、ゲートウェイ サブネットを構成するブレードが表示されません。

    ![ゲートウェイの構成の [サブネット、サイズ、ルーティングの種類]](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "ゲートウェイの構成の [サブネット、サイズ、ルーティングの種類]")
2. **[ゲートウェイの構成 (オプション)] の [サブネット、サイズ、ルーティングの種類]** をクリックして、**[ゲートウェイの構成]** ブレードを開きます。
3. **[ゲートウェイの構成]** ブレードで、**[サブネット] の [必要な設定の構成]** をクリックして、**[サブネットの追加]** ブレードを開きます。

    ![[ゲートウェイの構成] のゲートウェイ サブネット](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "[ゲートウェイの構成] のゲートウェイ サブネット")
4. **[サブネットの追加]** ブレードでゲートウェイ サブネットを追加します。 可能であれば、ゲートウェイ サブネットを追加するときに、/28 または /27 の CIDR ブロックを使用してゲートウェイ サブネットを作成することをお勧めします。 そうすることで、将来的な構成要件に対応するのに十分な IP アドレスを確保できます。  **[OK]** をクリックして設定を保存します。

    ![ゲートウェイ サブネットの追加](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "ゲートウェイ サブネットの追加")

## <a name="sku"></a>6.SKU と VPN の種類の指定
1. ゲートウェイの **[サイズ]** を選択します。 これは、仮想ネットワーク ゲートウェイの作成に使用するゲートウェイ SKU です。 ポータルでは、"既定の SKU" は **[Basic]** です。 ゲートウェイ SKU の詳細については、「[VPN Gateway の設定について](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。

    ![SKU と VPN の種類の選択](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "SKU と VPN の種類の選択")
2. ゲートウェイの **[ルーティングの種類]** を選択します。 これは VPN の種類とも呼ばれます。 ゲートウェイの種類は切り替えることができないため、適切な種類を選択する必要があります。 VPN デバイスは、選択するルーティングの種類に適合している必要があります。 VPN の種類の詳細については、[VPN Gateway の設定](vpn-gateway-about-vpn-gateway-settings.md#vpntype)に関するページを参照してください。 "RouteBased" と "PolicyBased" という VPN の種類の項目がありますが、 "動的" は "RouteBased"、"静的" は "PolicyBased" に対応しています。
3. **[OK]** をクリックして設定を保存します。
4. **[新しい VPN 接続]** ブレードで、ブレードの下部にある **[OK]** をクリックして、仮想ネットワーク ゲートウェイの作成を開始します。 完了するまで最大 45 分かかることがあります。

## <a name="vpndevice"></a>7.VPN デバイスの構成

構成に関する具体的な情報についてはお使いのデバイスの製造元の情報を参照し、デバイスの構成を行ってください。 Azure で正しく動作する VPN デバイスの詳細については、「 [VPN デバイス](vpn-gateway-about-vpn-devices.md) 」を参照してください。 さらに、使用する VPN デバイスに関して、[デバイスの互換性に関する既知の問題](vpn-gateway-about-vpn-devices.md#known)がないかどうかを確認してください。 

VPN デバイスを構成するとき、作成した VPN ゲートウェイの IP アドレスが必要になります。 この IP アドレスを見つけるには、仮想ネットワークの **[概要]** ブレードに移動します。

## <a name="CreateConnection"></a>8.接続の作成
この手順では、共有キーを設定して接続を作成します。 設定するキーは、VPN デバイスの構成で使用したものと同じである必要があります。

> [!NOTE]
> 現時点では、この手順は Azure Portal で実行できません。 サービス管理 (SM) バージョンの Azure PowerShell コマンドレットを使用する必要があります。                                                                                                                                                                             
>
>

### <a name="step-1-connect-to-your-azure-account"></a>手順 1. Azure アカウントに接続する

1. 管理者特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

        Login-AzureRmAccount
2. アカウントのサブスクリプションを確認します。

        Get-AzureRmSubscription
3. 複数のサブスクリプションがある場合は、使用するサブスクリプションを選択します。

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. SM バージョンの PowerShell コマンドレットを追加します。

        Add-AzureAccount

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>手順 2. 共有キーを設定して接続を作成する

PowerShell とクラシック デプロイメント モデルを使用する場合、ポータルのリソースの名前が、PowerShell の使用時に Azure で想定されている名前と異なる場合があります。 次の手順は、ネットワーク構成ファイルをエクスポートして、名前の正確な値を取得するのに役立ちます。

1. コンピューターにディレクトリを作成し、ネットワーク構成ファイルをそのディレクトリにエクスポートします。 この例では、ネットワーク構成ファイルは C:\AzureNet にエクスポートされます。

         Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
2. XML エディターでネットワーク構成ファイルを開いて、"LocalNetworkSite name" と "VirtualNetworkSite name" の値を確認します。 これらの値を例に反映させます。 スペースを含む名前を指定するときは、単一引用符を使って値を囲みます。

3. 共有キーを設定して接続を作成します。 "-SharedKey" は、生成して指定する値です。 この例では "abc123" を使いましたが、さらに複雑な値を生成して使うこともできます (推奨)。 重要なのは、ここで指定する値は、VPN デバイスを構成する際に指定した値と同じにする必要があることです。

        Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
        -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
接続が作成されたら、結果として **Status: Successful** が表示されます。

## <a name="verify"></a>9.接続を確認する

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="next-steps"></a>次のステップ
接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) に関するページを参照してください。


