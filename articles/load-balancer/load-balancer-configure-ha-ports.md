---
title: Azure Load Balancer 用の高可用性ポートを構成する
titlesuffix: Azure Load Balancer
description: すべてのポートで内部トラフィックを負荷分散するために高可用性ポートを使用する方法について説明します。
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: c0cf1eb62c8e01988c9014478ff72816e45ea64c
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275615"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>内部ロード バランサー用の高可用性ポートの構成

この記事では、内部ロード バランサーへの高可用性ポートのデプロイ例について説明します。 ネットワーク仮想アプライアンス (NVA) に固有の構成の詳細については、対応するプロバイダーの Web サイトを参照してください。

>[!NOTE]
>Azure Load Balancer では、Basic と Standard の 2 種類がサポートされています。 この記事では、Standard Load Balancer について説明します。 Basic Load Balancer の詳細については、[Load Balancer の概要](load-balancer-overview.md)に関するページを参照してください。

下図は、この記事で説明するデプロイ例の次の構成を示しています。

- NVA は、高可用性ポート構成の背後にある内部ロード バランサーのバックエンド プールにデプロイされます。 
- DMZ サブネットに適用されたユーザー定義ルート (UDR) は、次ホップを内部ロード バランサーの仮想 IP にすることで、すべてのトラフィックを NVA にルーティングします。 
- 内部ロード バランサーは、ロード バランサー アルゴリズムに従ってアクティブな NVA のいずれかにトラフィックを分散します。
- NVA はトラフィックを処理し、バックエンド サブネット内の元の送信先に転送します。
- 対応する UDR がバックエンド サブネットで構成されている場合は、リターン パスが同じルートをたどる可能性があります。 

![高可用性ポートのデプロイ例](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>高可用性ポートの構成

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

高可用性ポートを構成するには、バックエンド プール内の NVA で内部ロード バランサーを設定します。 NVA の正常性を検出するための対応するロード バランサー正常性プローブ構成と、高可用性ポートのロード バランサー規則を設定します。 ロード バランサー関連の全般的な構成については、[概要](load-balancer-get-started-ilb-arm-portal.md)に関する記事をご覧ください。 この記事では、高可用性ポートの構成を取り上げます。

構成では、基本的にフロントエンド ポートとバックエンド ポートの値を **0** に設定する必要があります。 プロトコル値は **All** に設定します。 この記事では、Azure portal、PowerShell、Azure CLI を使用して高可用性ポートを構成する方法について説明します。

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Azure Portal を使用して高可用性ポートのロード バランサー規則を構成する

Azure Portal を使用して高可用性ポートを構成するには、 **[HA ポート]** チェック ボックスをオンにします。 このオプションを選択すると、関連するポートとプロトコルの構成が自動的に設定されます。 

![Azure Portal を使用した高可用性ポートの構成](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Resource Manager テンプレートを使用して高可用性ポートの負荷分散規則を構成する

Load Balancer リソース内の 2017-08-01 API バージョンの Microsoft.Network/loadBalancers を使用して、高可用性ポートを構成できます。 次の JSON スニペットは、REST API を使用した高可用性ポートのロード バランサー構成の変更を示しています。

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>PowerShell を使用して高可用性ポートのロード バランサー規則を構成する

PowerShell で、次のコマンドを使用して高可用性ポートのロード バランサー規則を作成し、内部ロード バランサーを作成します。

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Azure CLI を使用して高可用性ポートのロード バランサー規則を構成する

[内部ロード バランサー セットの作成](load-balancer-get-started-ilb-arm-cli.md)に関する記事の手順 4. で、次のコマンドを使用して高可用性ポートのロード バランサー規則を作成します。

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>次の手順

[高可用性ポート](load-balancer-ha-ports-overview.md)の詳細を確認します。