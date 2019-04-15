---
title: ExpressRoute を使用して Azure Stack を Azure に接続する
description: ExpressRoute を使用して Azure Stack 内の仮想ネットワークを Azure 内の仮想ネットワークに接続する方法について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: bd5e5a3b6fa72698f04969219b1db3cdb0bde3a5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486701"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Azure ExpressRoute を使用して Azure Stack を Azure に接続する

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

この記事では、[Microsoft Azure ExpressRoute](/azure/expressroute/) の直接接続を使用して、Azure Stack の仮想ネットワークを Azure の仮想ネットワークに接続する方法について説明します。

この記事に忠実に従い、記載されている例を使用して同じテスト環境をセットアップできます。 または、この記事を参考にしながら、独自の ExpressRoute 環境をセットアップすることもできます。

## <a name="overview-assumptions-and-prerequisites"></a>概要と前提条件

Azure ExpressRoute を利用すると、接続プロバイダーが提供するプライベート接続を介して、オンプレミスのネットワークを Microsoft クラウドに拡張できます。 ExpressRoute は、公共のインターネットを経由する VPN 接続ではありません。

Azure ExpressRoute の詳細については、[ExpressRoute の概要](../expressroute/expressroute-introduction.md)に関するページを参照してください。

### <a name="assumptions"></a>前提条件

この記事では、以下のことを前提としています。

* Azure に関する実践的な知識がある。
* Azure Stack に関する基礎知識がある。
* ネットワークに関する基礎知識がある。

### <a name="prerequisites"></a>前提条件

ExpressRoute を使用して Azure Stack と Azure を接続するには、次の要件を満たしている必要があります。

* [接続プロバイダー](../expressroute/expressroute-locations.md)経由でプロビジョニングされている [ExpressRoute 回線](../expressroute/expressroute-circuit-peerings.md)。
* Azure で ExpressRoute 回線と VNet を作成するための Azure サブスクリプション。
* 次の機能を備えたルーター。
  * LAN インターフェイスと Azure Stack マルチテナント ゲートウェイの間でサイト間 VPN 接続をサポートする。
  * Azure Stack のデプロイに複数のテナントが存在する場合に、複数の VRF (Virtual Routing and Forwarding) を作成することができる。
* 次のポートを備えたルーター。
  * ExpressRoute 回線に接続された WAN ポート。
  * Azure Stack マルチテナント ゲートウェイに接続された LAN ポート。

### <a name="expressroute-network-architecture"></a>ExpressRoute ネットワークのアーキテクチャ

次の図は、この記事の例に従って ExpressRoute の設定を完了した後の Azure Stack と Azure の環境を示しています。

*図 1: ExpressRoute ネットワーク*

![ExpressRoute ネットワーク](media/azure-stack-connect-expressroute/Conceptual.png)

次の図は、複数のテナントが ExpressRoute ルーターを介して Azure Stack インフラストラクチャから Microsoft エッジで Azure に接続するしくみを示しています。

*図 2: マルチテナント接続*

![ExpressRoute でのマルチテナント接続](media/azure-stack-connect-expressroute/Architecture.png)

この記事の例では、図 2 に示したものと同じマルチテナント アーキテクチャを使い、ExpressRoute のプライベート ピアリングを使って Azure Stack を Azure に接続します。 この接続は、Azure Stack 内の仮想ネットワーク ゲートウェイから ExpressRoute ルーターへのサイト間 VPN 接続を使用して行われます。

この記事内の各手順では、Azure Stack 内の 2 つの異なるテナントにある 2 つの VNet から Azure 内の対応する VNet へのエンド ツー エンド接続を作成する方法を示します。 設定するテナントは必ずしも 2 つである必要はありません。これらの手順をシングル テナントに使うこともできます。

## <a name="configure-azure-stack"></a>Azure Stack の構成

1 つ目のテナントの Azure Stack 環境は、次の図の手順を参考にしてセットアップしてください。 複数のテナントを設定する場合は、これらの手順を繰り返すことになります。

