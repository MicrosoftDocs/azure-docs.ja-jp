---
title: 'VNet 対 VNet 接続を使用して VNet を VNet に接続する: Azure CLI'
description: VNet 間接続と Azure CLI を使用して仮想ネットワークどうしを接続します。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9502f3fbd50aad756e15daa4db1badda2abf9ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660068"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-azure-cli"></a>Azure CLI を使用して VNet 間の VPN ゲートウェイ接続を構成する

この記事は、VNet 間という接続の種類を使用して仮想ネットワークを接続する際に役立ちます。 仮想ネットワークが属しているリージョンやサブスクリプションは異なっていてもかまいません。 異なるサブスクリプションの VNet を接続する場合、サブスクリプションが同じ Active Directory テナントに関連付けられている必要はありません。

この記事の手順は、Resource Manager デプロイ モデルに適用されます。また、この手順では Azure CLI を使用します。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [異なるデプロイメント モデルの接続 - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [異なるデプロイメント モデルの接続 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a><a name="about"></a>VNet の接続について

VNet の接続方法は複数あります。 以降のセクションでは、仮想ネットワークを接続するさまざまな方法について説明します。

### <a name="vnet-to-vnet"></a>VNet 間

VNet 間接続の構成は、VNet を簡単に接続するための良い方法です。 VNet 間接続の種類を使用して仮想ネットワークどうしを接続することは、オンプレミスの場所へのサイト間 IPsec 接続を作成することに似ています。 どちらの接続の種類も、VPN ゲートウェイを使用して IPsec/IKE を使った安全なトンネルが確保され、通信時には同じように機能します。 この接続の種類の違いは、ローカル ネットワーク ゲートウェイの構成方法にあります。 VNet 間接続を作成するときは、ローカル ネットワーク ゲートウェイのアドレス空間は見えません。 自動的に作成されて値が設定されます。 一方の VNet のアドレス空間を更新した場合、もう一方の VNet が、更新されたアドレス空間へのルーティングを自動的に認識します。 VNet 間接続の作成は、通常、VNet どうしのサイト間接続を作成するよりも高速で簡単です。

### <a name="connecting-vnets-using-site-to-site-ipsec-steps"></a>サイト間 (IPsec) の手順による VNet の接続

複雑なネットワーク構成で作業している場合は、VNet 間の手順の代わりに、[サイト間](vpn-gateway-howto-site-to-site-resource-manager-cli.md)の手順を使用して VNet を接続する方がよい場合もあります。 サイト間の手順を使用する場合は、ローカル ネットワーク ゲートウェイを手動で作成および構成します。 各 VNet のローカル ネットワーク ゲートウェイは、他方の VNet をローカル サイトとして扱います。 そうすることで、トラフィックをルーティングするための追加のアドレス空間をローカル ネットワーク ゲートウェイに指定することができます。 VNet のアドレス空間が変更されたら、変更を反映するように、対応するローカル ネットワーク ゲートウェイを手動で更新する必要があります。 自動的には更新されません。

### <a name="vnet-peering"></a>VNET ピアリング

VNET ピアリングを使用して VNet を接続することを検討する場合もあります。 VNET ピアリングは VPN ゲートウェイを使用せず、さまざまな制約があります。 さらに、[VNET ピアリングの料金](https://azure.microsoft.com/pricing/details/virtual-network)は、[VNet 間 VPN Gateway の料金](https://azure.microsoft.com/pricing/details/vpn-gateway)と計算方法が異なります。 詳細については、「 [VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)」を参照してください。

## <a name="why-create-a-vnet-to-vnet-connection"></a><a name="why"></a>VNet 間接続を作成する理由

VNet 間接続による仮想ネットワークの接続が望ましいのは、次のような場合です。

* **リージョン間の geo 冗長性および geo プレゼンス**

  * インターネット接続エンドポイントを介さず、安全な接続を使って独自の geo レプリケーションや geo 同期をセットアップすることができます。
  * Azure Traffic Manager および Load Balancer を使用し、複数の Azure リージョンをまたぐ geo 冗長性を備えた、可用性に優れたワークロードをセットアップすることができます。 たとえば、複数の Azure リージョンにまたがる SQL AlwaysOn 可用性グループをセットアップすることができます。
* **特定のリージョン内で分離または管理境界を備えた多層アプリケーション**

  * 同じリージョン内で、分離または管理要件に基づいて相互に接続された複数の仮想ネットワークを利用し、多層アプリケーションをセットアップすることができます。

マルチサイト構成と VNet 間通信を組み合わせることができます。 そのため、クロスプレミス接続と仮想ネットワーク間接続とを組み合わせたネットワーク トポロジを確立することができます。

## <a name="which-vnet-to-vnet-steps-should-i-use"></a><a name="steps"></a>どの VNet 間の手順を使用する必要がありますか。

この記事では、VNet 間接続の 2 種類の手順について説明します。 1 つは [VNet が同じサブスクリプション内に存在する](#samesub)場合の手順で、もう 1 つは [VNet が別のサブスクリプション内に存在する](#difsub)場合の手順です。 

この演習では、構成を組み合わせるか、希望する方のみを選んでもかまいません。 どの構成でも、接続の種類として VNet 間を使用します。 ネットワーク トラフィックは、互いに直接接続されている VNet 間を行き来します。 この演習では、TestVNet4 からのトラフィックが TestVNet5 にルーティングされることはありません。

* [同じサブスクリプション内にある VNet:](#samesub)この構成の手順では、TestVNet1 と TestVNet4 を使用します。

  ![同じサブスクリプション内に存在する VNet における、VNet 間の手順を示す図。](./media/vpn-gateway-howto-vnet-vnet-cli/v2vrmps.png)

* [異なるサブスクリプション内にある VNet:](#difsub)この構成の手順では、TestVNet1 と TestVNet5 を使用します。

  ![v2v diagram](./media/vpn-gateway-howto-vnet-vnet-cli/v2vdiffsub.png)


## <a name="connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>同じサブスクリプション内にある VNet の接続

### <a name="before-you-begin"></a>開始する前に

開始する前に、最新バージョンの CLI コマンド (2.0 以降) をインストールします。 CLI コマンドのインストール方法については、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」をご覧ください。

### <a name="plan-your-ip-address-ranges"></a><a name="Plan"></a>IP アドレス範囲の計画

以下の手順に従って、2 つの仮想ネットワークを、それぞれのゲートウェイ サブネットおよび構成と共に作成します。 その後、2 つの VNet 間の VPN 接続を作成します。 ネットワーク構成の IP アドレスの範囲を計画することが重要です。 VNet の範囲やローカル ネットワークの範囲が重複することは、どのような形であれ許容されないので注意してください。 以下の例では、DNS サーバーは含まれていません。 仮想ネットワークの名前解決が必要な場合は、[名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)に関する記事を参照してください。

例では、次の値を使用します。

**TestVNet1 の値:**

* VNet 名: TestVNet1
* リソース グループ:TestRG1
* 場所:米国東部
* TestVNet1: 10.11.0.0/16 と 10.12.0.0/16
* FrontEnd:10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* GatewayName: VNet1GW
* パブリック IP: VNet1GWIP
* VPNType: RouteBased
* 接続 (1 から 4): VNet1toVNet4
* 接続 (1 から 5): VNet1toVNet5 (VNet が異なるサブスクリプションに存在する場合)

**TestVNet4 の値:**

* VNet 名: TestVNet4
* TestVNet2: 10.41.0.0/16 と 10.42.0.0/16
* FrontEnd:10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* リソース グループ:TestRG4
* 場所:米国西部
* GatewayName: VNet4GW
* パブリック IP: VNet4GWIP
* VPNType: RouteBased
* 接続:VNet4toVNet1

### <a name="step-1---connect-to-your-subscription"></a><a name="Connect"></a>手順 1 - サブスクリプションに接続する

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-numbers-include.md)]

### <a name="step-2---create-and-configure-testvnet1"></a><a name="TestVNet1"></a>手順 2 - TestVNet1 を作成し、構成する

1. リソース グループを作成します。

   ```azurecli
   az group create -n TestRG1  -l eastus
   ```
2. TestVNet1 と TestVNet1 のサブネットを作成します。 次の例では、TestVNet1 という名前の仮想ネットワークと FrontEnd という名前のサブネットを作成します。

   ```azurecli
   az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24
   ```
3. バックエンド サブネット用に追加のアドレス空間を作成します。 この手順では、先ほど作成したアドレス空間と追加するアドレス空間の両方を指定することに注意してください。 これは、[az network vnet update](/cli/azure/network/vnet) コマンドによって前の設定が上書きされるためです。 このコマンドを使用する際は、必ずすべてのアドレス プレフィックスを指定するようにしてください。

   ```azurecli
   az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestRG1
   ```
4. バックエンド サブネットを作成します。
  
   ```azurecli
   az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestRG1 --address-prefix 10.12.0.0/24 
   ```
5. ゲートウェイ サブネットを作成します。 ゲートウェイ サブネットの名前が "GatewaySubnet" であることに注意してください。 この名前は必須です。 この例では、ゲートウェイ サブネットに /27 が使用されています。 /29 と同程度の小規模なゲートウェイ サブネットを作成することはできますが、少なくとも /28 または /27 以上を選択してさらに多くのアドレスが含まれる大規模なサブネットを作成することをお勧めします。 これにより、十分な数のアドレスが、将来的に必要になる可能性のある追加の構成に対応できるようになります。

   ```azurecli 
   az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestRG1 --address-prefix 10.12.255.0/27
   ```
6. VNet 用に作成するゲートウェイに割り当てるパブリック IP アドレスを要求します。 AllocationMethod が Dynamic であることに注意してください。 使用する IP アドレスを指定することはできません。 IP アドレスはゲートウェイに動的に割り当てられます。

   ```azurecli
   az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
   ```
7. TestVNet1 用の仮想ネットワーク ゲートウェイを作成します。 VNet 間構成では、RouteBased VpnType が必要です。 このコマンドの実行時に "--no-wait" パラメーターを使用した場合には、フィードバックや出力が表示されなくなります。 "--no-wait" パラメーターを使用すると、ゲートウェイをバックグラウンドで作成できます。 これは、VPN ゲートウェイの作成がすぐに完了するという意味ではありません。 使用するゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

   ```azurecli
   az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-3---create-and-configure-testvnet4"></a><a name="TestVNet4"></a>手順 3 - TestVNet4 を作成し、構成する

1. リソース グループを作成します。

   ```azurecli
   az group create -n TestRG4  -l westus
   ```
2. TestVNet4 を作成します。

   ```azurecli
   az network vnet create -n TestVNet4 -g TestRG4 --address-prefix 10.41.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.41.0.0/24
   ```

3. TestVNet4 用に追加のサブネットを作成します。

   ```azurecli
   az network vnet update -n TestVNet4 --address-prefixes 10.41.0.0/16 10.42.0.0/16 -g TestRG4 
   az network vnet subnet create --vnet-name TestVNet4 -n BackEnd -g TestRG4 --address-prefix 10.42.0.0/24 
   ```
4. ゲートウェイ サブネットを作成します。

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet4 -n GatewaySubnet -g TestRG4 --address-prefix 10.42.255.0/27
   ```
5. パブリック IP アドレスを要求します。

   ```azurecli
   az network public-ip create -n VNet4GWIP -g TestRG4 --allocation-method Dynamic
   ```
6. TestVNet4 の仮想ネットワーク ゲートウェイを作成します。

   ```azurecli
   az network vnet-gateway create -n VNet4GW -l westus --public-ip-address VNet4GWIP -g TestRG4 --vnet TestVNet4 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-4---create-the-connections"></a><a name="createconnect"></a>手順 4 - 接続を作成する

ここまでで、VPN ゲートウェイを備えた VNet を 2つ用意できました。 次の手順では、仮想ネットワーク ゲートウェイの間に VPN ゲートウェイ接続を作成します。 上記の例を使用した場合、VNet ゲートウェイは異なるリソース グループに存在します。 ゲートウェイが異なるリソース グループにある場合は、接続時に各ゲートウェイのリソース ID を特定して指定する必要があります。 VNet が同じリソース グループにある場合は、リソース ID を指定する必要がないため、[2 番目の手順](#samerg)を使用できます。

### <a name="to-connect-vnets-that-reside-in-different-resource-groups"></a><a name="diffrg"></a>異なるリソース グループにある VNet を接続するには

1. 次のコマンドの出力から、VNet1GW のリソース ID を取得します。

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   出力結果から "id:" 行を探し出します。 次のセクションで接続を作成する際に引用符で囲まれた値が必要になります。 これらの値は、メモ帳などのテキスト エディターにコピーしてください。そうすることで、接続の作成時に簡単に貼り付けることができます。

   出力例:

   ```
   "activeActive": false, 
   "bgpSettings": { 
    "asn": 65515, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
   }, 
   "enableBgp": false, 
   "etag": "W/\"ecb42bc5-c176-44e1-802f-b0ce2962ac04\"", 
   "gatewayDefaultSite": null, 
   "gatewayType": "Vpn", 
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW", 
   "ipConfigurations":
   ```

   **"id":** の後にある引用符で囲まれた値をコピーします。

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. VNet4GW のリソース ID を取得し、その値をテキスト エディターにコピーします。

   ```azurecli
   az network vnet-gateway show -n VNet4GW -g TestRG4
   ```

3. TestVNet1 から TestVNet4 への接続を作成します。 この手順では、TestVNet1 から TestVNet4 への接続を作成します。 この例では、参照される共有キーがあります。 共有キーには独自の値を使用することができます。 両方の接続の共有キーが一致することが重要です。 接続の作成が完了するまでしばらくかかります。

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW 
   ```
4. TestVNet4 から TestVNet1 への接続を作成します。 この手順は上記の手順と同じですが、TestVNet4 から TestVNet1 への接続になります。 共有キーが一致することを確認してください。 接続が確立されるまで数分かかります。

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG4 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW -l westus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1G
   ```
5. 接続を確認します。 [接続の確認](#verify)に関するセクションを参照してください。

### <a name="to-connect-vnets-that-reside-in-the-same-resource-group"></a><a name="samerg"></a>同じリソース グループにある VNet を接続するには

1. TestVNet1 から TestVNet4 への接続を作成します。 この手順では、TestVNet1 から TestVNet4 への接続を作成します。 この例ではリソース グループが同じであることに注意してください。 ここでも参照される共有キーが表示されます。 共有キーには独自の値を使用することができますが、両方の接続の共有キーが一致する必要があります。 接続の作成が完了するまでしばらくかかります。

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet4GW
   ```
2. TestVNet4 から TestVNet1 への接続を作成します。 この手順は上記の手順と同じですが、TestVNet4 から TestVNet1 への接続になります。 共有キーが一致することを確認してください。 接続が確立されるまで数分かかります。

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG1 --vnet-gateway1 VNet4GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet1GW
   ```
3. 接続を確認します。 [接続の確認](#verify)に関するセクションを参照してください。

## <a name="connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>異なるサブスクリプション内にある VNet の接続

このシナリオでは、TestVNet1 と TestVNet5 を接続します。 VNet は異なるサブスクリプション内に存在します。 サブスクリプションが同じ Active Directory テナントに関連付けられている必要はありません。 この構成の手順では、TestVNet1 を TestVNet5 に接続するために VNet 間接続を追加します。

### <a name="step-5---create-and-configure-testvnet1"></a><a name="TestVNet1diff"></a>手順 5 - TestVNet1 を作成し、構成する

以下の手順は、前のセクションで説明した手順の続きです。 TestVNet1 と TestVNet1 の VPN ゲートウェイを作成して構成するには、[手順 1.](#Connect) と[手順 2.](#TestVNet1) を完了する必要があります。 この構成では、前のセクションの TestVNet4 を作成する必要はありません。ただし、TestVNet4 を作成しても以下の手順に影響はありません。 手順 1. と手順 2. が完了したら、(次の) 手順 6. に進んでください。

### <a name="step-6---verify-the-ip-address-ranges"></a><a name="verifyranges"></a>手順 6 - IP アドレス範囲を確認する

追加の接続を作成する場合、重要なのは、新しい仮想ネットワークの IP アドレス空間が、他の VNet 範囲またはローカル ネットワーク ゲートウェイ範囲のどれとも重複していないことを確認することです。 この演習では、TestVNet5 に次の値を使用します。

**TestVNet5 の値:**

* VNet 名: TestVNet5
* リソース グループ:TestRG5
* 場所:東日本
* TestVNet5: 10.51.0.0/16 と 10.52.0.0/16
* FrontEnd:10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* パブリック IP: VNet5GWIP
* VPNType: RouteBased
* 接続:VNet5toVNet1
* ConnectionType: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a><a name="TestVNet5"></a>手順 7 - TestVNet5 を作成し、構成する

この手順は、新しいサブスクリプション (サブスクリプション 5) との関連で実行する必要があります。 この部分は、サブスクリプションを所有する別の組織の管理者が実行することがあります。 サブスクリプションを切り替えるには、`az account list --all` を使用して、ご自分のアカウントで使用できるサブスクリプションを一覧表示します。次に、`az account set --subscription <subscriptionID>` を実行して、使用するサブスクリプションに切り替えます。

1. サブスクリプション 5 に接続していることを確認し、リソース グループを作成します。

   ```azurecli
   az group create -n TestRG5  -l japaneast
   ```
2. TestVNet5 を作成します。

   ```azurecli
   az network vnet create -n TestVNet5 -g TestRG5 --address-prefix 10.51.0.0/16 -l japaneast --subnet-name FrontEnd --subnet-prefix 10.51.0.0/24
   ```

3. サブネットを追加します。

   ```azurecli
   az network vnet update -n TestVNet5 --address-prefixes 10.51.0.0/16 10.52.0.0/16 -g TestRG5
   az network vnet subnet create --vnet-name TestVNet5 -n BackEnd -g TestRG5 --address-prefix 10.52.0.0/24
   ```

4. ゲートウェイ サブネットを追加します。

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet5 -n GatewaySubnet -g TestRG5 --address-prefix 10.52.255.0/27
   ```

5. パブリック IP アドレスを要求します。

   ```azurecli
   az network public-ip create -n VNet5GWIP -g TestRG5 --allocation-method Dynamic
   ```
6. TestVNet5 ゲートウェイを作成する

   ```azurecli
   az network vnet-gateway create -n VNet5GW -l japaneast --public-ip-address VNet5GWIP -g TestRG5 --vnet TestVNet5 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-8---create-the-connections"></a><a name="connections5"></a>手順 8 - 接続を作成する

ゲートウェイが異なるサブスクリプションにあるため、この手順は、 **[サブスクリプション 1]** と **[サブスクリプション 5]** というマークの付いた 2 つの CLI セッションに分かれています。 サブスクリプションを切り替えるには、`az account list --all` を使用して、ご自分のアカウントで使用できるサブスクリプションを一覧表示します。次に、`az account set --subscription <subscriptionID>` を実行して、使用するサブスクリプションに切り替えます。

1. **[サブスクリプション 1]** サブスクリプション 1 にログインして接続します。 次のコマンドを実行し、その出力からゲートウェイの名前と ID を取得します。

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   出力の "id:" をコピーします。 メールやその他の方法で、VNet ゲートウェイ (VNet1GW) の ID と名前をサブスクリプション 5 の管理者に送信します。

   出力例:

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. **[サブスクリプション 5]** サブスクリプション 5 にログインして接続します。 次のコマンドを実行し、その出力からゲートウェイの名前と ID を取得します。

   ```azurecli
   az network vnet-gateway show -n VNet5GW -g TestRG5
   ```

   出力の "id:" をコピーします。 メールやその他の方法で、VNet ゲートウェイ (VNet5GW) の ID と名前をサブスクリプション 1 の管理者に送信します。

3. **[サブスクリプション 1]** この手順では、TestVNet1 から TestVNet5 への接続を作成します。 共有キーには独自の値を使用することができますが、両方の接続の共有キーが一致する必要があります。 接続の作成完了までしばらくかかります。  サブスクリプション 1 に接続します。

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet5 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```

4. **[サブスクリプション 5]** この手順は上記の手順と同じですが、TestVNet5 から TestVNet1 への接続になります。 同じ共有キーを使用し、サブスクリプション 5 に接続してください。

   ```azurecli
   az network vpn-connection create -n VNet5ToVNet1 -g TestRG5 --vnet-gateway1 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW -l japaneast --shared-key "eeffgg" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```

## <a name="verify-the-connections"></a><a name="verify"></a>接続の確認
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>VNet 間接続に関してよく寄せられる質問
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>次のステップ

* 接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines のドキュメント](../index.yml)を参照してください。
* BGP の詳細については、[BGP の概要](vpn-gateway-bgp-overview.md)に関する記事と [BGP の構成方法](vpn-gateway-bgp-resource-manager-ps.md)に関する記事を参照してください。