---
title: Azure Managed Instance for Apache Cassandra と Azure Cosmos DB Cassandra API の違い
description: Azure Managed Instance for Apache Cassandra と Azure Cosmos DB の Cassandra API との違いについて説明します。 また、それぞれのサービスの利点と選択すべき場面についても説明します。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 3050dda4b3c0e1a05d751a4f5969bba69ad0506d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747687"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-preview-and-azure-cosmos-db-cassandra-api"></a>Azure Managed Instance for Apache Cassandra (プレビュー) と Azure Cosmos DB Cassandra API の違い 

この記事では、Azure Managed Instance for Apache Cassandra と Azure Cosmos DB の Cassandra API との違いについて説明します。 この記事では、この 2 つのサービスからの選び方、また、どのような場合に独自の Apache Cassandra 環境をホストすべきかについての推奨事項を紹介しています。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="key-differences"></a>主な相違点

Azure Managed Instance for Apache Cassandra は、Azure におけるオープンソースの Apache Cassandra インスタンスのデプロイ操作やスケーリング操作のほか、ノードの正常性を維持するための操作を自動化します。 また、既にあるオンプレミスまたはクラウドのセルフホステッド Apache Cassandra クラスターの容量をスケールアウトする機能も備えています。 スケールアウトするには、マネージド Cassandra データセンターを既存のクラスター リングに追加します。

Azure Cosmos DB の [Cassandra API](../cosmos-db/cassandra-introduction.md) は、Microsoft が提供するグローバルに分散されたクラウドネイティブ データベース サービス、[Azure Cosmos DB](../cosmos-db/index.yml) 上の互換性レイヤーです。 Azure でこれらのサービスを組み合わせることで、複雑なハイブリッド クラウド環境の Apache Cassandra のユーザーに、隙のない選択肢が提供されています。

## <a name="how-to-choose"></a>選び方

次の表は、それぞれのデプロイ方法が適している一般的なシナリオやワークロード要件、目標を示しています。

| |オンプレミスまたは Azure におけるセルフホステッド Apache Cassandra | Azure Managed Instance for Apache Cassandra | Azure Cosmos DB の Cassandra API |
|---------|---------|---------|---------|
|**デプロイの種類**| カスタムのパッチまたはスニッチを使用して、高度にカスタマイズされた Apache Cassandra のデプロイがある。 | カスタム コードを一切使用していない標準的なオープンソースの Apache Cassandra デプロイがある。 | 下層は Apache Cassandra でなくても、[ワイヤ プロトコル](../cosmos-db/cassandra-support.md) レベルですべてのオープンソースのクライアント ドライバーに準拠しているプラットフォームであれば問題ない。 |
| **運用上のオーバーヘッド**| クラスターのデプロイ、構成、保守を行うことができる Cassandra エキスパートが既にいる。  | Apache Cassandra ノードの正常性に関する運用上のオーバーヘッドは削減したいが、プラットフォーム レベルの構成 (レプリケーションや一貫性など) に対する制御手段は確保しておきたい。 | クラウドにおけるフル マネージドの PaaS (サービスとしてのプラットフォーム) データベースを使用して運用上のオーバーヘッドを解消したい。 |
| **オペレーティング システムの要件**| 仮想マシンのオペレーティング システムのカスタム イメージまたはゴールデン イメージを保守するという要件がある。 | バニラ イメージを使用してもよいが、SKU、メモリ、ディスク、IOPS に対する制御手段は確保しておきたい。 | 容量プロビジョニングを単純化し、Azure Cosmos DB の[要求ユニット](../cosmos-db/request-units.md)のように、スループットと一対一の関係で結びついた単一の正規化メトリックとして表したい。 |
| **価格モデル**| Datastax ツールなどの管理ソフトウェアを使用したい。ライセンス コストも問題ない。 | 純粋なオープンソースのライセンスと VM インスタンスベースの価格の方が望ましい。 | [自動スケーリング](../cosmos-db/manage-scale-cassandra.md#use-autoscale)と[サーバーレス](../cosmos-db/serverless.md)のプランを含む、クラウドネイティブの価格を使用したい。 |
| **Analytics**| 分析パイプラインのプロビジョニングに対するフル コントロールが、その構築と維持のオーバーヘッドに関係なく必要。 | Azure Databricks など、クラウドベースの分析サービスを使用したい。 | [Azure Synapse Link for Cosmos DB](../cosmos-db/synapse-link.md) と共にプラットフォームに組み込まれた凖リアルタイムのハイブリッド トランザクショナル分析が必要。 |
| **ワークロード パターン**| ワークロードの状態はかなり安定しているため、クラスターのノードを頻繁にスケーリングする必要はない。 | ワークロードが不安定なため、データ センターのノードのスケールアップとスケールダウンまたはデータ センターの追加と削除を容易に行える必要がある。 | ワークロードは不安定であることが多いため、スケールアップとスケールダウンをすばやく、かつきわめて大規模に行える必要がある。 |
| **SLA**| 一貫性、スループット、可用性、ディザスター リカバリーに関する SLA を維持するプロセスに不安がない。 | 一貫性、スループット、可用性に関する SLA を維持するプロセスには不安がないものの、バックアップに関しては支援が必要。 | 一貫性、スループット、可用性、ディザスター リカバリーに関して全面的かつ包括的な SLA が必要。 |

## <a name="next-steps"></a>次のステップ

次のいずれかのクイックスタートに取り組みます。

* [Azure portal からマネージド インスタンス クラスターを作成する](create-cluster-portal.md)