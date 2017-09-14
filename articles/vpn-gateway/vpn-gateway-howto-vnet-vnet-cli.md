---
title: "仮想ネットワークを別の VNet に接続する: Azure CLI | Microsoft Docs"
description: "この記事では、Azure Resource Manager と Azure CLI を使用して仮想ネットワークどうしを接続する方法を説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: ae42f661b39e8b6170fd415d758404fb33009ccc
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-azure-cli"></a>Azure CLI を使用して VNet 間の VPN ゲートウェイ接続を構成する

この記事では、仮想ネットワーク間で VPN Gateway 接続を確立する方法について説明します。 仮想ネットワークが属しているリージョンやサブスクリプションは異なっていてもかまいません。 異なるサブスクリプションの VNet を接続する場合、サブスクリプションが同じ Active Directory テナントに関連付けられている必要はありません。 

この記事の手順は、Resource Manager デプロイメント モデルに適用されます。また、この手順では Azure CLI を使用します。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [異なるデプロイメント モデルの接続 - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [異なるデプロイメント モデルの接続 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

仮想ネットワークどうし (VNet 間) の接続は、VNet をオンプレミス サイトの場所に接続することと似ています。 どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。 複数の VNet が同じリージョンに存在する場合、それらを VNet ピアリングで接続することを検討してください。 VNet ピアリングは、VPN ゲートウェイを使用しません。 詳細については、「 [VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)」を参照してください。

マルチサイト構成と VNet 間通信を組み合わせることができます。 そのため、クロスプレミス接続と仮想ネットワーク間接続を組み合わせたネットワーク トポロジを確立することができます (下図参照)。

![接続について](./media/vpn-gateway-howto-vnet-vnet-cli/aboutconnections.png)

### <a name="why"></a>仮想ネットワークを接続する理由

仮想ネットワークを接続するのは次のような場合です。

* **リージョン間の geo 冗長性および geo プレゼンス**

  * インターネット接続エンドポイントを介さず、安全な接続を使って独自の geo レプリケーションや geo 同期をセットアップすることができます。
  * Azure Traffic Manager および Load Balancer を使用し、複数の Azure リージョンをまたぐ geo 冗長性を備えた、可用性に優れたワークロードをセットアップすることができます。 たとえば、複数の Azure リージョンにまたがる SQL AlwaysOn 可用性グループをセットアップすることができます。
* **特定のリージョン内で分離または管理境界を備えた多層アプリケーション**

  * 同じリージョン内で、分離または管理要件に基づいて相互に接続された複数の仮想ネットワークを利用し、多層アプリケーションをセットアップすることができます。

VNet 間接続の詳細については、この記事の最後にある「[VNet 間接続に関してよく寄せられる質問](#faq)」を参照してください。

### <a name="which-set-of-steps-should-i-use"></a>どの手順を利用するべきでしょうか。

この記事では、2 種類の手順について説明します。 1 つは [VNet が同じサブスクリプション内に存在する](#samesub)場合の手順で、もう 1 つは [VNet が異なるサブスクリプション内に存在する](#difsub)場合の手順です。

## <a name="samesub"></a>同じサブスクリプション内にある VNet の接続

![v2v diagram](./media/vpn-gateway-howto-vnet-vnet-cli/v2vrmps.png)

### <a name="before-you-begin"></a>開始する前に

開始する前に、最新バージョンの CLI コマンド (2.0 以降) をインストールします。 CLI コマンドのインストール方法については、「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)](/cli/azure/install-azure-cli)」を参照してください。

### <a name="Plan"></a>IP アドレス範囲の計画

以下の手順に従って、2 つの仮想ネットワークを、それぞれのゲートウェイ サブネットおよび構成と共に作成します。 その後、2 つの VNet 間の VPN 接続を作成します。 ネットワーク構成の IP アドレスの範囲を計画することが重要です。 VNet の範囲やローカル ネットワークの範囲が重複することは、どのような形であれ許容されないので注意してください。 以下の例では、DNS サーバーは含まれていません。 仮想ネットワークの名前解決が必要な場合は、[名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)に関する記事を参照してください。

例では、次の値を使用します。

**TestVNet1 の値:**

* VNet の名前: TestVNet1
* リソース グループ: TestRG1
* 場所: 米国東部
* TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* ゲートウェイの名前: VNet1GW
* パブリック IP: VNet1GWIP
* VPN の種類: RouteBased
* 接続 (1 ～ 4): VNet1toVNet4
* 接続 (1 ～ 5): VNet1toVNet5
* 接続の種類: VNet2VNet

**TestVNet4 の値:**

* VNet の名前: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* リソース グループ: TestRG4
* 場所: 米国西部
* ゲートウェイの名前: VNet4GW
* パブリック IP: VNet4GWIP
* VPN の種類: RouteBased
* 接続: VNet4toVNet1
* 接続の種類: VNet2VNet


### <a name="Connect"></a>手順 1 - サブスクリプションに接続する

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-numbers-include.md)]

### <a name="TestVNet1"></a>手順 2 - TestVNet1 を作成し、構成する

1. リソース グループを作成します。

  ```azurecli
  az group create -n TestRG1  -l eastus
  ```
2. TestVNet1 と TestVNet1 のサブネットを作成します。 次の例では、TestVNet1 という名前の仮想ネットワークと FrontEnd という名前のサブネットを作成します。

  ```azurecli
  az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24
  ```
