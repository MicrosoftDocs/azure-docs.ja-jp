---
title: 特定のパブリック IP アドレスのプールを作成する
description: 独自のパブリック IP アドレスを使用する Batch プールを作成する方法について説明します。
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: e822311718847e173763847d503335f71457308b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91849330"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>特定のパブリック IP アドレスの Azure Batch プールを作成する

Azure Batch プールを作成すると、指定した [Azure 仮想ネットワーク (VNet) のサブネットでプールをプロビジョニング](batch-virtual-network.md)できます。 Batch プール内の仮想マシンには、Batch によって作成されるパブリック IP アドレスを使用してアクセスします。 これらのパブリック IP アドレスは、プールの有効期間中に変更される可能性があります。つまり、IP アドレスが更新されない場合、ネットワーク設定が古くなる可能性があります。

プール内の仮想マシンで使用する静的パブリック IP アドレスの一覧を作成できます。 これにより、パブリック IP アドレスの一覧を制御し、予期せず変更されないようにすることができます。 このことは、特定の IP アドレスへのアクセスを制限する外部サービス (データベースなど) を使用している場合に特に便利です。

パブリック IP アドレスのないプールを作成する方法については、「[パブリック IP アドレスのない Azure Batch プールを作成する](./batch-pool-no-public-ip-address.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- **[認証]** : パブリック IP アドレスを使用するには、Batch クライアント API で [Azure Active Directory (AD) 認証](batch-aad-auth.md)を使用する必要があります。

- **Azure VNet**。 プールと IP アドレスを作成するのと同じ Azure サブスクリプションからの[仮想ネットワーク](batch-virtual-network.md)を使用する必要があります。 Azure Resource Manager ベースの VNet のみが使用できます。 VNet が[一般的な要件](batch-virtual-network.md#vnet-requirements)をすべて満たしていることを確認してください。

- **少なくとも 1 つの Azure パブリック IP アドレス**。 1 つ以上のパブリック IP アドレスを作成するには、[Azure portal](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)、[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/network/public-ip#az-network-public-ip-create)、[Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress) のいずれかを使用することができます。 以下に示す要件に従ってください。

> [!NOTE]
> パブリック IP アドレスを含んでいるリソース グループには、Batch によって自動的に追加のネットワーク リソースが割り当てられます。 通常、専用のノード 100 台ごとに、1 つのネットワーク セキュリティ グループ (NSG) と 1 つのロード バランサーが Batch によって割り当てられます。 これらのリソースは、サブスクリプションのリソース クォータによって制限されます。 大規模なプールを使用する場合、これらの 1 つ以上のリソースについて、[クォータの引き上げの要求](batch-quota-limit.md#increase-a-quota)が必要になる場合があります。

## <a name="public-ip-address-requirements"></a>パブリック IP アドレス要件

パブリック IP アドレスを作成する場合は、次の要件に注意してください。

- パブリック IP アドレスが存在するサブスクリプションとリージョンは、プールの作成に使用する Batch アカウントと同じである必要があります。
- **IP アドレスの割り当て** は、**静的** に設定する必要があります。
- **SKU** は **Standard** に設定する必要があります。
- DNS 名を指定する必要があります。
- パブリック IP アドレスは、仮想マシン構成プールにのみ使用する必要があります。 他のリソースがこれらの IP アドレスを使用することはできません。使用した場合、プールに割り当てエラーが発生する可能性があります。
- セキュリティ ポリシーまたはリソース ロックによって、ユーザーのパブリック IP アドレスへのアクセスが制限されることはありません。
- プールに対して指定されているパブリック IP アドレスの数は、プールの対象となる VM の数に対応できる大きさである必要があります。 この値は、少なくともプールの  **targetDedicatedNodes**  と  **targetLowPriorityNodes**  プロパティの合計である必要があります。 IP アドレスが十分でない場合、プールによってコンピューティング ノードが部分的に割り当てられ、サイズ変更エラーが発生します。 Batch では、現時点で、100 台の VM ごとに 1 つのパブリック IP アドレスが使用されます。
- パブリック IP アドレスのバッファーが常に余分にあるようにします。 少なくとも 1 つのパブリック IP アドレスか、プールに追加するパブリック IP アドレスの合計の約 10% のうち、大きい方を追加することをお勧めします。 この追加のバッファーにより、スケールダウン時に Batch の内部最適化に役立つだけでなく、スケールアップまたはスケールダウンの失敗後に迅速にスケールアップすることができます。
- プールが作成されると、プールによって使用されるパブリック IP アドレスの一覧を追加または変更することはできません。 リストを変更する必要がある場合は、プールを削除してから再作成する必要があります。

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>パブリック IP アドレスで Batch プールを作成する

次の例では、[Azure Batch Service REST API](/rest/api/batchservice/pool/add) を使用して、パブリック IP アドレスを使用するプールを作成する方法を示します。

### <a name="batch-service-rest-api"></a>Batch Service REST API

REST API URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

要求本文

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>次のステップ

- [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
- [Azure 仮想ネットワークのサブネットでのプールの作成](batch-virtual-network.md)について学習します。
- [パブリック IP アドレスのない Azure Batch プールの作成](./batch-pool-no-public-ip-address.md)について学習します。

