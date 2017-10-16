---
title: "ExpressRoute を使用して Azure Stack を Azure に接続する"
description: "ExpressRoute を使用して Azure Stack 内の仮想ネットワークを Azure 内の仮想ネットワークに接続する方法です。"
services: azure-stack
documentationcenter: 
author: victorar
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: aa6973939c6cfe0688f5781fdcea5d39670249df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>ExpressRoute を使用して Azure Stack を Azure に接続する

*適用先: Azure Stack 統合システムおよび Azure Stack Development Kit*

Azure Stack 内の仮想ネットワークを Azure 内の仮想ネットワークに接続する 2 つの方法をサポートしています。
   * **サイト間**

     IPsec (IKE v1 および IKE v2) 経由での VPN 接続。 この種類の接続には、VPN デバイスまたは RRAS が必要です。 詳細については、「[VPN を使用して Azure Stack を Azure に接続する](azure-stack-connect-vpn.md)」を参照してください。
   * **ExpressRoute**

     Azure Stack デプロイから Azure への直接接続。 ExpressRoute は、公共のインターネットを経由する VPN 接続では**ありません**。 Azure ExpressRoute の詳細については、[ExpressRoute の概要](../expressroute/expressroute-introduction.md)に関するページを参照してください。

この記事では、ExpressRoute を使用して Azure Stack を Azure に接続する例を示します。
## <a name="requirements"></a>必要条件
ExpressRoute を使用して Azure Stack を Azure に接続するための特定の要件を次に示します。
* Azure で ExpressRoute 回線と VNet を作成するための Azure サブスクリプション。
* [接続プロバイダー](../expressroute/expressroute-locations.md)を経由する、プロビジョニングされている ExpressRoute 回路。
* WAN ポートに ExpressRoute 回線が接続されているルーター。
* ルーターの LAN 側は Azure Stack マルチテナント ゲートウェイにリンクされています。
* ルーターの LAN インターフェイスと Azure Stack マルチテナント ゲートウェイの間でサイト間 VPN 接続がサポートされている必要があります。
* Azure Stack デプロイで複数のテナントが追加される場合は、ルーターで複数の VRF (Virtual Routing and Forwarding) を作成できる必要があります。

次の図は、構成完了後のネットワークの概念図です。

![概念図](media/azure-stack-connect-expressroute/Conceptual.png)

**図 1**

次のアーキテクチャ ダイアグラムは、複数のテナントが ExpressRoute ルーターを介して Azure Stack インフラストラクチャから Microsoft エッジで Azure に接続する仕組みを示しています。

![アーキテクチャ ダイアグラム](media/azure-stack-connect-expressroute/Architecture.png)

**図 2**

この記事の例では、同じアーキテクチャを使用して、ExpressRoute のプライベート ピアリング経由で Azure に接続します。 これは、Azure Stack 内の仮想ネットワーク ゲートウェイから ExpressRoute ルーターへのサイト間 VPN 接続を使用して行われます。 この記事内の次の手順では、Azure Stack 内の 2 つの異なるテナントにある 2 つの VNet から Azure 内の対応する VNet へのエンド ツー エンド接続を作成する方法を示します。 テナント VNet を追加して各テナントごとに手順を繰り返したり、1 つのテナント VNet だけをデプロイしたりすることもできます。

## <a name="configure-azure-stack"></a>Azure Stack の構成
ここでは、Azure Stack 環境をテナントとして設定するのに必要なリソースを作成します。 次の手順は、実行する必要のある操作を表しています。 これらの手順では、Azure Stack ポータルを使用してリソースを作成する方法を示していますが、PowerShell を使用することもできます。

