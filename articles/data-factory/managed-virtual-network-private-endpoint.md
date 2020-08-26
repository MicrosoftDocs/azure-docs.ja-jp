---
title: マネージド仮想ネットワークとマネージド プライベート エンドポイント
description: Azure Data Factory におけるマネージド仮想ネットワークとマネージド プライベート エンドポイントについて説明します。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2020
ms.openlocfilehash: 14a3a76ef4fefb7a33b272b846e1f1cb66644c3e
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225684"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory マネージド仮想ネットワーク (プレビュー)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory におけるマネージド仮想ネットワークとマネージド プライベート エンドポイントについて説明します。


## <a name="managed-virtual-network"></a>マネージド仮想ネットワーク

Azure Data Factory マネージド仮想ネットワーク (VNET) 内に Azure Integration Runtime (IR) を作成すると、マネージド仮想ネットワークを使用して統合ランタイムがプロビジョニングされ、プライベート エンドポイントを利用して、サポートされているデータ ストアに安全に接続します。 

マネージド仮想ネットワーク内に Azure IR を作成することで、データ統合プロセスの分離と安全が確保されます。 

マネージド仮想ネットワークを使用する利点は次のとおりです。

- マネージド仮想ネットワークを使用すると、仮想ネットワークを管理する負担を Azure Data Factory にオフロードできます。 Azure Integration Runtime のサブネットは最終的に Virtual Network の多くのプライベート IP を使用する可能性があり、事前のネットワーク インフラストラクチャ計画が必要になりますが、このサブネットを作成する必要がありません。 
- データ統合を安全に行うために、Azure のネットワークに関する深い知識は必要ありません。 それよりむしろ、データ エンジニアにとっては、セキュリティで保護された ETL の使用開始が非常に簡単になっています。 
- マネージド仮想ネットワークは、マネージド プライベート エンドポイントと共に、データ流出を防止します。 

> [!IMPORTANT]
>現時点では、マネージド VNet は Azure Data Factory リージョンと同じリージョンでのみサポートされています。
 

![ADF マネージド仮想ネットワークのアーキテクチャ](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>マネージド プライベート エンドポイント

マネージド プライベート エンドポイントは、Azure Data Factory マネージド仮想ネットワークに作成されるプライベート エンドポイントで、Azure リソースへのプライベート リンクを確立します。 これらのプライベート エンドポイントは、Azure Data Factory によって自動的に管理されます。 

![新しいマネージド プライベート エンドポイント](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory では、プライベート リンクがサポートされます。 プライベート リンクを使用すると、Azure (PaaS) サービス (Azure Storage、Azure Cosmos DB、Azure Synapse Analytics (以前の Azure SQL Data Warehouse) など) にアクセスできます。

プライベート リンクを使用すると、データ ストアとマネージド仮想ネットワーク間のトラフィックは、全面的に Microsoft のバックボーンネットワークを介して転送されます。 プライベート リンクによって、データ流出のリスクから身を守ることができます。 リソースへのプライベート リンクは、プライベート エンドポイントを作成することによって確立します。

プライベート エンドポイントでは、マネージド仮想ネットワークでプライベート IP アドレスを使用して、効率的にサービスを取り込みます。 プライベート エンドポイントは、サービス全体にではなく Azure 内の特定のリソースにマップされます。 顧客は、その組織で承認されている特定のリソースに接続を制限することができます。 [プライベート リンクとプライベート エンドポイント](https://docs.microsoft.com/azure/private-link/)についての詳しい情報をご覧ください。

> [!NOTE]
> Azure のデータ ソースとの接続にはすべて、マネージド プライベート エンドポイントを作成することをお勧めします。 
 
> [!WARNING]
> PaaS データ ストア (BLOB、ADLS Gen2、SQL DW) に対してプライベート エンドポイントが既に作成されており、すべてのネットワークからのアクセスが許可されている場合でも、ADF からはマネージド プライベート エンドポイントを使用してのみアクセスできます。 このようなシナリオでは、必ずプライベート エンドポイントを作成してください。 

Azure Data Factory にマネージド プライベート エンドポイントを作成すると、プライベート エンドポイント接続は "保留" 状態で作成されます。 承認ワークフローが開始されます。 接続を承認または拒否する役割は、プライベート リンク リソースの所有者が担います。

![マネージド プライベート エンドポイント](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

所有者が接続を承認した場合、プライベート リンクが確立されます。 それ以外の場合、プライベート リンクは確立されません。 どちらの場合も、マネージド プライベート エンドポイントは接続の状態で更新されます。

![マネージド プライベート エンドポイントを承認する](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

承認済み状態のマネージド プライベート エンドポイントのみが、指定されたプライベート リンク リソースにトラフィックを送信できます。

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題
### <a name="supported-data-sources"></a>サポートされるデータ ソース
次のデータ ソースでは、ADF マネージド仮想ネットワークからのプライベート リンクを介した接続がサポートされています。
- Azure Blob Storage
- Azure Table Storage
- Azure Files
- Azure Data Lake Gen2
- Azure SQL Database (Azure SQL Managed Instance を含まない)
- Azure Synapse Analytics (旧称 Azure SQL Data Warehouse)
- Azure CosmosDB SQL
- Azure Key Vault
- Azure Private Link サービス

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>ADF マネージド仮想ネットワークからのパブリック エンドポイントを介した送信方向の通信
- 送信方向の通信用には、ポート 443 のみが開かれています。
- Azure Storage および Azure Data Lake Gen2 では、ADF マネージド仮想ネットワークからパブリック エンドポイント経由で接続することはサポートされていません。

## <a name="next-steps"></a>次のステップ

- チュートリアル:[マネージド仮想ネットワークとプライベート エンドポイントを使用してコピー パイプラインを構築する](tutorial-copy-data-portal-private.md) 
- チュートリアル:[マネージド仮想ネットワークとプライベート エンドポイントを使用してマッピング データフロー パイプラインを構築する](tutorial-data-flow-private.md)
