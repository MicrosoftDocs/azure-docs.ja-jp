---
title: 可用性ゾーン間でプールを作成する
description: 障害からの保護に役立つように、ゾーン ポリシーを使用して Batch プールを作成する方法について説明します。
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 98109e1b74106bc636eaa715575e4b30ab29f9e2
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056991"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>可用性ゾーン間で Azure Batch プールを作成する

[可用性ゾーン](https://azure.microsoft.com/global-infrastructure/availability-zones/)をサポートする Azure リージョンには少なくとも 3 つの異なるゾーンがあり、それぞれが独自の独立した電源、ネットワーク、冷却システムを備えています。 仮想マシン構成を使用して Azure Batch プールを作成する場合、可用性ゾーン間で Batch プールをプロビジョニングすることを選択できます。 このゾーン ポリシーを使用してプールを作成すると、Azure データセンター レベルの障害から Batch コンピューティング ノードを保護するのに役立ちます。

たとえば、3 つの可用性ゾーンをサポートする Azure リージョンに、ゾーン ポリシーを使用してプールを作成できます。 1 つの可用性ゾーン内の Azure データセンターでインフラストラクチャの障害が発生しても、Batch プールでは、他の 2 つの可用性ゾーン内に正常なノードがまだあります。そのため、タスクのスケジュールのためにプールを引き続き使用できます。

## <a name="regional-support-and-other-requirements"></a>リージョンのサポートとその他の要件

Batch では、可用性ゾーンのサポートで Azure とのパリティが維持されます。 ゾーン オプションを使用するには、[サポートされている Azure リージョン](../availability-zones/az-region.md)でプールを作成する必要があります。

Batch プールが可用性ゾーンに対して割り当てられるようにするために、プールが作成される Azure リージョンでは、複数のゾーンで要求した VM SKU をサポートする必要があります。 これを検証するには、[リソース SKU リスト API](/rest/api/compute/resourceskus/list) を呼び出し、[resourceSku](/rest/api/compute/resourceskus/list#resourcesku) の **locationInfo** フィールドを確認します。 要求した VM SKU に対して、複数のゾーンがサポートされていることを確認します。

[ユーザー サブスクリプション モードの Batch アカウント](accounts.md#batch-accounts)の場合、プールを作成するサブスクリプションに、要求した VM SKU に対するゾーン オファーの制限がないことを確認します。 これを確認するには、[リソース SKU リスト API](/rest/api/compute/resourceskus/list) を呼び出し、[ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions) を確認します。 ゾーン制限が存在する場合は、ゾーン制限を解除するために[サポート チケット](../azure-portal/supportability/sku-series-unavailable.md)を送信できます。

また、ノード間通信が有効になっていて、[InfiniBand をサポートする VM SKU](../virtual-machines/workloads/hpc/enable-infiniband.md) を使用する場合は、ゾーン ポリシーを使用してプールを作成できないので注意してください。

## <a name="create-a-batch-pool-across-availability-zones"></a>可用性ゾーン間で Batch プールを作成する

次の例は、可用性ゾーン間で Batch プールを作成する方法を示しています。

> [!NOTE]
> ゾーン ポリシーを使用してプールを作成すると、Batch サービスは、選択したリージョン内のすべての可用性ゾーンに対してプールを割り当てようとします。ゾーンに対して特定の割り当てを指定することはできません。

### <a name="batch-management-client-net-sdk"></a>Batch Management Client .NET SDK

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>Batch REST API

REST API URL

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

要求本文

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>次のステップ

- [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
- [Azure 仮想ネットワークのサブネットでのプールの作成](batch-virtual-network.md)について学習します。
- [パブリック IP アドレスのない Azure Batch プールの作成](./batch-pool-no-public-ip-address.md)について学習します。

