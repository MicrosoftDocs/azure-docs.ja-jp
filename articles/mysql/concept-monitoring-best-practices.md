---
title: 監視のベスト プラクティス - Azure Database for MySQL
description: この記事では、Azure Database for MySQL を監視するためのベスト プラクティスについて説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96354887"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>Azure Database for MySQL を監視するためのベスト プラクティス - 単一サーバー

データベース操作を監視し、データ サイズの増加に伴ってパフォーマンスが低下しないようにするために使用できるベスト プラクティスについて説明します。 Microsoft は、プラットフォームに新しい機能を追加していく中で、このセクションで詳しく説明するベスト プラクティスを改善していきます。

## <a name="layout-of-the-current-monitoring-toolkit"></a>現在の監視ツールキットのレイアウト

Azure Database for MySQL には、簡単に使用状況を監視し、リソース (CPU、メモリ、I/O など) を追加または削除し、潜在的な問題のトラブルシューティングを行い、データベースのパフォーマンスを向上させるために使用できるツールと方法が用意されています。 定期的に[パフォーマンス メトリックを監視](concepts-monitoring.md#metrics)することで、さまざまな時間範囲の平均、最大、最小の値を確認できます。

メトリックのしきい値に対して[アラートを設定](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal)して、サーバーがこれらの制限に達した場合に通知を受け、適切な操作を実行できます。  

データベース サーバーを監視して、データベースに割り当てられているリソースでアプリケーション ワークロードを処理できることを確認します。 データベースがリソース制限に達している場合は、次のことを検討してください。
    * リソース消費量が上位のクエリを特定して最適化する。 
    * サービス レベルをアップグレードすることで、さらにリソースを追加する。

### <a name="cpu-utilization"></a>CPU 使用率
CPU 使用率を監視し、データベースが CPU リソースを使い果たしていないかどうかを確認します。 CPU 使用率が 90% 以上の場合は、仮想コアの数を増やしてコンピューティングをスケールアップするか、次の価格レベルにスケーリングする必要があります。  CPU をスケールアップまたはスケールダウンするときに、スループットまたはコンカレンシーが想定どおりであることを確認します。 

### <a name="memory"></a>メモリ 
データベース サーバーで使用可能なメモリの量は[仮想コアの数](concepts-pricing-tiers.md)に比例します。 メモリがワークロードに対して十分であることを確認します。 アプリケーションのロード テストを実行して、メモリが読み取りおよび書き込み操作のために十分であることを確認します。 データベースのメモリ消費量が、定義したしきい値を超えて頻繁に増大する場合は、仮想コアを増やすか、パフォーマンス レベルを上げることでインスタンスをアップグレードする必要があることを示しています。 [クエリ ストア](concepts-query-store.md)、[クエリ パフォーマンスの推奨事項](concepts-performance-recommendations.md)を使用して、実行時間が最も長いクエリを特定します。 最適化する機会を探します。 

### <a name="storage"></a>ストレージ 
MySQL サーバー用にプロビジョニングされた[ストレージの容量](howto-create-manage-server-portal.md#scale-compute-and-storage)によって、サーバーの IOPS が決まります。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、サーバー ログ、バックアップ スナップショットが格納されます。 消費されるディスク領域が、プロビジョニングされたディスク領域の合計の 85% を常に超えないようにしてください。 それが当てはまる場合は、データベース サーバーからデータを削除するかアーカイブして、領域を解放する必要があります。 

### <a name="network-traffic"></a>ネットワーク トラフィック 

**ネットワーク受信スループット、ネットワーク送信スループット** – MySQL インスタンスとの間のネットワーク トラフィックの速度 (MB/秒)。 スループットが予想よりも低い場合は、サーバーのスループット要件を評価し、トラフィックを常に監視する必要があります。 

### <a name="database-connections"></a>データベース接続 
**データベース接続** – Azure Database for MySQL に接続されるクライアント セッションの数は、[選択した SKU サイズの接続制限](concepts-server-parameters.md#max_connections)に合わせる必要があります。 


## <a name="next-steps"></a>次の手順

- [Azure Database for MySQL のパフォーマンスのベスト プラクティス](concept-performance-best-practices.md)
- [Azure Database for MySQL を使用したサーバー操作のベスト プラクティス](concept-operation-excellence-best-practices.md)
