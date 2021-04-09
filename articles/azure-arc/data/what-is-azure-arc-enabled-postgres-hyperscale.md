---
title: Azure Arc 対応 PostgreSQL Hyperscale とは
description: Azure Arc 対応 PostgreSQL Hyperscale とは
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 8edf540c3f67a3a8bee075569f0a2588cae18a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390013"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale とは

Azure Arc 対応 PostgreSQL Hyperscale は、Azure Arc 対応データ サービスの一部として使用できるデータベース サービスの 1 つです。 Azure Arc により、Kubernetes と任意のインフラストラクチャを使用して、Azure データ サービスをオンプレミス、エッジ、パブリック クラウドで実行できるようになります。 Azure Arc 対応データ サービスの価値提案は次の内容を示します。
- 常に最新
- エラスティック スケール
- セルフサービス プロビジョニング
- 統合された管理
- 切断されたシナリオのサポート

詳細については、次を参照してください。
- [Azure Arc 対応データ サービスとは](overview.md)
- [接続モードと要件](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>ソリューションを比較する

このセクションでは、Azure Arc 対応 PostgreSQL Hyperscale が Azure Database for PostgreSQL Hyperscale (Citus) とはどのように異なるかについて説明します。

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Azure Database for PostgreSQL Hyperscale (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database for PostgreSQL Hyperscale (Citus)":::

これは、Azure でのサービスとしてのデータベース (PaaS) として使用できる Postgres データベース エンジンのハイパースケール フォーム ファクターです。 ハイパースケール エクスペリエンスを可能にする Citus 拡張機能を備えています。 このフォーム ファクターでは、サービスは Microsoft データセンターで実行され、Microsoft によって運用されます。

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure Arc 対応 PostgreSQL Hyperscale":::

これは、Azure Arc 対応データ サービスで使用できる Postgres データベース エンジンのハイパースケール フォーム ファクターです。 これもまた、ハイパースケール エクスペリエンスを可能にする Citus 拡張機能を備えています。 このフォーム ファクターでは、システムをホストするインフラストラクチャを顧客が提供し、それらのシステムを運用します。

## <a name="next-steps"></a>次のステップ
- **実際に使ってみてください。** Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。 

- **独自のものを作成します。** 独自の Kubernetes クラスターを作成するには、こちらの手順に従います。 
   1. [クライアント ツールをインストールする](install-client-tools.md)
   2. [Azure Arc データ コントローラーを作成する](create-data-controller.md)
   3. [Azure Arc で Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc 対応データ サービスについて確認する](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Azure Arc について確認する](https://aka.ms/azurearc)