![ネットワーク リソースの手順](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>開始する前に
構成を開始する前に、次のものが必要です。
* Azure Stack デプロイ。

   Azure Stack Development Kit をデプロイする方法については、「[Azure Stack 開発キット デプロイ クイックスタート](azure-stack-deploy-overview.md)」を参照してください。
* ユーザーがサブスクライブできる Azure Stack 上のプラン。

  手順については、「[Azure Stack ユーザーが仮想マシンを使用できるようにする](azure-stack-tutorial-tenant-vm.md)」を参照してください。

### <a name="create-network-resources-in-azure-stack"></a>Azure Stack でネットワーク リソースを作成する

Azure Stack 内で各テナントに必要なネットワーク リソースを作成するには、次の手順を使用します。

#### <a name="create-the-virtual-network-and-vm-subnet"></a>仮想ネットワークと VM サブネットを作成する
1. ユーザー (テナント) アカウントを使用してユーザー ポータルにサインインします。

2. ポータルで、**[新規]** をクリックします。

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. Marketplace メニューから **[ネットワーキング]** を選択します。

4. メニューの **[仮想ネットワーク]** をクリックします。

5. 次の表を使用して、適切なフィールドに値を入力します。

   |フィールド  |値  |
   |---------|---------|
   |名前     |Tenant1VNet1         |
   |アドレス空間     |10.1.0.0/16|
   |サブネット名     |Tenant1 Sub1|
   |サブネットのアドレス範囲     |10.1.1.0/24|

6. 先ほど作成したサブスクリプションが、**[サブスクリプション]** フィールドに設定されたことがわかります。

    a. [リソース グループ] については、リソース グループを作成することも、既存のリソース グループがある場合に **[既存のものを使用]** を選択することもできます。

    b. 既定の場所を確認します。

    c. **[ダッシュボードにピン留め]** をクリックします。

    d. **Create** をクリックしてください。



#### <a name="create-the-gateway-subnet"></a>ゲートウェイ サブネットを作成する
1. ダッシュボードから、作成した仮想ネットワーク リソース (Tenant1VNet1) を開きます。
2. [設定] セクションで **[サブネット]** を選択します。
3. **[ゲートウェイ サブネット]** をクリックして、ゲートウェイ サブネットを仮想ネットワークに追加します。
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. サブネットの名前は、既定で **GatewaySubnet** に設定されます。
   ゲートウェイ サブネットは特別であり、正しく動作するにはこの特定の名前である必要があります。
5. **[アドレス範囲]** フィールドでアドレスが **10.1.0.0/24** であることを確認します。
6. **[OK]** をクリックして、ゲートウェイ サブネットを作成します。

#### <a name="create-the-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成する
1. Azure Stack ユーザー ポータルで、**[新規]** をクリックします。
   
2. Marketplace メニューから **[ネットワーキング]** を選択します。
3. ネットワーク リソースの一覧から **[仮想ネットワーク ゲートウェイ]** を選択します。
4. **[名前]** フィールドに「**GW1**」と入力します。
5. **[仮想ネットワーク]** 項目をクリックして、仮想ネットワークを選択します。
   一覧から **[Tenant1VNet1]** を選択します。
6. **[パブリック IP アドレス]** メニュー項目をクリックします。 **[パブリック IP アドレスの選択]** セクションが開いたら、**[新規作成]** をクリックします。
7. **[名前]** フィールドに「**GW1-PiP**」と入力し、**[OK]** をクリックします。
8. **[VPN の種類]** では、既定で **[ルートベース]** が選択されています。
    この設定はそのままにしておきます。
9. **[サブスクリプション]** と **[場所]** が正しいことを確認します。 必要に応じて、リソースをダッシュボードにピン留めすることができます。 **Create** をクリックしてください。

#### <a name="create-the-local-network-gateway"></a>ローカル ネットワーク ゲートウェイを作成する

ローカル ネットワーク ゲートウェイ リソースの目的は、VPN 接続のもう一方の端にあるリモート ゲートウェイを示すことです。 この例の場合、リモート側は ExpressRoute ルーターの LAN サブインターフェイスです。 この例のテナント 1 では、リモート アドレスは図 2 に示すように 10.60.3.255 です。

1. Azure Stack 物理マシンにログインします。
2. ユーザー アカウントを使用してユーザー ポータルにサインインし、**[新規]** をクリックします。
3. Marketplace メニューから **[ネットワーキング]** を選択します。
4. リソースの一覧から **[ローカル ネットワーク ゲートウェイ]** を選択します。
5. **[名前]** フィールドに「**ER-Router-GW**」と入力します。
6. **[IP アドレス]** フィールドについては、図 2 を参照してください。 ExpressRoute ルーターにおけるテナント 1 用の LAN サブインターフェイスの IP アドレスは、10.60.3.255 です。 ご使用の環境に合わせて、ルーターにおいて対応するインターフェイスの IP アドレスを入力します。
7. **[アドレス空間]** フィールドに、Azure で接続する VNet のアドレス空間を入力します。 この例については、図 2 を参照してください。 テナント 1 の場合、必要なサブネットが **192.168.2.0/24** (これは、Azure 内のハブ VNet です) および **10.100.0.0/16** (これは Azure 内のスポーク VNet です) であることに注意してください。 ご使用の環境に対応するサブネットを入力します。
   > [!IMPORTANT]
   > この例では、Azure Stack ゲートウェイと ExpressRoute ルーターの間のサイト間 VPN 接続に静的ルートを使用していると想定しています。

8. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** がすべて正しいことを確認し、**[作成]** をクリックします。

#### <a name="create-the-connection"></a>接続の作成
1. Azure Stack ユーザー ポータルで、**[新規]** をクリックします。
2. Marketplace メニューから **[ネットワーキング]** を選択します。
3. リソースの一覧から **[接続]** を選択します。
4. **[基本]** の設定セクションで、**[接続の種類]** として **[サイト間 (IPSec)]** を選択します。
5. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** を選択し、**[OK]** をクリックします。
6. **[設定]** セクションで、**[仮想ネットワーク ゲートウェイ]** をクリックし、**[GW1]** をクリックします。
7. **[ローカル ネットワーク ゲートウェイ]** をクリックし、**[ER Router GW]** をクリックします。
8. **[接続名]** フィールドに、「**ConnectToAzure**」と入力します。
9. **[共有キー (PSK)]** フィールドに「**abc123**」と入力し、**[OK]** をクリックします。
10. **[概要]** セクションで、**[OK]** をクリックします。

    接続が作成された後は、仮想ネットワーク ゲートウェイで使用されるパブリック IP アドレスを確認できます。 Azure Stack ポータルでこのアドレスを見つけるには、仮想ネットワーク ゲートウェイを参照します。 **[概要]** で、**[パブリック IP アドレス]** を見つけます。 このアドレスは (デプロイで該当する場合に) 次のセクションで*内部 IP アドレス*として使用するため、メモしておきます。

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>仮想マシンの作成
VPN 接続経由のデータ移動を検証するには、Azure Stack VNet でデータを送受信する仮想マシンが必要です。 ここでは仮想マシンを作成し、仮想ネットワーク内の VM サブネットにそれを配置します。

1. Azure Stack ユーザー ポータルで、**[新規]** をクリックします。
2. Marketplace メニューから、**[Virtual Machines]** を選択します。
3. 仮想マシンのイメージの一覧で、**Windows Server 2016 Datacenter Eval** イメージを選択し、**[作成]** をクリックします。
4. **[基本]** セクションの **[名前]** フィールドに「**VM01**」と入力します。
5. 有効なユーザー名とパスワードを入力します。 このアカウントは、作成後の VM へのログインに使用します。
6. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** を指定し、**[OK]** をクリックします。
7. **[サイズ]** セクションで、この場合は仮想マシンのサイズを選択し、**[選択]** をクリックします。
8. **[設定]** セクションは既定値のままでかまいません。 選択されている仮想ネットワークが **Tenant1VNet1** であり、サブネットが **10.1.1.0/24** に設定されていることを確認してください。 **[OK]**をクリックします。
9. **[概要]** セクションで設定を確認し、**[OK]** をクリックします。

接続する各テナント VNet について、「**仮想ネットワークと VM サブネットを作成する**」から「**仮想マシンの作成**」までのセクションの手順を繰り返します。

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>ゲートウェイ トラバーサル用に NAT 仮想マシンを構成する
> [!IMPORTANT]
> このセクションは、Azure Stack Development Kit デプロイにのみ適用されます。 マルチノード デプロイでは NAT は必要ありません。

Azure Stack Development Kit は自己完結型であり、物理ホストがデプロイされているネットワークから分離されています。 そのため、ゲートウェイが接続されている「外部」VIP ネットワークは外部になく、ネットワーク アドレス変換 (NAT) を行うルーターの裏側に隠されています。
 
このルーターは Windows Server 仮想マシン (**AzS-bgpnat01**) であり、Azure Stack Development Kit インフラストラクチャでルーティングとリモート アクセス サービス (RRAS) ロールを実行します。 AzS-BGPNAT01 仮想マシンでは、両端を結ぶサイト間 VPN 接続を許可するよう NAT を構成する必要があります。

#### <a name="configure-the-nat"></a>NAT を構成する

1. 管理者アカウントで Azure Stack 物理マシンにログインします。
2. 次の PowerShell スクリプトをコピーして編集し、管理者特権の Windows PowerShell ISE で実行します。 管理者パスワードを使用しているものに置換してください。 返されるアドレスは*外部 BGPNAT アドレス*です。

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. NAT を構成するには、次の PowerShell スクリプトをコピーして編集し、管理者特権の Windows PowerShell ISE で実行します。 スクリプトを編集して、*外部 BGPNAT アドレス*と*内部 IP アドレス* (以前に「**接続の作成**」セクションでメモしたもの) を置換します。

   図の例では、*外部 BGPNAT アドレス*は 10.10.0.62 であり、*内部 IP アドレス*は 192.168.102.1 です。

   ```
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Azure を構成する
Azure Stack の構成が完了したので、いくつかの Azure リソースをデプロイできます。 次の図は、Azure 内のサンプル テナント仮想ネットワークを示しています。 Azure 内の VNet には、任意の名前とアドレス指定スキームを使用できます。 ただし、Azure と Azure Stack 内の VNet のアドレス範囲は一意であり、重複していない必要があります。

![Azure VNet](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**図 3**

Azure でデプロイするリソースは、Azure Stack でデプロイしたリソースに似ています。 同じように、次のものをデプロイします。
* 仮想ネットワークとサブネット
* ゲートウェイ サブネット
* 仮想ネットワーク ゲートウェイ
* 接続
* ExpressRoute 回線

例の Azure ネットワーク インフラストラクチャは、次のように構成されています。
* 標準的なハブ (192.168.2.0/24) とスポーク (10.100.0.0./16) の VNet モデルが使用されています。
* ワークロードはスポーク VNet でデプロイされ、ExpressRoute 回線はハブ VNet に接続されています。
* 2 つの VNet は VNet ピアリング機能を使用してリンクされています。

### <a name="configure-vnets"></a>VNet を構成する
1. Azure 資格情報を使用して、Azure Portal にサインインします。
2. 192.168.2.0/24 アドレス領域を使用してハブ VNet を作成します。 192.168.2.0/25 アドレス範囲を使用してサブネットを作成し、192.168.2.128/27 アドレス範囲を使用してゲートウェイ サブネットを追加します。
3. 10.100.0.0/16 アドレス範囲を使用してスポーク VNet とサブネットを作成します。


Azure での仮想ネットワークの作成の詳細については、「[複数のサブネットを含んだ仮想ネットワークを作成する](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)」を参照してください。

### <a name="configure-an-expressroute-circuit"></a>ExpressRoute 回線を構成する

1. 「[ExpressRoute の前提条件とチェックリスト](../expressroute/expressroute-prerequisites.md)」にある ExpressRoute の前提条件を確認します。
2. 「[ExpressRoute 回線の作成と変更](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)」の手順に従い、Azure サブスクリプションを使用して ExpressRoute 回線を作成します。
3. 前の手順で取得したサービス キーをホスト側/プロバイダーと共有し、当該の側で ExpressRoute 回線をプロビジョニングできるようにします。
4. 「[ExpressRoute 回線のピアリングの作成と変更を行う](../expressroute/expressroute-howto-routing-portal-resource-manager.md)」の手順に従い、ExpressRoute 回路でプライベート ピアリングを構成します。

### <a name="create-the-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成する

* 「[PowerShell を使用して ExpressRoute の仮想ネットワーク ゲートウェイを構成する](../expressroute/expressroute-howto-add-gateway-resource-manager.md)」の手順に従い、ハブ VNet で ExpressRoute 用の仮想ネットワーク ゲートウェイを作成します。

### <a name="create-the-connection"></a>接続の作成

* ExpressRoute 回線をハブ VNet にリンクするには、「[ExpressRoute 回線に仮想ネットワークを 接続する](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)」の手順に従います。

### <a name="peer-the-vnets"></a>VNet をピアリングする

* 「[Azure ポータルを使用した仮想ネットワーク ピアリングの作成](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md)」の手順を使用して、ハブおよびスポーク VNet をピアリングします。 VNet ピアリングを構成する際は、必ず次のオプションを選択します。
   * ハブからスポーク: **ゲートウェイ転送を許可する**
   * スポークからハブ: **リモート ゲートウェイを使用する**

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

* ワークロード仮想マシンをスポーク VNet にデプロイします。

Azure で接続する追加のテナント VNet ごとに、それぞれの ExpressRoute 回線を使用してこれらの手順を繰り返します。

## <a name="configure-the-router"></a>ルーターを構成する

次のエンド ツー エンド インフラストラクチャ ダイアグラムは、ExpressRoute ルーターの構成を示しています。 2 つのテナント (Tenant 1 と Tenant 2) とそれぞれの ExpressRoute 回線が示されています。 2 つのテナント間でのエンド ツー エンドの分離を保証するため、各テナントは ExpressRoute ルーターの LAN および WAN 側にある独自の VRF (Virtual Routing and Forwarding) にリンクされています。 サンプル構成に従う際は、ルーター インターフェイスで使用されている IP アドレスに注意してください。

![エンド ツー エンド ダイアグラム](media/azure-stack-connect-expressroute/EndToEnd.png)

**図 4**

Azure Stack からサイト間 VPN 接続を終了するには、IKEv2 VPN と BGP をサポートする任意のルーターを使用できます。 ExpressRoute 回線を使用して Azure に接続する際は、同じルーターが使用されます。 

図 4 で示されているネットワーク インフラストラクチャをサポートする Cisco ASR 1000 の構成例を次に示します。

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>接続をテストする

サイト間接続と ExpressRoute 回線を確立した後、接続をテストします。 このタスクは単純です。  Azure VNet で作成した仮想マシンのいずれかにログオンし、Azure Stack 環境で作成した仮想マシンに ping を実行するか、またはその逆を実行します。 

送信するトラフィックがサイト間接続と ExpressRoute 接続を確実に通過するようにするため、両方の端で仮想マシンの VIP アドレスでなく専用 IP (DIP) アドレスに ping を実行する必要があります。 そのため、接続の反対側のアドレスを探してメモする必要があります。

### <a name="allow-icmp-in-through-the-firewall"></a>ファイアウォールで ICMP を許可する
既定では、Windows Server 2016 では ICMP パケットがファイアウォールを通過することは許可されません。 そのため、テストで使用するすべての仮想マシンについて、管理者特権の PowerShell ウィンドウで次のコマンドレットを実行してください。


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Azure Stack 仮想マシンへの ping

1. テナント アカウントを使用して、Azure Stack ユーザー ポータルにサインインします。
2. 左側のナビゲーション バーで、**[Virtual Machines]** をクリックします。
3. 以前に作成した仮想マシンを見つけ、クリックします。
4. 仮想マシンのセクションで、**[接続]** をクリックします。
5. 管理者特権の PowerShell ウィンドウを開き、「**ipconfig /all**」と入力します。
6. 出力で IPv4 アドレスを見つけてメモします。 Azure VNet 内の仮想マシンから、このアドレスに ping を実行します。 例の環境では、このアドレスは 10.1.1.x/24 サブネットから来ています。 ご使用の環境ではアドレスが異なる可能性があります。 ただし、テナント VNet サブネット用に作成したサブネット内にある必要があります。


### <a name="view-data-transfer-statistics"></a>データ転送統計情報を表示する

接続を通過するトラフィック量を知りたい場合は、Azure Stack ユーザー ポータルの [接続] セクションでその情報を見ることができます。 この情報は、先ほど送信した ping が実際に VPN および ExpressRoute 接続を通過したことを検証するためのもう 1 つの優れた手段でもあります。

1. テナント アカウントを使用して、Microsoft Azure Stack ユーザー ポータルにサインインします。
2. VPN ゲートウェイが作成されたリソース グループに移動し、オブジェクトの種類として **[接続]** を選択します。
3. 一覧内の **[ConnectToAzure]** 接続をクリックします。
4. **[接続]** セクションで、**[受信データ]** と **[送信データ]** の統計情報を確認できます。ここには 0 以外の値が表示されます。

   ![受信データと送信データ](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>次のステップ
[Azure と Azure Stack へのアプリのデプロイ](azure-stack-solution-pipeline.md)