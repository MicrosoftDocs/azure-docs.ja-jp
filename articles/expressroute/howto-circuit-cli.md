---
title: 'クイックスタート: ExpressRoute 回線の作成と変更:Azure CLI'
description: このクイックスタートでは、Azure CLI を使用した ExpressRoute 回線の作成、プロビジョニング、確認、更新、削除、プロビジョニング解除の方法を示します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: a1d50c3f8f94fbfd7dbcb9b25e051b7f2951c518
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91969094"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-cli"></a>クイックスタート: Azure CLI を使用した ExpressRoute 回線の作成と変更

このクイックスタートでは、コマンド ライン インターフェイス (CLI) を使用して Azure ExpressRoute 回線を作成する方法について説明します。 回線の状態確認、更新、削除のほか、プロビジョニング解除の方法も紹介します。

## <a name="prerequisites"></a>前提条件

* 構成を開始する前に、[前提条件](expressroute-prerequisites.md)と[ワークフロー](expressroute-workflows.md)を確認してください。
* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 最新バージョンの CLI コマンド (2.0 以降) をインストールします。 CLI コマンドのインストール方法については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」および「[Azure CLI を使ってみる](/cli/azure/get-started-with-azure-cli)」を参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute 回線の作成とプロビジョニング

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure アカウントにサインインしてサブスクリプションを選択する

構成を始めるには、Azure アカウントにサインインします。 Cloud Shell の "試してみる" オプションを使用している場合は、自動的にサインインします。 接続については、次の例を参照してください。

```azurecli-interactive
az login
```

アカウントのサブスクリプションを確認します。

```azurecli-interactive
az account list
```

ExpressRoute 回線を作成するサブスクリプションを選択します。

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>サポートされるプロバイダー、場所、帯域幅のリストを取得する

ExpressRoute 回線を作成する前に、サポートされている接続プロバイダー、場所、帯域幅オプションのリストが必要になります。 CLI コマンド `az network express-route list-service-providers` によってこの情報が返されます。この情報は、後のステップで使用します。

```azurecli-interactive
az network express-route list-service-providers
```

応答は次の例のようになります。

```output
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

応答をチェックして、ご利用の接続プロバイダーがリストにあるかどうかを確認します。 以下の情報は、回線を作成する際に必要になるため、書き留めておいてください。

* 名前
* PeeringLocations
* BandwidthsOffered

これで、ExpressRoute 回線を作成する準備が整いました。

### <a name="create-an-expressroute-circuit"></a>ExpressRoute 回線の作成

> [!IMPORTANT]
> ExpressRoute 回線の課金は、サービス キーが発行されたときから始まります。 接続プロバイダーが回線をプロビジョニングする準備ができたら、この操作を実行します。
>
>

リソース グループがまだない場合は、ExpressRoute 回線を作成する前に、作成しておく必要があります。 リソース グループを作成するには、次のコマンドを実行します。

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

以下の例では、Silicon Valley の Equinix を通じて 200 Mbps の ExpressRoute 回線を作成する方法を示します。 別のプロバイダーおよび異なる設定を使用する場合は、要求を実行するときにその情報に置き換えてください。

必ず、適切な SKU レベルと SKU ファミリを指定してください。

* SKU レベルによって、ExpressRoute 回線が [Local](expressroute-faqs.md#expressroute-local)、Standard、[Premium](expressroute-faqs.md#expressroute-premium) のどれであるかが決まります。 *Local*、*Standard、または *Premium* を指定できます。 SKU を *[Standard] または [Premium]* から *[Local]* に変更することはできません。
* SKU ファミリによって、課金の種類が決まります。 従量制課金データ プランの場合は *MeteredData*、無制限データ プランの場合は *UnlimitedData* を指定できます。 課金の種類は *MeteredData* から *UnlimitedData* に変更できますが、その種類を *UnlimitedData* から *MeteredData* に変更することはできません。 *Local* 回線は *UnlimitedData* のみです。


ExpressRoute 回線の課金は、サービス キーが発行されたときから始まります。 以下に、新しいサービス キーの要求の例を示します。

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

応答にはサービス キーが含まれます。

### <a name="list-all-expressroute-circuits"></a>すべての ExpressRoute 回線を一覧表示する

作成したすべての ExpressRoute 回路の一覧を取得するには、`az network express-route list` コマンドを実行します。 この情報は、このコマンドを使用していつでも取得できます。 すべての回線を一覧表示するには、パラメーターを付けずに呼び出します。

```azurecli-interactive
az network express-route list
```

サービス キーは、応答の *ServiceKey* フィールドに一覧表示されます。

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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

"-h" パラメーターを使ってコマンドを実行することで、すべてのパラメーターの詳細な説明を取得できます。

```azurecli-interactive
az network express-route list -h
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>プロビジョニングのためにサービス キーを接続プロバイダーに送信する

