---
title: マネージド プライベート エンドポイント
description: Azure Synapse Analytics のマネージド プライベート エンドポイントについて説明する記事
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/12/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 65794c695fa4b36586b23a308845b1f12a20b7cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98569948"
---
# <a name="synapse-managed-private-endpoints"></a>Synapse マネージド プライベート エンドポイント

この記事では、Azure Synapse Analytics のマネージド プライベート エンドポイントについて説明します。

## <a name="managed-private-endpoints"></a>マネージド プライベート エンドポイント

マネージド プライベート エンドポイントは、Azure Synapse ワークスペースに関連付けられているマネージド仮想ネットワークに作成されたプライベート エンドポイントです。 マネージド プライベート エンドポイントは、Azure リソースへのプライベート リンクを確立します。 これらのプライベート エンドポイントは、Azure Synapse によって自動的に管理されます。 Azure Synapse ワークスペースからマネージド プライベート エンドポイントを作成して、Azureサービス (Azure Storage や Azure Cosmos DB など)、および Azure でホストされる顧客またはパートナーのサービスにアクセスできます。

マネージド プライベート エンドポイントを使用すると、Azure Synapse ワークスペースと他の Azure リソース間のトラフィックはすべて、Microsoft のバックボーン ネットワークを経由します。 マネージド プライベート エンドポイントにより、データ流出から保護されます。 マネージド プライベート エンドポイントでは、マネージド仮想ネットワークのプライベート IP アドレスを使用して、Azure Synapse ワークスペースが通信している Azure サービスを仮想ネットワークに効果的に取り込みます。 マネージド プライベート エンドポイントは、サービス全体ではなく、Azure 内の特定のリソースにマップされます。 顧客は、その組織で承認されている特定のリソースに接続を制限することができます。 

[プライベート リンクとプライベート エンドポイント](../../private-link/index.yml)についての詳しい情報をご覧ください。

>[!IMPORTANT]
>マネージド プライベート エンドポイントは、マネージド ワークスペース仮想ネットワークを使用した Azure Synapse ワークスペースでのみサポートされます。

>[!NOTE]
>Azure Synapse ワークスペースを作成するときに、マネージド仮想ネットワークを関連付けることを選択できます。 マネージド仮想ネットワークをワークスペースに関連付けることにした場合は、ワークスペースからの送信トラフィックを承認済みのターゲットだけに制限することもできます。 これらのターゲットに対するマネージド プライベート エンドポイントを作成する必要があります。 


Azure Synapse のマネージド プライベート エンドポイントを作成すると、プライベート エンドポイント接続が "保留" 状態で作成されます。 承認ワークフローが開始されます。 接続を承認または拒否する役割は、プライベート リンク リソースの所有者が担います。 所有者が接続を承認した場合、プライベート リンクが確立されます。 ただし、所有者が接続を承認しない場合、プライベート リンクは確立されません。 どちらの場合も、マネージド プライベート エンドポイントは接続の状態で更新されます。 マネージド プライベート エンドポイントにリンクされているプライベート リンク リソースにトラフィックを送信するときに使用できるのは、承認済み状態のマネージド プライベート エンドポイントだけです。

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>専用 SQL プールとサーバーレス SQL プールのマネージド プライベート エンドポイント

専用 SQL プールとサーバーレス SQL プールは、Azure Synapse ワークスペースにおける分析機能です。 これらの機能では、[マネージド ワークスペース仮想ネットワーク](./synapse-workspace-managed-vnet.md)にはデプロイされていないマルチテナント インフラストラクチャが使用されます。

ワークスペースが作成されると、そのワークスペースに、専用 SQL プールとサーバーレス SQL プールに対する 2 つのマネージド プライベート エンドポイントが Azure Synapse によって作成されます。 

この 2 つのマネージド プライベート エンドポイントは、Synapse Studio に表示されます。 これらを Studio に表示するには、左側のナビゲーションで **[管理]** を選択し、 **[Managed private endpoints]\(マネージド プライベート エンドポイント\)** を選択します。

SQL プールを対象としたマネージド プライベート エンドポイントは *synapse-ws-sql--\<workspacename\>* で、サーバーレス SQL プールを対象にしたマネージド プライベート エンドポイントは *synapse-ws-sqlOnDemand--\<workspacename\>* です。

![専用 SQL プールとサーバーレス SQL プールのマネージド プライベート エンドポイント](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Azure Synapse ワークスペースを作成すると、この 2 つのマネージド プライベート エンドポイントが自動的に作成されます。 この 2 つのマネージド プライベート エンドポイントに対して課金されることはありません。

## <a name="next-steps"></a>次のステップ

詳細については、記事「[データ ソースへのマネージド プライベート エンドポイントを作成する](./how-to-create-managed-private-endpoints.md)」に進んでください。