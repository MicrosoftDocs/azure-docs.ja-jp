---
title: パブリック IP アドレスのない Azure Batch プールを作成する
description: パブリック IP アドレスのないプールを作成する方法について説明します
author: pkshultz
ms.topic: how-to
ms.date: 12/9/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 806e85fca0a509d56e248fc7779fba0f0a59a61d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97007672"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>パブリック IP アドレスのない Azure Batch プールを作成する

Azure Batch プールを作成する場合は、パブリック IP アドレスのない仮想マシン構成プールをプロビジョニングすることができます。 この記事では、パブリック IP アドレスのない Batch プールを設定する方法について説明します。

## <a name="why-use-a-pool-without-public-ip-addresses"></a>パブリック IP アドレスのないプールを使用する理由

既定では、Azure Batch 仮想マシン構成プール内のすべてのコンピューティング ノードに、パブリック IP アドレスが割り当てられます。 このアドレスは、タスクをスケジュールするため、およびコンピューティング ノードとの通信 (インターネットへの送信アクセスなど) を行うために Batch サービスによって使用されます。

これらのノードへのアクセスを制限し、インターネットからのこれらのノードの探索可能性を低く抑えるには、パブリック IP アドレスのないプールをプロビジョニングできます。

> [!IMPORTANT]
> 現在、次のリージョンでは、パブリック IP アドレスがない Azure Batch プールのサポートは、パブリックプレビュー段階にあります。フランス中部、東アジア、米国中西部、米国中南部、米国西部 2、米国東部、北ヨーロッパ、米国東部 2、米国中部、西ヨーロッパ、米国中北部、米国西部、オーストラリア東部、東日本、西日本
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- **[認証]** : [仮想ネットワーク](./batch-virtual-network.md)内でパブリック IP アドレスのないプールを使用するには、Batch クライアント API で Azure Active Directory (AD) 認証を使用する必要があります。 Azure AD の Azure Batch のサポートについては、「[Batch サービスの認証に Active Directory を使用する](batch-aad-auth.md)」に記載されています。 仮想ネットワーク内にご自分のプールを作成しない場合は、Azure AD 認証またはキーベースの認証のいずれかを使用できます。

- **Azure VNet**。 [仮想ネットワーク](batch-virtual-network.md)内にプールを作成する場合は、次の要件と構成に従ってください。 1 つまたは複数のサブネットを持つ VNet を前もって用意するために、Azure Portal、Azure PowerShell、Azure コマンド ライン インターフェイス (CLI)、その他の方法を利用できます。
  - VNET が存在するサブスクリプションとリージョンは、プールの作成に使用する Batch アカウントと同じである必要があります。
  - プールに指定されたサブネットには、プールの対象となる VM 数 (つまり、プールの `targetDedicatedNodes` および `targetLowPriorityNodes` プロパティの合計) に対応できる十分な未割り当て IP アドレスが必要です。 サブネットの未割り当て IP アドレスが十分でない場合、プールによってコンピューティング ノードが部分的に割り当てられ、サイズ変更エラーが発生します。
  - プライベート リンク サービスとエンドポイントのネットワーク ポリシーを無効にする必要があります。 これを行うには Azure CLI を使用します: ```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --resouce-group <resourcegroup> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```

> [!IMPORTANT]
> 100 の専用ノードまたは優先順位の低いノードごとに、プライベート リンク サービスが 1 つおよびロード バランサーが 1 つ、Batch によって割り当てられます。 これらのリソースは、サブスクリプションの[リソース クォータ](../azure-resource-manager/management/azure-subscription-service-limits.md)によって制限されます。 大規模なプールでは、これらの 1 つまたは複数のリソースについて、[クォータの引き上げの要求](batch-quota-limit.md#increase-a-quota)が必要になる場合があります。 また、Batch によって作成されたリソースにはリソース ロックを適用しないでください。そうしないと、プールの削除やゼロへのサイズ変更など、ユーザーが開始した操作の結果として、リソースのクリーンアップが妨げられるからです。

## <a name="current-limitations"></a>現在の制限

1. パブリック IP アドレスのないプールでは、Cloud Services 構成ではなく、仮想マシンの構成を使用する必要があります。
1. Batch コンピューティング ノードに対する[カスタム エンドポイント構成](pool-endpoint-configuration.md)は、パブリック IP アドレスのないプールでは機能しません。
1. パブリック IP アドレスがないため、この種類のプールでは、[独自に指定したパブリック IP アドレスを使用](create-pool-public-ip.md)することはできません。

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>Azure portal でパブリック IP アドレスのないプールを作成する

1. Azure Portal の Batch アカウントに移動します。
1. 左側の **[設定]** ウィンドウで、 **[プール]** を選択します。
1. **[プール]** ウィンドウで、 **[追加]** を選択します。
1. **[プールの追加]** ウィンドウで、 **[イメージの種類]** ドロップダウンから、使用する予定のオプションを選択します。
1. ご利用のイメージの適切な **[Publisher]\(パブリッシャー\)、[Offer]\(プラン\)、[SKU]** を選択します。
1. **[ノード サイズ]** 、 **[ターゲットの専用ノード数]** 、 **[低優先度ノード]** など、残りの必須の設定ほか、オプションの設定も必要に応じて指定します。
1. 必要に応じて、使用する仮想ネットワークとサブネットを選択します。 この仮想ネットワークは、作成するプールと同じリソース グループに存在する必要があります。
1. **[IP アドレスのプロビジョニングの種類]** で、 **[NoPublicIPAddresses]** を選択します。

![NoPublicIPAddresses が選択された [プールの追加] 画面のスクリーンショット。](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>Batch REST API を使用して、パブリック IP アドレスなしのプールを作成する

次の例では、[Azure Batch REST API](/rest/api/batchservice/pool/add) を使用して、パブリック IP アドレスを使用するプールを作成する方法を示します。

### <a name="rest-api-uri"></a>REST API URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>要求本文

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "taskSlotsPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>インターネットへの送信アクセス

パブリック IP アドレスのないプールでは、[仮想ネットワーク NAT](../virtual-network/nat-overview.md) を使用するなどして、ネットワーク設定を適切に構成しない限り、ご利用の仮想マシンからパブリック インターネットへのアクセスは不可能です。 NAT では、仮想ネットワーク内の仮想マシンからインターネットへの送信アクセスのみが許可されていることに注意してください。 Batch で作成されたコンピューティング ノードにはパブリックにアクセスすることはできません。パブリック IP アドレスが関連付けられていないためです。

送信接続を実現するもう 1 つの方法は、ユーザー定義ルート (UDR) を使用することです。 これにより、パブリック インターネットアクセスが可能なプロキシ コンピューターにトラフィックをルーティングすることができます。

## <a name="next-steps"></a>次のステップ

- [仮想ネットワーク内でのプールの作成](batch-virtual-network.md)について詳細を説明します。
- [Batch アカウントでプライベート エンドポイントを使用](private-connectivity.md)する方法について説明します。
