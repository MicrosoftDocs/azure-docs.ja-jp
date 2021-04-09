---
title: Azure Managed Instance for Apache Cassandra の紹介
description: Azure Managed Instance for Apache Cassandra について説明します。 このサービスでは、Azure で Apache Cassandra のネイティブ オープンソース インスタンスのデプロイとスケーリングが管理されます。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 03/02/2021
ms.openlocfilehash: d99c62e7d88510c351f87d85b4f8c5c271767cb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747626"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra-preview"></a>Azure Managed Instance for Apache Cassandra とは (プレビュー)

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Managed Instance for Apache Cassandra サービスでは、マネージド オープンソースの Apache Cassandra データセンターに対して、デプロイとスケーリングの自動化された操作を提供します。 このサービスは、ハイブリッド シナリオの推進と継続的なメンテナンスの削減に役立ちます。 一般リリース時には、[Azure Cosmos DB Cassandra API](../cosmos-db/cassandra-introduction.md) による緊密な統合とデータ移動の機能が与えられます。

:::image type="content" source="./media/introduction/icon.gif" alt-text="Azure Managed Instance for Apache Cassandra は Apache Cassandra 向けの管理サービスです。" border="false":::

## <a name="key-benefits"></a>主な利点

### <a name="hybrid-deployments"></a>ハイブリッド デプロイ

仮想マシン スケール セットとして自動的にデプロイされる Apache Cassandra データセンターのマネージド インスタンスを新規または既存の Azure Virtual Network にこのサービスを利用して簡単に配置できます。 これらのデータセンターは、Azure または別のクラウド環境にある [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) 経由で、オンプレミスで実行されている既存の Apache Cassandra リングに追加できます。

- **簡略化デプロイ:** ハイブリッド接続の確立後、ゴシップ プロトコルによりデプロイが簡単になります。
- **ホストされたメトリック:** クラスター全体のアクティビティを監視するために、[Prometheus](https://prometheus.io/docs/introduction/overview/) でメトリックがホストされます。

### <a name="simplified-scaling"></a>簡素化スケーリング

マネージド インスタンスでは、データセンターでのノードのスケールアップとスケールダウンが完全に管理されます。 必要なノードの数を入力すると、スケーリング オーケストレーターによって Cassandra リング内でその操作が確立されます。

### <a name="managed-and-cost-effective"></a>マネージドかつ高いコスト効果

このサービスでは、次の一般的な Apache Cassandra タスクの管理操作が提供されます。

- クラスターのプロビジョニング
- データセンターのプロビジョニング
- データセンターのスケーリング
- データセンターの削除
- キースペースでの修復操作の開始
- データセンターの構成の変更
- バックアップの設定

価格モデルは柔軟であり、オンデマンドであり、インスタンスベースです。ライセンス料金はありません。 この価格モデルでは、特定のワークロード ニーズに合わせて調整できます。 必要なコアの数、VM SKU、メモリ サイズ、ディスク領域を選択します。

## <a name="next-steps"></a>次のステップ

次のいずれかのクイックスタートに取り組みます。

* [Azure portal からマネージド インスタンス クラスターを作成する](create-cluster-portal.md)
* [Azure Databricks でマネージド Apache Spark クラスターをデプロイする](deploy-cluster-databricks.md)
* [Azure CLI を利用して Azure Managed Instance for Apache Cassandra リソースを管理する](manage-resources-cli.md)
