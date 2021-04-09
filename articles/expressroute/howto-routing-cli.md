---
title: チュートリアル:ExpressRoute 回線のピアリングの構成 - Azure CLI
description: このチュートリアルでは、ExpressRoute 回線のプライベート、パブリック、および Microsoft ピアリングを作成し、プロビジョニングする方法を説明します。 この記事では、回線のピアリングの状態確認、更新、または削除の方法も示します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: duau
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2c56e847e3b112d50285cd2c116c8f22efbc507f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715532"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>チュートリアル:CLI を使用した ExpressRoute 回線のピアリングの作成と変更

このチュートリアルでは、CLI を使用して Resource Manager デプロイ モデルで ExpressRoute 回線のルーティング構成またはピアリングを作成および管理する方法について説明します。 また、ExpressRoute 回線の状態確認、ピアリングの更新、または削除およびプロビジョニング解除を行うこともできます。 別の方法を使用して回線を操作する場合は、次の一覧から記事を選択してください。

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [パブリック ピアリング](about-public-peering.md)
> * [ビデオ - プライベート ピアリング](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [ビデオ - Microsoft ピアリング](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (クラシック)](expressroute-howto-routing-classic.md)
> 

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - 回線用に Microsoft ピアリングを構成、更新、削除する
> - 回線用に Azure プライベート ピアリングを構成、更新、削除する

## <a name="prerequisites"></a>前提条件

* 開始する前に、最新バージョンの CLI コマンド (2.0 以降) をインストールします。 CLI コマンドのインストール方法については、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」をご覧ください。
* 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、[ワークフロー](expressroute-workflows.md)の各ページを確認してください。
* アクティブな ExpressRoute 回線が必要です。 手順に従って、[ExpressRoute 回線を作成](howto-circuit-cli.md)し、接続プロバイダー経由で回線を有効にしてから続行してください。 この記事のコマンドを実行するには、ExpressRoute 回線がプロビジョニングされ、有効な状態になっている必要があります。

次の手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されます。 サービス プロバイダーが提供するマネージド レイヤー 3 サービス (MPLS など、通常は IPVPN) を使用する場合、接続プロバイダーがユーザーに代わってルーティングを構成および管理します。

ExpressRoute 回線にプライベート ピアリングおよび Microsoft ピアリングを構成できます。 ピアリングは、任意の順序で構成できます。 ただし、各ピアリングの構成は必ず一度に 1 つずつ完了するようにしてください。 ルーティング ドメインとピアリングの詳細については、[ExpressRoute のルーティング ドメイン](expressroute-circuit-peerings.md)に関する記事をご覧ください。

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft ピアリング

このセクションでは、ExpressRoute 回線用の Microsoft ピアリング構成を作成、取得、更新、および削除します。

> [!IMPORTANT]
> 2017 年 8 月 1 日より前に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが定義されていない場合でも、すべてのサービス プレフィックスが Microsoft ピアリングでアドバタイズされます。 2017 年 8 月 1 日以降に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが回線に接続されるまで、プレフィックスはアドバタイズされません。 詳しくは、「[Configure a route filter for Microsoft peering](how-to-routefilter-powershell.md)」(Microsoft ピアリング用にルート フィルターを構成する) をご覧ください。
> 


### <a name="to-create-microsoft-peering"></a>Microsoft ピアリングを作成するには

1. 最新バージョンの Azure CLI をインストールします。 最新バージョンの Azure コマンド ライン インターフェイス (CLI) を使用します。

   ```azurecli
   az login
   ```

   ExpressRoute 回線を作成するサブスクリプションを選択します。

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

1. ExpressRoute 回線を作成します。 手順に従って、 [ExpressRoute 回線](howto-circuit-cli.md) を作成し、接続プロバイダー経由で回線をプロビジョニングします。 接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Microsoft ピアリングを有効にするように接続プロバイダーに依頼できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、次の手順を使用して、構成を続行します。 

1. ExpressRoute 回線がプロビジョニングされ、有効になっていることを確認します。 次の例を使用してください。

   ```azurecli
   az network express-route list
   ```

   応答は次の例のようになります。

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. 回路の Microsoft ピアリングを構成する 続行する前に、次の情報を確認してください。

   * プライマリ リンク用の /30 サブネット。 アドレス ブロックは、自分が所有し、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。
   * セカンダリ リンク用の /30 サブネット。 アドレス ブロックは、自分が所有し、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。
   * アドバタイズするプレフィックス:BGP セッションを介してアドバタイズする予定のすべてのプレフィックスのリストを指定します。 パブリック IP アドレス プレフィックスのみが受け入れられます。 一連のプレフィックスを送信する場合は、コンマ区切りのリストを送信できます。 これらのプレフィックスは、RIR/IRR に登録する必要があります。
   * **省略可能 -** 顧客 ASN:ピアリング AS 番号に登録されていないプレフィックスをアドバタイズしている場合は、それらが登録されている AS 番号を指定できます。
   * ルーティング レジストリ名: AS 番号とプレフィックスを登録する RIR/IRR を指定することができます。
   * **省略可能 -** MD5 を使用する場合には、パスワードを準備します。

   次の例を実行して、回線用に Microsoft ピアリングを構成します。

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft ピアリングの詳細を表示するには

構成の詳細を取得するには、次の例を使用します。

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Microsoft は、指定された 'アドバタイズされたパブリック プレフィックス' と 'ピア ASN' (または '顧客 ASN') がインターネット ルーティング レジストリでユーザーに割り当てられているかどうかを確認します。 別のエンティティからパブリック プレフィックスを取得している場合、およびルーティング レジストリに割り当てが記録されていない場合、自動検証は完了せず、手動検証が必要になります。 自動検証が失敗すると、上記のコマンドの出力時に 'AdvertisedPublicPrefixesState' が '検証が必要です' と表示されます。 
> 
> '検証が必要です' というメッセージが表示された場合は、ルーティング レジストリにプレフィックスの所有者として一覧表示されているエンティティによって組織にパブリック プレフィックスが割り当てられていることを示すドキュメントを収集し、サポート チケットを開くことにより、これらのドキュメントを手動検証のために送信してください。 
> 
>

出力は次の例のようになります。

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Microsoft ピアリング構成を更新するには

構成のどの部分でも更新することができます。 次の例では、回線のアドバタイズされたプレフィックスが 123.1.0.0/24 から 124.1.0.0/24 に更新されています。

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>IPv6 Microsoft ピアリング設定を既存の IPv4 構成に追加するには

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

## <a name="azure-private-peering"></a><a name="private"></a>Azure プライベート ピアリング

このセクションでは、ExpressRoute 回線用の Azure プライベート ピアリング構成を作成、取得、更新、および削除します。

### <a name="to-create-azure-private-peering"></a>Azure プライベート ピアリングを作成するには

1. 最新バージョンの Azure CLI をインストールします。
1. 
   ```azurecli
   az login
   ```

   ExpressRoute 回線を作成するサブスクリプションを選択します。

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
1. ExpressRoute 回線を作成します。 手順に従って、 [ExpressRoute 回線](howto-circuit-cli.md) を作成し、接続プロバイダー経由で回線をプロビジョニングします。 接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Azure プライベート ピアリングを有効にするように接続プロバイダーに依頼できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、接続プロバイダーがルーティングを管理しない場合は、回線を作成した後、次の手順を使用して、構成を続行します。

1. ExpressRoute 回線がプロビジョニングされ、有効になっていることを確認します。 次の例を使用してください。

   ```azurecli
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   応答は次の例のようになります。

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Silicon Valley",
   "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

1. 回線用に Azure プライベート ピアリングを構成します。 次の手順に進む前に、以下のものがそろっていることを確認します。

   * 仮想ネットワーク用に予約されたアドレス空間の一部ではないサブネットのペア。 一方のサブネットはプライマリ リンク用に使用され、もう一方はセカンダリ リンク用に使用されます。 これらの各サブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。 このサブネットのペアには、3 つのオプションがあります。
       * IPv4: 2 つの /30 サブネット。
       * IPv6: 2 つの /126 サブネット。
       * 両方: 2 つの /30 サブネットと 2 つの /126 サブネット。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。 このピアリングではプライベート AS 番号を使用できます。 65515 は使用しないようにしてください。
   * **省略可能 -** MD5 を使用する場合には、パスワードを準備します。

   次の例を使用して、回線用に Azure プライベート ピアリングを構成します。

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   MD5 ハッシュを使用する場合は、次の例を使用します。

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 顧客 ASN ではなく、ピアリング ASN として AS 番号を指定するようにしてください。
   > 
   > 

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Azure プライベート ピアリングの詳細を表示するには

構成の詳細を取得するには、次の例を使用します。

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

出力は次の例のようになります。

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Azure プライベート ピアリングの構成を更新するには

構成の任意の部分を更新するには、次の例を使用します。 この例では、回線の VLAN ID が 100 から 500 に更新されています。

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft ピアリングを削除するには

ピアリング構成を削除するには、次の例を実行します。

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure プライベート ピアリングを削除するには

ピアリング構成を削除するには、次の例を実行します。

> [!WARNING]
> この例を実行する前に、すべての仮想ネットワークと ExpressRoute Global Reach 接続が削除されていることを確認する必要があります。 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="next-steps"></a>次のステップ

Azure プライベート ピアリングを構成すると、仮想ネットワークを回線にリンクできます。詳細については、次を参照してください。 

> [!div class="nextstepaction"]
> [ExpressRoute 回線への VNet のリンク](howto-linkvnet-cli.md)
