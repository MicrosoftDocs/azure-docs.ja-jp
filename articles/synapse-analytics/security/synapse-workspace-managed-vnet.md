---
title: マネージド仮想ネットワーク
description: Azure Synapse Analytics のマネージド仮想ネットワークについて説明する記事
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 06b535b25df19e5062d16184f4469d9e9253b9c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87042605"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Azure Synapse Analytics のマネージド仮想ネットワーク (プレビュー)

この記事では、Azure Synapse Analytics のマネージド仮想ネットワークについて説明します。

## <a name="managed-workspace-virtual-network"></a>マネージド ワークスペース仮想ネットワーク

Azure Synapse ワークスペースを作成するときに、そのワークスペースを Microsoft Azure Virtual Network に関連付けることを選択できます。 ワークスペースに関連付けられた仮想ネットワークは、Azure Synapse によって管理されます。 この仮想ネットワークは、"*マネージド ワークスペース仮想ネットワーク*" と呼ばれます。

マネージド ワークスペース仮想ネットワークによってもたらされる価値として、次の 4 つの点が挙げられます。

- マネージド ワークスペース仮想ネットワークを使用すると、仮想ネットワークの管理負荷を Azure Synapse にオフロードすることができます。
- Azure Synapse 管理トラフィックが仮想ネットワークに入るのを許可するインバウンド NSG ルールを、独自の仮想ネットワークに対して構成する必要はありません。 そのような NSG ルールの構成ミスは、顧客へのサービス中断を招きます。
- Spark クラスター用のサブネットをピーク負荷に基づいて自分で作成する必要がありません。
- マネージド ワークスペース仮想ネットワークは、マネージド プライベート エンドポイントと共に、データ流出への保護対策となります。 マネージド プライベート エンドポイントは、マネージド ワークスペース仮想ネットワークが関連付けられているワークスペースにのみ作成できます。

マネージド ワークスペース仮想ネットワークが関連付けられたワークスペースを作成することによって、そのワークスペースが他のワークスペースから分離されたネットワークであることが保証されます。 Azure Synapse のワークスペースには、さまざまな分析機能が用意されています。データ統合、Apache Spark、SQL プール、SQL オンデマンド。

マネージド ワークスペース仮想ネットワークが関連付けられているワークスペースには、データ統合および Spark リソースがデプロイされます。 さらに、マネージド ワークスペース仮想ネットワークでは、Spark のアクティビティに関して、ユーザーレベルの分離性が確保されます。各 Spark クラスターが独自のサブネットに配置されるためです。

SQL プールと SQL オンデマンドはマルチテナントの機能であるため、マネージド ワークスペース仮想ネットワークの外に存在します。 ワークスペース内の SQL プールと SQL オンデマンドへの通信には、Azure Private Link が使用されます。 このプライベート リンクは、マネージド ワークスペース仮想ネットワークが関連付けられたワークスペースを作成すると自動的に作成されます。

>[!IMPORTANT]
>このワークスペースの構成は、ワークスペースの作成後に変更することはできません。 たとえば、マネージド ワークスペース仮想ネットワークが関連付けられていないワークスペースを再構成して、仮想ネットワークを関連付けることはできません。 同様に、マネージド ワークスペース仮想ネットワークが関連付けられているワークスペースを再構成して、仮想ネットワークの関連付けを解除することもできません。

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>マネージド ワークスペース仮想ネットワークを使用して Azure Synapse ワークスペースを作成する

まだ登録していない場合、ネットワーク リソース プロバイダーを登録します。 リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。 [登録](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)時、リソース プロバイダーの一覧から *Microsoft.Network* を選択します。

マネージド ワークスペース仮想ネットワークが関連付けられた Azure Synapse ワークスペースを作成するには、Azure portal の **[セキュリティとネットワーク]** タブを選択し、 **[マネージド仮想ネットワークの有効化]** チェック ボックスをオンにします。

チェック ボックスをオフのままにした場合、ワークスペースには仮想ネットワークが関連付けられません。

>[!IMPORTANT]
>プライベート リンクは、マネージド ワークスペース仮想ネットワークがあるワークスペースでしか使用できません。

![マネージド ワークスペース仮想ネットワークを有効にする](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>マネージド プライベート エンドポイントを経由した場合を除き、マネージド ワークスペース仮想ネットワークからのアウトバウンド トラフィックは将来すべてブロックされます。 Azure のデータ ソースとの接続にはすべて、ワークスペースに外部となるマネージド プライベート エンドポイントを作成することをお勧めします。 

Azure Synapse ワークスペースがマネージド ワークスペース仮想ネットワークに関連付けられているかどうかは、Azure portal の **[概要]** を選択することで確認できます。

![Azure portal におけるワークスペースの概要](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>次のステップ

[Azure Synapse ワークスペース](../quickstart-create-workspace.md)を作成する

[マネージド プライベート エンドポイント](./synapse-workspace-managed-private-endpoints.md)について理解を深める

[データ ソースへのマネージド プライベート エンドポイントを作成する](./how-to-create-managed-private-endpoints.md)
