---
title: Azure Batch プールでノード エンドポイントを構成する | Microsoft Docs
description: Azure Batch プールの計算ノードで SSH ポートまたは RDP ポートへのアクセスを構成する方法と無効にする方法。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: lahugh
ms.openlocfilehash: e6c7f2762a6742a1aff7a2c3aff977b5e3657349
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322468"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Azure Batch プールの計算ノードへのリモート アクセスを構成する/無効にする

Batch の既定では、ネットワークに接続している[ノード ユーザー](/rest/api/batchservice/computenode/adduser)は Batch プールの計算ノードに外部接続できます。 たとえば、リモート デスクトップ (RDP) を利用し、ポート 3389 で Windows プールの計算ノードに接続できます。 同様に、既定では、Secure Shell (SSH) を利用し、ポート 22 で Linux プールの計算ノードに接続できます。 

場合によっては、自分の環境で、これらの既定の外部アクセス設定を制限したり、無効にしたりする必要があります。 Batch API を利用して [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) プロパティを設定することで、これらの設定を変更できます。 

## <a name="about-the-pool-endpoint-configuration"></a>プール エンドポイントの構成について
エンドポイントは、フロントエンド ポートの 1 つまたは複数の[ネットワーク アドレス変換 (NAT) プール](/rest/api/batchservice/pool/add#inboundnatpool)から構成されます。 (計算ノードの Batch プールと NAT プールを混同しないでください。)プールの計算ノードの既定の接続設定をオーバーライドするように各 NAT プールを設定します。 

各 NAT プール構成には、1 つまたは複数の[ネットワーク セキュリティ グループ (NSG) ルール](/rest/api/batchservice/pool/add#networksecuritygrouprule)が含まれています。 各 NSG ルールによって、エンドポイントへの特定のネットワーク トラフィックが許可されるか、拒否されます。 すべてのトラフィック、[サービス タグ](../virtual-network/security-overview.md#service-tags) ("Internet" など) で識別されるトラフィック、特定の IP アドレスやサブネットから届くトラフィックを許可または拒否するように選択できます。

### <a name="considerations"></a>考慮事項
* プール エンドポイント構成は、プールの[ネットワーク構成](/rest/api/batchservice/pool/add#networkconfiguration)の一部です。 ネットワーク構成には任意で、[Azure 仮想ネットワーク](batch-virtual-network.md)にプールを参加させる設定を含めることができます。 仮想ネットワークにプールを設定した場合、仮想ネットワークのアドレス設定を使用する NSG ルールを作成できます。
* NAT プールを構成するとき、複数の NSG ルールを構成できます。 ルールは優先順位に従ってチェックされます。 いずれかのルールが適用されると、それ以上はルールの一致テストが行われなくなります。


## <a name="example-deny-all-rdp-traffic"></a>例:すべての RDP トラフィックを拒否する

次の C# コードの抜粋からは、Windows プールの計算ノードに RDP エンドポイントを構成し、すべてのネットワーク トラフィックを拒否する方法を確認できます。 このエンドポイントでは、範囲 *60000 - 60099* のポートのフロントエンド プールが使用されます。 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>例:インターネットからのすべての SSH トラフィックを拒否する

次の Python コードの抜粋からは、Linux プールの計算ノードに SSH エンドポイントを構成し、すべてのインターネット トラフィックを拒否する方法を確認できます。 このエンドポイントでは、範囲 *4000 - 4100* のポートのフロントエンド プールが使用されます。 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                    source_address_prefix='Internet'
                )
            ]
        )
        ]
    )
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>例:特定の IP アドレスからの RDP トラフィックを許可する

次の C# コードの抜粋からは、Windows プールの計算ノードに RDP エンドポイントを構成し、IP アドレス *198.51.100.7* からのみ RDP アクセスを許可する方法を確認できます。 2 つ目の NSG ルールによって、この IP アドレスに一致しないトラフィックが拒否されます。

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>例:特定のサブネットからの SSH トラフィックを許可する

次の Python コードの抜粋からは、Linux プールの計算ノードに SSH エンドポイントを構成し、サブネット *192.168.1.0/24* からのみアクセスを許可する方法を確認できます。 2 つ目の NSG ルールによって、このサブネットに一致しないトラフィックが拒否されます。

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access='allow',
                    source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                    priority=175,
                    access='deny',
                    source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>次の手順

- Azure の NSG ルールに関する詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](../virtual-network/security-overview.md)」をご覧ください。

- Batch の詳細な概要については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md)」を参照してください。

