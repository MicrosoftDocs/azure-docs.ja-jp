---
title: Azure Advisor を使用して Azure アプリケーションのパフォーマンスを向上させる | Microsoft Docs
description: Advisor を使用して、Azure のデプロイのパフォーマンスを最適化します。
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: f3c6e39203fb0d864ecf952e0468959d66931e1f
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491581"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Azure Advisor を使用して Azure アプリケーションのパフォーマンスを向上させる

Azure Advisor のパフォーマンスに関する推奨事項は、ビジネスに不可欠なアプリケーションのスピードと応答性を向上させるために役立ちます。 Advisor のパフォーマンスに関する推奨事項は、Advisor ダッシュボードの **[パフォーマンス]** タブで取得できます。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Traffic Manager プロファイルの DNS Time to Live を短縮して迅速に正常なエンドポイントにフェールオーバーする

Traffic Manager プロファイルで [Time to Live (TTL) 設定](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations)を使用すると、特定のエンドポイントがクエリに応答しなくなった場合にエンドポイントを切り替える時間を指定できます。 TTL 値を減らすと、クライアントは機能しているエンドポイントに短時間でルーティングされるようになります。

Azure Advisor は、長い TTL が構成された Traffic Manager プロファイルを識別し、プロファイルが[高速フェールオーバー](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)に構成されているかどうかに応じて TTL を 20 秒または 60 秒に構成することを推奨します。

## <a name="improve-database-performance-with-sql-db-advisor"></a>SQL DB Advisor によるデータベースのパフォーマンス向上

Advisor は、すべての Azure リソースに関する推奨事項を、一貫性がある統合された形で提示します。 SQL Database Advisor と統合して、SQL Azure Database のパフォーマンスを向上させるための推奨事項を生成します。 SQL Database Advisor は、SQL Azure Database の使用履歴を分析することで、パフォーマンスを評価します。 その後、データベースの一般的なワークロードを実行する上で最適な推奨事項を提示します。

> [!NOTE]
> 推奨事項を取得するには、データベースを約 1 週間使用し、その週の間に、何らかの一貫性のあるアクティビティが行われている必要があります。 SQL Database Advisor は、ランダムでむらのあるアクティビティよりも、一貫性のあるアクティビティのクエリ パターンをより簡単に最適化できます。

SQL Database Advisor の詳細については「[SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)」を参照してください。

## <a name="improve-app-service-performance-and-reliability"></a>App Service のパフォーマンスと信頼性の向上

Azure Advisor は、App Services のエクスペリエンスを向上させ、関連するプラットフォームの機能を検出するためのベスト プラクティスの推奨事項を統合します。 App Services に関する推奨事項の例を次に示します。
* アプリの実行時にメモリや CPU のリソースが使い尽くされるインスタンスの検出と軽減オプションの提供。
* Web アプリやデータベースなどのリソースの配置によってパフォーマンスの向上とコストの削減を実現できるインスタンスの検出。

App Services に関する推奨事項の詳細については、「[Azure App Service のベスト プラクティス](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)を参照してください。

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Managed Disks を使用してディスク I/O スロットリングを防ぐ

Advisor では、スケーラビリティ ターゲットに達しているストレージ アカウントに属している仮想マシンが識別されます。 この条件により、これらの VM は I/O スロットリングを受けやすくなります。 Advisor では、これらの仮想マシンが Managed Disks を使用してパフォーマンスの低下を防ぐことを推奨しています。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Premium Storage を使用して仮想マシンのディスクのパフォーマンスと信頼性を向上させる

Advisor では、ストレージ アカウントでトランザクション量の多い Standard ディスクがある仮想マシンが識別されて、Premium ディスクへのアップグレードが推奨されます。 

Azure Premium Storage は、高負荷の I/O ワークロードを実行する仮想マシン向けに高パフォーマンスで待ち時間の少ないディスク サポートを提供します。 Premium Storage アカウントを使用する仮想マシンのディスクでは、ソリッド ステート ドライブ (SSD) にデータを格納します。 アプリケーションで最適なパフォーマンスを実現するには、高い IOPS を必要とする仮想マシン ディスクは Premium Storage に移行することをお勧めします。

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>SQL データ ウェアハウス テーブルのデータ スキューを除去してクエリのパフォーマンスを向上させる

データ スキューは、ワークロードの実行時に不要なデータの移動やリソースのボトルネックを引き起こす可能性があります。 Advisor は 15% を超える分散データスキューを検出し、データの再分散とテーブルの分散キーの再選択を行うことをお勧めします。 スキューの識別と除去の詳細については、[スキューのトラブルシューティング](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)に関する記事を参照してください。

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>SQL データ ウェアハウス テーブルの古いテーブル統計情報を作成するか更新してクエリのパフォーマンスを向上させる

Advisor は、最新の[テーブル統計](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)状態がないテーブルを識別し、テーブル統計を作成または更新することをお勧めします。 SQL データウェアハウスのクエリ オプティマイザーは、最新の統計を使用してクエリ結果のカーディナリティまたは行数を推定して、最速のパフォーマンスをもたらす高品質のクエリ プランを作成できるようにします。

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>スケールアップして SQL Data Warehouse テーブルでのキャッシュ使用率を最適化し、クエリのパフォーマンスを向上させる

Azure Advisor では、SQL Data Warehouse でキャッシュ使用率が高い場合と、ヒット率が低い場合が検出されます。 この状態は、SQL Data Warehouse のパフォーマンスに影響を与える可能性がある高いキャッシュ削除を示しています。 Advisor では、SQL Data Warehouse をスケールアップし、ワークロードに十分なキャッシュ容量が割り当てられるようにすることが推奨されます。

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>SQL Data Warehouse のテーブルをレプリケートされたテーブルに変換し、クエリのパフォーマンスを向上させる

Advisor では、レプリケートされたテーブルではないが、変換によってメリットがあるテーブルが識別されて、これらのテーブルを変換することが推奨されます。 推奨は、レプリケートされるテーブルのサイズ、列の数、テーブルの分散の種類、および SQL Data Warehouse のテーブルのパーティションの数の基づいています。 コンテキストに対する推奨では、追加のヒューリスティックが提供される場合があります。 この推奨事項が決定される方法について詳しくは、「[SQL Data Warehouse のレコメンデーション](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables)」をご覧ください。 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>ストレージ アカウントを Azure Resource Manager に移行して最新の Azure 機能のすべてを手に入れる

ストレージ アカウント デプロイ モデルを Azure Resource Manager (Resource Manager) に移行して、テンプレート デプロイや、追加のセキュリティ オプションをご利用ください。また、GPv2 アカウントにアップグレードして Azure Storage の最新機能を利用することもできます。 Advisor は、クラシック デプロイ モデルを使用しているスタンドアロンのストレージ アカウントをすべて特定して、Resource Manager デプロイ モデルに移行することを推奨します。

> [!NOTE]
> Azure Monitor のクラシック アラートは 2019 年 6 月に廃止される予定です。 新しいプラットフォームでアラート機能を保持するには、Resource Manager を使用するようにクラシック ストレージ アカウントをアップグレードすることをお勧めします。 詳しくは、[クラシック アラートの廃止](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)に関するページをご覧ください。

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Advisor のパフォーマンスに関する推奨事項にアクセスする方法

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

2.  Advisor ダッシュボードで、**[パフォーマンス]** タブをクリックします。

## <a name="next-steps"></a>次の手順

Advisor の推奨事項の詳細については、以下を参照してください。

* [Advisor 入門](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
