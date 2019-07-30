---
title: Azure Cosmos DB アカウントの操作
description: この記事では、Azure Cosmos DB アカウントの作成および使用方法について説明します。
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 617d19b0dd9da926eb49170c1566febc6f6280ba
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467813"
---
# <a name="work-with-azure-cosmos-account"></a>Azure Cosmos アカウントの使用

Azure Cosmos DB はフルマネージドのサービスとしてのプラットフォーム (PaaS) です。 Azure Cosmos DB を使い始めるには、まず Azure サブスクリプションで Azure Cosmos アカウントを作成する必要があります。 Azure Cosmos アカウントには固有の DNS 名が含まれており、Azure portal、Azure CLI、または別の言語固有の SDK を使用してアカウントを管理できます。 詳細については、[Azure Cosmos アカウントの管理方法](how-to-manage-database-account.md)に関するページを参照してください。

Azure Cosmos アカウントは、グローバルな分散と高可用性の基本単位です。 データとスループットを複数の Azure リージョンにグローバルに分散するために、いつでも Azure リージョンを Azure Cosmos アカウントに追加および削除できます。 Azure Cosmos アカウントは、単一または複数の書き込みリージョンを持つように構成できます。 詳細については、[Azure Cosmos アカウントに Azure リージョンを追加および削除する方法](how-to-manage-database-account.md)に関するページを参照してください。 Azure Cosmos アカウントの[既定の一貫性](consistency-levels.md)レベルを構成できます。 Azure Cosmos DB は、スループット、99 パーセンタイルの待ち時間、一貫性、および高可用性を含む包括的な SLA を提供します。 詳細については、「[Azure Cosmos DB の SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)」を参照してください。

Azure Cosmos アカウント内のすべてのデータに対するアクセスを安全に管理するには、アカウントに関連付けられた[マスター キー](secure-access-to-data.md)を使用できます。 データへのアクセスをセキュリティでさらに保護するには、Azure Cosmos アカウントに対して [VNET サービス エンドポイント](vnet-service-endpoint.md)と [IP ファイアウォール](firewall-support.md)を構成します。 

## <a name="elements-in-an-azure-cosmos-account"></a>Azure Cosmos アカウントの要素

Azure Cosmos DB コンテナーはスケーラビリティの基本単位です。 コンテナー上に持つことができるプロビジョニング スループット (RU/秒) とストレージは事実上無制限です。 Azure Cosmos DB では、プロビジョニング スループットとストレージを柔軟にスケーリングするために、指定した論理パーティション キーを使用してコンテナーを透過的に分割します。 詳細については、[Azure Cosmos コンテナーとアイテムの使用](databases-containers-items.md)に関するページを参照してください。

現在、1 つの Azure サブスクリプションで Azure Cosmos アカウントを最大 100 個作成することができます。 1 つの Azure Cosmos アカウントで、事実上無制限のデータ量とプロビジョニング スループットを管理できます。 データとプロビジョニング スループットを管理するには、自分のアカウントで 1 つまたは複数の Azure Cosmos データベースを作成し、そのデータベース内に 1 つまたは複数のコンテナーを作成することができます。 次の画像は、Azure Cosmos アカウントの要素の階層を示しています。

![Azure Cosmos アカウントの階層](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>次の手順

Azure Cosmos アカウントの管理方法とその他の概念を確認します。

* [Azure Cosmos アカウントを管理する方法](how-to-manage-database-account.md)
* [グローバル配信](distribute-data-globally.md)
* [一貫性レベル](consistency-levels.md)
* [Azure Cosmos のコンテナーおよび項目の操作](databases-containers-items.md)
* [Azure Cosmos アカウントの VNET サービス エンドポイント](vnet-service-endpoint.md)
* [Azure Cosmos アカウントの IP ファイアウォール](firewall-support.md)
* [Azure Cosmos アカウントに Azure リージョンを追加および削除する方法](how-to-manage-database-account.md)
* [Azure Cosmos DB の SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