"ServiceProviderProvisioningState" は、サービス プロバイダー側でのプロビジョニングの現在の状態に関する情報を提供します。 Status は、Microsoft 側での状態を提供します。 詳細については、[ワークフローに関する記事](expressroute-workflows.md#expressroute-circuit-provisioning-states)を参照してください。

新しい ExpressRoute 回線を作成する場合、この回線は次の状態になります。

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

回線は、接続プロバイダーが有効にしている間、次の状態に変化します。

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

ExpressRoute 回線を使用するには、次の状態になっている必要があります。

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>回線キーのステータスと状態を定期的に確認する

サービス キーのステータスと状態を確認すれば、ご利用の回線をプロバイダーがプロビジョニングした時期を知ることができます。 回線が構成されると、以下の例に示すように、*ServiceProviderProvisioningState* が *Provisioned* と表示されます。

```azurecli-interactive
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
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="create-your-routing-configuration"></a>ルーティング構成を作成する

回線ピアリングの作成と変更の詳しい手順については、「 [PowerShell を使用した ExpressRoute 回線のルーティングの作成と変更](howto-routing-cli.md) 」を参照してください。

> [!IMPORTANT]
> 次の手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されます。 サービス プロバイダーが提供する管理対象レイヤー 3 サービス (MPLS など、通常は IP VPN) を使用する場合、接続プロバイダーがユーザーに代わってルーティングを構成して管理します。
>
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>ExpressRoute 回線への仮想ネットワークのリンク

次に、ExpressRoute 回線に仮想ネットワークをリンクします。 [ExpressRoute 回線への仮想ネットワークのリンク](howto-linkvnet-cli.md)に関する記事を参照してください。

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute 回線の変更

ExpressRoute 回線の特定のプロパティは、接続に影響を与えることなく変更できます。 次の変更は、ダウンタイムなしで行うことができます。

* ExpressRoute 回線の ExpressRoute Premium アドオンを有効または無効にすることができます。 SKU を *[Standard] または [Premium]* から *[Local]* に変更することはサポートされていません。
* ポート上に使用可能な容量があれば、ExpressRoute 回線の帯域幅を増やすことができます。 ただし、回線の帯域幅のダウングレードはサポートされていません。
* 課金プランを従量制課金データから無制限データに変更することができます。 ただし、無制限データから従量制課金データへの課金プランの変更はサポートされていません。
* *従来の操作の許可* を有効または無効にできます。

制限と制約事項の詳細は、「[ExpressRoute の FAQ](expressroute-faqs.md)」を参照してください。

### <a name="to-enable-the-expressroute-premium-add-on"></a>ExpressRoute Premium アドオンを有効にするには

次のコマンドを使用して、既存の回線の ExpressRoute Premium アドオンを有効にできます。

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

これで、回線の ExpressRoute Premium アドオン機能が有効になりました。 このコマンドが正常に実行された時点で、Premium アドオン機能の課金が始まります。

### <a name="to-disable-the-expressroute-premium-add-on"></a>ExpressRoute Premium アドオンを無効にするには

> [!IMPORTANT]
> 標準回線で許可されるリソースより多くのリソースを使用する場合、この操作は失敗することがあります。
>
>

ExpressRoute Premium アドオンを無効にする前に、次の条件を把握しておいてください。

* Premium から Standard にダウングレードする前に、回線にリンクされている仮想ネットワークの数が 10 未満であることを確認する必要があります。 そうでない場合、更新要求が失敗し、Premium 料金で課金されます。
* 他の地理的リージョン内のすべての仮想ネットワークのリンクをまず解除する必要があります。 リンクを解除しないと、更新要求が失敗し、Premium 料金で引き続き課金されることになります。
* プライベート ピアリングの場合、ルート テーブルのサイズを 4,000 ルート未満にする必要があります。 ルート テーブルのサイズが 4,000 ルートを超えた場合、BGP セッションがドロップします。 アドバタイズされたプレフィックスの数が 4,000 を下回るまで、BGP セッションは有効な状態に戻りません。

次の例を使用し、既存の回線の ExpressRoute Premium アドオンを無効にできます。

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>ExpressRoute 回線の帯域幅を更新するには

プロバイダーでサポートされている帯域幅のオプションについては、「[ExpressRoute の FAQ](expressroute-faqs.md)」を確認してください。 既存の回線のサイズを超えるサイズを選択することができます。

> [!IMPORTANT]
> 既存のポートの容量が不十分な場合、ExpressRoute 回線の再作成が必要になる可能性があります。 その場所に使用可能な追加の容量がない場合、回路をアップグレードすることはできません。
>
> 中断せずに ExpressRoute 回線の帯域幅を減らすことはできません。 帯域幅をダウングレードするには、ExpressRoute 回線のプロビジョニングを解除してから、新しい ExpressRoute 回線を再度プロビジョニングする必要があります。
>

必要なサイズを決定した後、次のコマンドを使用して、回線のサイズを変更できます。

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

お客様の回線のアップグレードは Microsoft 側で行われます。 次に、接続プロバイダーに連絡し、この変更に合わせて接続プロバイダー側の構成を更新するよう依頼する必要があります。 その通知が完了すると、更新された帯域幅オプションでの課金が開始されます。

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>SKU を従量制から無制限に変更するには

ExpressRoute 回線の SKU を変更するには、次の例を使用します。

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>クラシック環境および Resource Manager 環境へのアクセスを制御するには

「[クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線の移行](expressroute-howto-move-arm.md)」の説明を参照してください。

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute 回線のプロビジョニング解除

ExpressRoute 回線のプロビジョニングを解除して削除する場合は必ず、次の条件を把握しておいてください。

* ExpressRoute 回線からすべての仮想ネットワークのリンクを解除する必要があります この操作が失敗した場合は、回線にリンクされている仮想ネットワークがないか確認してください。
* ExpressRoute 回線サービス プロバイダーのプロビジョニング状態が **プロビジョニング中** または **プロビジョニング済み** の場合、サービス プロバイダー側の回線のプロビジョニングを解除するには、サービス プロバイダーに連絡する必要があります。 Microsoft は、サービス プロバイダーが回線のプロビジョニング解除を完了し、通知するまで、リソースの予約と課金を続行します。
* サービス プロバイダーが回線のプロビジョニングを解除済みである場合 (つまり、サービス プロバイダーのプロビジョニング状態が **未プロビジョニング** に設定されている場合)、回線を削除することができます。 それにより、回線の課金は停止します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ExpressRoute 回線は、次のコマンドを実行して削除できます。

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>次のステップ

回線を作成し、プロバイダーでプロビジョニングしたら、次の手順に進み、ピアリングを構成します。

> [!div class="nextstepaction"]
> [ExpressRoute 回線のルーティングの作成と変更を行う](howto-routing-cli.md)