>[!NOTE]
>これらの手順では、Azure Stack ポータルを使用してリソースを作成する方法を示していますが、PowerShell を使用することもできます。

![Azure Stack のネットワークのセットアップ](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>開始する前に

Azure Stack の構成を始める前に、次のものが必要となります。

* Azure Stack 統合システムのデプロイまたは Azure Stack Development Kit (ASDK) のデプロイ。 ASDK のデプロイについては、[Azure Stack Development Kit のデプロイに関するクイック スタート](azure-stack-deploy-overview.md)を参照してください。
* ユーザーがサブスクライブできる Azure Stack 内のオファー。 詳細については、[プラン、オファー、サブスクリプション](azure-stack-plan-offer-quota-overview.md)に関するページを参照してください。

### <a name="create-network-resources-in-azure-stack"></a>Azure Stack でネットワーク リソースを作成する

Azure Stack 内でテナントに必要なネットワーク リソースを作成するには、次の手順を使用します。

#### <a name="create-the-virtual-network-and-vm-subnet"></a>仮想ネットワークと VM サブネットを作成する

1. ユーザー (テナント) アカウントを使用してユーザー ポータルにサインインします。

2. ポータルで **[+ リソースの作成]** を選択します。

3. **[Azure Marketplace]** の **[ネットワーク]** を選択します。

4. **[おすすめ]** の **[仮想ネットワーク]** を選択します。

5. **[仮想ネットワークの作成]** で、次の表に示した値をそれぞれ対応するフィールドに入力します。

   |フィールド  |値  |
   |---------|---------|
   |名前     |Tenant1VNet1         |
   |アドレス空間     |10.1.0.0/16|
   |サブネット名     |Tenant1 Sub1|
   |サブネットのアドレス範囲     |10.1.1.0/24|

6. 先ほど作成したサブスクリプションが、**[サブスクリプション]** フィールドに設定されたことがわかります。 その他のフィールドは、次のように入力してください。

    * **[リソース グループ]** で **[新規作成]** を選択して新しいリソース グループを作成するか、既存のリソース グループがある場合は **[既存のものを使用]** を選択します。
    * 既定の**場所**を確認します。
    * **Create** をクリックしてください。
    * (省略可能) **[ダッシュボードにピン留めする]** をクリックします。

#### <a name="create-the-gateway-subnet"></a>ゲートウェイ サブネットを作成する

1. **[仮想ネットワーク]** の **[Tenant1VNet1]** を選択します。
1. **[設定]** で、**[サブネット]** を選択します。
1. **[+ ゲートウェイ サブネット]** を選択し、ゲートウェイ サブネットを仮想ネットワークに追加します。
1. サブネットの名前は、既定で **GatewaySubnet** に設定されます。 ゲートウェイ サブネットは特殊なケースであり、正しく動作するにはこの名前を使用する必要があります。
1. **[アドレス範囲]** が **10.1.0.0/24** であることを確認します。
1. **[OK]** をクリックして、ゲートウェイ サブネットを作成します。

#### <a name="create-the-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成する

1. Azure Stack ユーザー ポータルで、**[+ リソースの作成]** をクリックします。
1. **[Azure Marketplace]** の **[ネットワーク]** を選択します。
1. ネットワーク リソースの一覧から **[仮想ネットワーク ゲートウェイ]** を選択します。
1. **[名前]** フィールドに「**GW1**」と入力します。
1. **[仮想ネットワーク]** を選択します。
1. ボックスの一覧から **[Tenant1VNet1]** を選択します。
1. **[パブリック IP アドレス]**、**[パブリック IP アドレスの選択]** の順に選択し、**[新規作成]** をクリックします。
1. **[名前]** フィールドに「**GW1-PiP**」と入力し、次に **[OK]** をクリックします。
1. **[VPN の種類]** では、既定で **[ルートベース]** が選択されています。 この設定はそのままにしておきます。
1. **[サブスクリプション]** と **[場所]** が正しいことを確認します。 **Create** をクリックしてください。

#### <a name="create-the-local-network-gateway"></a>ローカル ネットワーク ゲートウェイを作成する

ローカル ネットワーク ゲートウェイ リソースは、VPN 接続のもう一方の端にあるリモート ゲートウェイを識別します。 この例の場合、接続のリモート側の終端は ExpressRoute ルーターの LAN サブインターフェイスです。 テナント 1 では、図 2 に示したように、10.60.3.255 がリモート アドレスになります。

1. ユーザー アカウントを使用して Azure Stack ユーザー ポータルにサインインし、**[+ リソースの作成]** を選択します。
1. **[Azure Marketplace]** の **[ネットワーク]** を選択します。
1. リソースの一覧から **[ローカル ネットワーク ゲートウェイ]** を選択します。
1. **[名前]** フィールドに「**ER-Router-GW**」と入力します。
1. **[IP アドレス]** フィールドについては、図 2 を参照してください。 ExpressRoute ルーターにおけるテナント 1 用の LAN サブインターフェイスの IP アドレスは、10.60.3.255 です。 ご使用の環境に合わせて、ルーターにおいて対応するインターフェイスの IP アドレスを入力します。
1. **[アドレス空間]** フィールドに、Azure で接続する VNet のアドレス空間を入力します。 "*図 2*" のテナント 1 のサブネットは次のとおりです。

   * 192.168.2.0/24 (Azure のハブ VNet)。
   * 10.100.0.0/16 (Azure のスポーク VNet)。

   > [!IMPORTANT]
   > この例では、Azure Stack ゲートウェイと ExpressRoute ルーターの間のサイト間 VPN 接続に静的ルートを使用していると想定しています。

1. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** がすべて正しいことを確認します。 **[作成]** を選択します。

#### <a name="create-the-connection"></a>接続の作成

1. Azure Stack ユーザー ポータルで、**[+ リソースの作成]** を選択します。
1. **[Azure Marketplace]** の **[ネットワーク]** を選択します。
1. リソースの一覧から **[接続]** を選択します。
1. **[基本]** で、**[接続の種類]** として **[サイト間 (IPSec)]** を選択します。
1. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** を選択します。 Click **OK**.
1. **[設定]** で **[仮想ネットワーク ゲートウェイ]** を選択し、**[GW1]** を選択します。
1. **[ローカル ネットワーク ゲートウェイ]** を選択し、**[ER Router GW]** を選択します。
1. **[接続名]** フィールドに、「**ConnectToAzure**」と入力します。
1. **[共有キー (PSK)]** フィールドに「**abc123**」と入力し、**[OK]** を選択します。
1. **[概要]** で **[OK]** を選択します。

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>仮想ネットワーク ゲートウェイのパブリック IP アドレスを取得する

仮想ネットワーク ゲートウェイの作成後、ゲートウェイのパブリック IP アドレスを取得できます。 このアドレスは、後でデプロイに必要になった場合のためにメモしておいてください。 デプロイによっては、このアドレスが**内部 IP アドレス**として使用されます。

1. Azure Stack ユーザー ポータルで **[すべてのリソース]** を選択します。
1. **[すべてのリソース]** で、仮想ネットワーク ゲートウェイを選択します。この例では **GW1** が該当します。
1. **[仮想ネットワーク ゲートウェイ]** で、リソースの一覧から **[概要]** を選択します。 または、**[プロパティ]** を選択することもできます。
1. メモする必要のある IP アドレスは、**[パブリック IP アドレス]** に一覧表示されています。 この例の構成では、192.68.102.1 が該当します。

#### <a name="create-a-virtual-machine"></a>仮想マシンの作成

VPN 接続上のデータ トラフィックをテストするには、Azure Stack VNet 内でデータを送受信する仮想マシンが必要です。 仮想マシンを作成して、目的の仮想ネットワークの VM サブネットにデプロイします。

1. Azure Stack ユーザー ポータルで、**[+ リソースの作成]** を選択します。
1. **[Azure Marketplace]** の **[計算]** を選択します。
1. 仮想マシンのイメージの一覧で、**Windows Server 2016 Datacenter Eval** イメージを選択します。

   >[!NOTE]
   >この記事で使用されているイメージを入手できない場合は、Azure Stack のオペレーターに依頼して、別の Windows Server イメージを入手してください。

1. **[仮想マシンの作成]** で、**[基本]** を選択し、次に **[名前]** に「**VM01**」と入力します。
1. 有効なユーザー名とパスワードを入力します。 このアカウントは、作成後の VM へのサインインに使用します。
1. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** を指定します。 **[OK]** を選択します。
1. **[サイズの選択]** で、このインスタンスの仮想マシンのサイズを選び、**[選択]** を選択します。
1. **[設定]** で、次のことを確認します。

   * 仮想ネットワークが **Tenant1VNet1** であること。
   * サブネットが **10.1.1.0/24** に設定されていること。

   既定の設定を使用して **[OK]** をクリックします。

1. **[概要]** で VM の構成を確認し、**[OK]** をクリックします。

さらにテナントを追加するには、次の各セクションで行った手順を繰り返します。

* [仮想ネットワークと VM サブネットを作成する](#create-the-virtual-network-and-vm-subnet)
* [ゲートウェイ サブネットを作成する](#create-the-gateway-subnet)
* [仮想ネットワーク ゲートウェイを作成する](#create-the-virtual-network-gateway)
* [ローカル ネットワーク ゲートウェイを作成する](#create-the-local-network-gateway)
* [接続を作成する](#create-the-connection)
* [仮想マシンの作成](#create-a-virtual-machine)

テナント 2 を例として使用する場合は、重複を避けるために必ず IP アドレスを変更してください。

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>ゲートウェイ トラバーサル用に NAT 仮想マシンを構成する

> [!IMPORTANT]
> このセクションは、Azure Stack Development Kit (ASDK) デプロイにのみ適用されます。 マルチノード デプロイでは NAT は必要ありません。

Azure Stack Development Kit は自己完結型であり、物理ホストがデプロイされているネットワークから分離されています。 ゲートウェイが接続されている VIP ネットワークは外部には公開されず、ネットワーク アドレス変換 (NAT) を行うルーターの内側に隠されています。

このルーターは Windows Server 仮想マシン (AzS-BGPNAT01) であり、ルーティングとリモート アクセス サービス (RRAS) ロールを実行します。 AzS-BGPNAT01 仮想マシンでは、両端を結ぶサイト間 VPN 接続を許可するよう NAT を構成する必要があります。

#### <a name="configure-the-nat"></a>NAT を構成する

1. Azure Stack のホスト コンピューターに管理者アカウントでサインインします。
1. 次の PowerShell スクリプトをコピーし、編集します。 `your administrator password` を実際の管理者パスワードに置き換えたうえで、管理者特権の PowerShell ISE からスクリプトを実行してください。 このスクリプトから**外部 BGPNAT アドレス**が返されます。

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "your administrator password" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```

1. NAT を構成するには、次の PowerShell スクリプトをコピーし、編集します。 このスクリプトを編集して、`External BGPNAT address` と `Internal IP address` を次の例の値に置き換えます。

   * "*外部 BGPNAT アドレス*" には 10.10.0.62 を使用します。
   * "*内部 IP アドレス*" には 192.168.102.1 を使用します。

   管理者特権の PowerShell ISE から次のスクリプトを実行します。

   ```powershell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Azure を構成する

Azure Stack の構成が完了したら、Azure のリソースをデプロイすることができます。 次の図は、Azure 内のテナント仮想ネットワークの例を示しています。 Azure 内の VNet には、任意の名前とアドレス指定スキームを使用できます。 ただし、Azure と Azure Stack 内の VNet のアドレス範囲は一意であり、重複していない必要があります。

*図 3: Azure VNet*

![Azure VNet](media/azure-stack-connect-expressroute/AzureArchitecture.png)

Azure でデプロイするリソースは、Azure Stack でデプロイしたリソースに似ています。 デプロイするコンポーネントは次のとおりです。

* 仮想ネットワークとサブネット
* ゲートウェイ サブネット
* 仮想ネットワーク ゲートウェイ
* 接続
* ExpressRoute 回線

この例の Azure ネットワーク インフラストラクチャは、次のように構成されています。

* 標準的なハブ (192.168.2.0/24) とスポーク (10.100.0.0./16) の VNet モデル。 ハブスポーク ネットワーク トポロジの詳細については、「[Azure にハブスポーク ネットワーク トポロジを実装する](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)」を参照してください。
* ワークロードはスポーク VNet でデプロイされ、ExpressRoute 回線はハブ VNet に接続されています。
* 2 つの VNet は、VNet ピアリングを使用して接続されています。

### <a name="configure-the-azure-vnets"></a>Azure VNet の構成

1. Azure 資格情報を使用して、Azure Portal にサインインします。
1. 192.168.2.0/24 アドレス範囲を使用してハブ VNet を作成します。
1. 192.168.2.0/25 アドレス範囲を使用してサブネットを作成し、192.168.2.128/27 アドレス範囲を使用してゲートウェイ サブネットを追加します。
1. 10.100.0.0/16 アドレス範囲を使用してスポーク VNet とサブネットを作成します。

Azure での仮想ネットワークの作成の詳細については、「[Create a virtual network (仮想ネットワークの作成)](../virtual-network/manage-virtual-network.md#create-a-virtual-network)」を参照してください。

### <a name="configure-an-expressroute-circuit"></a>ExpressRoute 回線を構成する

1. 「[ExpressRoute の前提条件とチェックリスト](../expressroute/expressroute-prerequisites.md)」にある ExpressRoute の前提条件を確認します。

1. 「[ExpressRoute 回線の作成と変更](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)」の手順に従い、Azure サブスクリプションを使用して ExpressRoute 回線を作成します。

   >[!NOTE]
   >サービスには、接続の終端で ExpressRoute 回線を設定できるよう、ご利用の回線のサービス キーを指定してください。

1. 「[ExpressRoute 回線のピアリングの作成と変更を行う](../expressroute/expressroute-howto-routing-portal-resource-manager.md)」の手順に従い、ExpressRoute 回路でプライベート ピアリングを構成します。

### <a name="create-the-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成する

「[PowerShell を使用して ExpressRoute の仮想ネットワーク ゲートウェイを構成する](../expressroute/expressroute-howto-add-gateway-resource-manager.md)」の手順に従い、ハブ VNet で ExpressRoute 用の仮想ネットワーク ゲートウェイを作成します。

### <a name="create-the-connection"></a>接続の作成

ExpressRoute 回線をハブ VNet にリンクするには、「[ExpressRoute 回線に仮想ネットワークを 接続する](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)」の手順に従います。

### <a name="peer-the-vnets"></a>VNet をピアリングする

「[Azure portal を使用した仮想ネットワーク ピアリングの作成](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md)」の手順を使用して、ハブおよびスポーク VNet をピアリングします。 VNet ピアリングを構成する際は、必ず次のオプションを選択します。

* ハブからスポーク: **ゲートウェイ転送を許可する**。
* スポークからハブ: **リモート ゲートウェイを使用する**。

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

ワークロード仮想マシンをスポーク VNet にデプロイします。

Azure で接続する追加のテナント VNet ごとに、それぞれの ExpressRoute 回線を使用してこれらの手順を繰り返します。

## <a name="configure-the-router"></a>ルーターを構成する

ExpressRoute ルーターは、次の ExpressRoute ルーターの構成図を参考に構成してください。 2 つのテナント (Tenant 1 と Tenant 2) とそれぞれの ExpressRoute 回線が示されています。 各テナントは ExpressRoute ルーターの LAN および WAN 側にある独自の VRF (Virtual Routing and Forwarding) にリンクされています。 この構成によって、2 つのテナントが確実にエンド ツー エンドで分離されます。 この構成例に従う際は、ルーターのインターフェイスで使用されている IP アドレスに注意してください。

*図 4: ExpressRoute ルーターの構成*

![ExpressRoute ルーターの構成](media/azure-stack-connect-expressroute/EndToEnd.png)

Azure Stack からサイト間 VPN 接続を終了するには、IKEv2 VPN と BGP をサポートする任意のルーターを使用できます。 ExpressRoute 回線を使用して Azure に接続する際は、同じルーターが使用されます。

次の Cisco ASR 1000 Series Aggregation Services Router の構成例は、「*ExpressRoute ルーターの構成*」の図に示したネットワーク インフラストラクチャをサポートします。

```shell
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
 description PRIMARY ExpressRoute Link to AZURE over Equinix
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
 description BACKUP ExpressRoute Link to AZURE over Equinix
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

サイト間接続と ExpressRoute 回線を確立した後、接続をテストします。

次の ping テストを実行します。

* Azure VNet 内のいずれかの仮想マシンにサインインし、Azure Stack 内に作成した仮想マシンに ping を実行します。
* Azure Stack 内に作成したいずれかの仮想マシンにサインインし、Azure VNet 内に作成した仮想マシンに ping を実行します。

>[!NOTE]
>送信するトラフィックがサイト間接続と ExpressRoute 接続を確実に通過するようにするため、両方の端で仮想マシンの VIP アドレスでなく専用 IP (DIP) アドレスに ping を実行する必要があります。

### <a name="allow-icmp-in-through-the-firewall"></a>ファイアウォールで ICMP を許可する

Windows Server 2016 の既定では、受信 ICMP パケットがファイアウォールを通過できないように設定されています。 ping テストに使用するすべての仮想マシンについて、受信 ICMP パケットを許可する必要があります。 ICMP のファイアウォール規則を作成するには、管理者特権の PowerShell ウィンドウで次のコマンドレットを実行します。

```powershell
# Create ICMP firewall rule.
New-NetFirewallRule `
  –DisplayName “Allow ICMPv4-In” `
  –Protocol ICMPv4
```

### <a name="ping-the-azure-stack-virtual-machine"></a>Azure Stack 仮想マシンへの ping

1. テナント アカウントを使用して、Azure Stack ユーザー ポータルにサインインします。

1. 作成した仮想マシンを見つけて選択します。

1. **[接続]** を選択します。

1. Windows または PowerShell の管理者特権のコマンド プロンプトで「**ipconfig /all**」と入力します。 出力として返された IPv4 アドレスをメモします。

1. Azure VNet 内の仮想マシンから、この IPv4 アドレスに ping を実行します。

   例の環境では、この IPv4 アドレスは 10.1.1.x/24 サブネットから来ています。 このアドレスは、ご使用の環境で異なる場合がありますが、テナント VNet サブネット用に作成したサブネット内にある必要があります。

### <a name="view-data-transfer-statistics"></a>データ転送統計情報を表示する

接続を通過するトラフィック量を知りたい場合は、Azure Stack ユーザー ポータルでその情報を見ることができます。 これは、ping テスト データが VPN 接続と ExpressRoute 接続を通過したかどうかを調べる手段としてもお勧めします。

1. テナント アカウントを使用して、Azure Stack ユーザー ポータルにサインインし、**[すべてのリソース]** を選択します。
1. VPN ゲートウェイのリソース グループに移動し、オブジェクトの種類として **[接続]** を選択します。
1. 一覧から **[ConnectToAzure]** 接続を選択します。
1. **[接続]** > **[概要]** で、**[受信データ]** と **[送信データ]** の統計情報を確認できます。0 以外の何らかの値が表示されていると思います。

   ![受信データと送信データ](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>次の手順

[Azure と Azure Stack へのアプリのデプロイ](azure-stack-solution-pipeline.md)