3. バックエンド サブネット用に追加のアドレス空間を作成します。 この手順では、先ほど作成したアドレス空間と追加するアドレス空間の両方を指定することに注意してください。 これは、[az network vnet update](https://docs.microsoft.com/cli/azure/network/vnet#update) コマンドによって前の設定が上書きされるためです。 このコマンドを使用する際は、必ずすべてのアドレス プレフィックスを指定するようにしてください。

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

### <a name="TestVNet4"></a>手順 3 - TestVNet4 を作成し、構成する

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

### <a name="createconnect"></a>手順 4 - 接続を作成する

ここまでで、VPN ゲートウェイを備えた VNet を 2つ用意できました。 次の手順では、仮想ネットワーク ゲートウェイの間に VPN ゲートウェイ接続を作成します。 上記の例を使用した場合、VNet ゲートウェイは異なるリソース グループに存在します。 ゲートウェイが異なるリソース グループにある場合は、接続時に各ゲートウェイのリソース ID を特定して指定する必要があります。 VNet が同じリソース グループにある場合は、リソース ID を指定する必要がないため、[2 番目の手順](#samerg)を使用できます。

### <a name="diffrg"></a>異なるリソース グループにある VNet を接続するには

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

### <a name="samerg"></a>同じリソース グループにある VNet を接続するには

1. TestVNet1 から TestVNet4 への接続を作成します。 この手順では、TestVNet1 から TestVNet4 への接続を作成します。 この例ではリソース グループが同じであることに注意してください。 ここでも参照される共有キーが表示されます。 共有キーには独自の値を使用することができますが、両方の接続の共有キーが一致する必要があります。 接続の作成が完了するまでしばらくかかります。

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet4GW
  ```
2. TestVNet4 から TestVNet1 への接続を作成します。 この手順は上記の手順と同じですが、TestVNet4 から TestVNet1 への接続になります。 共有キーが一致することを確認してください。 接続が確立されるまで数分かかります。

  ```azurecli
  az network vpn-connection create -n VNet4ToVNet1 -g TestRG1 --vnet-gateway1 VNet4GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet1GW
  ```
3. 接続を確認します。 [接続の確認](#verify)に関するセクションを参照してください。

## <a name="difsub"></a>異なるサブスクリプション内にある VNet の接続

![v2v diagram](./media/vpn-gateway-howto-vnet-vnet-cli/v2vdiffsub.png)

このシナリオでは、TestVNet1 と TestVNet5 を接続します。 VNet は異なるサブスクリプション内に存在します。 サブスクリプションが同じ Active Directory テナントに関連付けられている必要はありません。 この構成の手順では、TestVNet1 を TestVNet5 に接続するために VNet 間接続を追加します。

### <a name="TestVNet1diff"></a>手順 5 - TestVNet1 を作成し、構成する

以下の手順は、前のセクションで説明した手順の続きです。 TestVNet1 と TestVNet1 の VPN ゲートウェイを作成して構成するには、[手順 1.](#Connect) と[手順 2.](#TestVNet1) を完了する必要があります。 この構成では、前のセクションの TestVNet4 を作成する必要はありません。ただし、TestVNet4 を作成しても以下の手順に影響はありません。 手順 1. と手順 2. が完了したら、(次の) 手順 6. に進んでください。

### <a name="verifyranges"></a>手順 6 - IP アドレス範囲を確認する

追加の接続を作成する場合、重要なのは、新しい仮想ネットワークの IP アドレス空間が、他の VNet 範囲またはローカル ネットワーク ゲートウェイ範囲のどれとも重複していないことを確認することです。 この演習では、TestVNet5 に次の値を使用します。

**TestVNet5 の値:**

* VNet の名前: TestVNet5
* リソース グループ: TestRG5
* 場所: 東日本
* TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* パブリック IP: VNet5GWIP
* VPN の種類: RouteBased
* 接続: VNet5toVNet1
* 接続の種類: VNet2VNet

### <a name="TestVNet5"></a>手順 7 - TestVNet5 を作成し、構成する

この手順は、新しいサブスクリプション (サブスクリプション 5) との関連で実行する必要があります。 この部分は、サブスクリプションを所有する別の組織の管理者が実行することがあります。 サブスクリプションを切り替えるには、"az account list --all" を実行して、アカウントで使用できるサブスクリプションを一覧表示します。次に、"az account set --subscription <subscriptionID>" を実行して、使用するサブスクリプションに切り替えます。

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

### <a name="connections5"></a>手順 8 - 接続を作成する

ゲートウェイが異なるサブスクリプションにあるため、**[サブスクリプション 1]** と **[サブスクリプション 5]** というマークの付いた 2 つの CLI セッションにこの手順を分けました。 サブスクリプションを切り替えるには、"az account list --all" を実行して、アカウントで使用できるサブスクリプションを一覧表示します。次に、"az account set --subscription <subscriptionID>" を実行して、使用するサブスクリプションに切り替えます。

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

3. **[サブスクリプション 1]** この手順では、TestVNet1 から TestVNet5 への接続を作成します。 共有キーには独自の値を使用することができますが、両方の接続の共有キーが一致する必要があります。 接続の作成完了までしばらくかかります。 サブスクリプション 1 に接続します。

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet5 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
  ```

4. **[サブスクリプション 5]** この手順は上記の手順と同じですが、TestVNet5 から TestVNet1 への接続になります。 同じ共有キーを使用し、サブスクリプション 5 に接続してください。

  ```azurecli
  az network vpn-connection create -n VNet5ToVNet1 -g TestRG5 --vnet-gateway1 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW -l japaneast --shared-key "eeffgg" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
  ```

## <a name="verify"></a>接続の確認
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections v2v cli](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="faq"></a>VNet 間接続に関してよく寄せられる質問
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>次のステップ

* 接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines のドキュメント](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)を参照してください。
* BGP の詳細については、[BGP の概要](vpn-gateway-bgp-overview.md)に関する記事と [BGP の構成方法](vpn-gateway-bgp-resource-manager-ps.md)に関する記事を参照してください。

