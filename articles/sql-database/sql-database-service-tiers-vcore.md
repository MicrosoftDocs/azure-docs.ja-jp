---
title: Azure SQL Database サービス - 仮想コア | Microsoft Docs
description: 仮想コア ベースの購入モデルでは、コンピューティングおよびストレージ リソースを個別にスケーリングし、オンプレミスのパフォーマンスを一致させて、コストを最適化できます。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: edba858f9be3350034ff48ea16d3c9137254bb97
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357935"
---
# <a name="vcore-service-tiers-azure-hybrid-benefit-and-migration"></a>仮想コア サービス層、Azure ハイブリッド特典、および移行

仮想コア ベースの購入モデルでは、コンピューティングおよびストレージ リソースを個別にスケーリングし、オンプレミスのパフォーマンスを一致させて、コストを最適化できます。 ハードウェアの世代を選択することもできます。

- Gen4 - Intel E5-2673 v3 (Haswell) 2.4 GHz プロセッサに基づく最大 24 個の論理 CPU、仮想コア = 1 PP (物理コア)、7 GB/コア、接続されている SSD
- Gen5 - Intel E5-2673 v4 (Broadwell) 2.3 GHz プロセッサに基づく最大 80 個の論理 CPU、仮想コア = 1 LP (ハイパースレッド)、5.1 GB/コア、高速 eNVM SSD

Gen4 ハードウェアでは、仮想コアあたり大幅に多くのメモリが提供されます。 一方、Gen5 ハードウェアでは、コンピューティング リソースをはるかに高くまでスケールアップできます。

> [!NOTE]
> DTU ベースのサービス レベルの詳細については、[DTU ベースのサービス レベル](sql-database-service-tiers-dtu.md)に関するページを参照してください。 DTU ベースのサービス レベルと仮想コアベースのサービス レベルの違いの詳細については、[Azure SQL Database の購入モデル](sql-database-purchase-models.md)に関するページを参照してください。

## <a name="service-tier-characteristics"></a>サービス レベルの特性

仮想コア モデルでは、General Purpose、Hyperscale、Business Critical の 3 つのサービス レベルが提供されます。 サービス レベルは、さまざまなコンピューティング サイズ、高可用性の設計、障害の分離、ストレージの種類、ストレージのサイズ、IO 範囲によって区別されます。 バックアップ用に必要なストレージと保有期間を個別に構成する必要があります。 Azure portal で、(データベースではなく) [サーバー] > [マネージド バックアップ] > [ポリシーの構成] > [Point In Time Restore Configuration]\(ポイント イン タイム リストア構成\) > [7 - 35 days]\(7 ～ 35 日\) の順に移動します。

次の表は、この 3 つのレベルの違いを把握するうえで役立ちます。

||**汎用**|**Business Critical**|**ハイパースケール (プレビュー)**|
|---|---|---|---|
|最適な用途|ほとんどのビジネス ワークロード。 予算重視のスケーラブルでバランスの取れたコンピューティングおよびストレージ オプションを提供します。|IO 要件の高いビジネス アプリケーション。 分離された複数のレプリカを使用して、最高の耐障害性が提供されます。|高度にスケーラブルなストレージと読み取りスケールの要件を持つほとんどのビジネス ワークロード|
|Compute|Gen4:1 ～ 24 仮想コア<br/>Gen5:1 ～ 80 仮想コア|Gen4:1 ～ 24 仮想コア<br/>Gen5:1 ～ 80 仮想コア|Gen4:1 ～ 24 仮想コア<br/>Gen5:1 ～ 80 仮想コア|
|メモリ|Gen4:コアあたり 7 GB<br>Gen5:コアあたり 5.1 GB | Gen4:コアあたり 7 GB<br>Gen5:コアあたり 5.1 GB |Gen4:コアあたり 7 GB<br>Gen5:コアあたり 5.1 GB|
|Storage|リモート ストレージの使用:<br/>単一データベース:5 GB – 4 TB<br/>Managed Instance:32 GB ～ 8 TB |ローカル SSD ストレージの使用:<br/>単一データベース:5 GB – 4 TB<br/>Managed Instance:32 GB ～ 4 TB |柔軟性が高く、必要に応じて自動拡張されるストレージ。 最大 100 TB のストレージなどをサポートします。 ローカル バッファー プール キャッシュとローカル データ ストレージ用のローカル SSD ストレージ。 最終的な長期データ ストアとしての Azure リモート ストレージ。 |
|IO スループット (概算)|単一データベース:仮想コアあたり 500 IOPS (最大 7000 IOPS)</br>Managed Instance:[ファイル サイズ](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)に依存|コアあたり 5000 IOPS (最大 200,000 IOPS)|TBD|
|可用性|1 レプリカ、読み取りスケールなし|3 レプリカ、1 [読み取りスケール レプリカ](sql-database-read-scale-out.md)、<br/>ゾーン冗長 HA|?|
|バックアップ|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7 ～ 35 日 (既定では 7 日)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7 ～ 35 日 (既定では 7 日)|Azure リモート ストレージおよび復元のスナップショットベースのバックアップは、高速復旧のためにこのようなスナップショットを使用します。 バックアップは瞬時に行われ、Compute の IO パフォーマンスには影響しません。 復元は非常に高速で、データ操作のサイズにはなりません (数時間 ～ 数日ではなく、分単位で行われます)。|
|インメモリ|サポートされていません|サポートされています|サポートされていません|
|||

> [!NOTE]
> Azure 無料アカウントと組み合わせて Basic サービス レベルで無料の Azure SQL Database を取得して、Azure を探索できます。 詳細については、「[Azure の無料アカウントでマネージド クラウド データベースを作成する](https://azure.microsoft.com/free/services/sql-database/)」をご覧ください。

- 詳細については、[単一データベースでの仮想コア リソースの制限](sql-database-vcore-resource-limits-single-databases.md)および [Managed Instance での仮想コア リソースの制限](sql-database-managed-instance.md#vcore-based-purchasing-model)に関するページを参照してください。
- General Purpose および Business Critical サービス レベルの詳細については、「[General Purpose and Business Critical service tiers](sql-database-service-tiers-general-purpose-business-critical.md)」(General Purpose サービス レベルと Business Critical サービス レベル) をご覧ください。
- 仮想コアベースの購入モデルにおけるハイパースケール サービス レベルの詳細については、[ハイパースケール サービス レベル](sql-database-service-tier-hyperscale.md)に関するページを参照してください。  

> [!IMPORTANT]
> 必要なコンピューティング能力が仮想コア 1 つ分を下回る場合は、DTU ベースの購入モデルを使用します。

## <a name="azure-hybrid-benefit"></a>Azure ハイブリッド特典

仮想コアベースの購入モデルでは、[SQL Server 向け Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を利用して、お使いの既存のライセンスを SQL Database の割引料金のライセンスに交換できます。 この Azure 特典では、オンプレミスのソフトウェア アシュアランス付き SQL Server ライセンスを利用することで、オンプレミスの SQL Server ライセンスで Azure SQL Database の料金が最大 30% オフになります。

![価格](./media/sql-database-service-tiers/pricing.png)

Azure ハイブリッド特典では、SQL データベース エンジン自体については既存の SQL Server ライセンスを使用して、基になる Azure インフラストラクチャについてのみ支払うか (**BasePrice**)、それとも基になるインフラストラクチャと SQL Server ライセンスの両方を支払うか (**LicenseIncluded**) を選択できます。 Azure portal または次の API のいずれかを使用して、ライセンス モデルを選択または変更できます。

- PowerShell を使用してライセンスの種類を設定または更新するには:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase):
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql)

- Azure CLI を使用してライセンスの種類を設定または更新するには:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- REST API を使用してライセンスの種類を設定または更新するには:

  - [データベース - 作成または更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [データベース - 更新](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migration-from-dtu-model-to-vcore-model"></a>DTU モデルから仮想コア モデルへの移行

### <a name="migration-of-a-database"></a>データベースの移行

DTU ベースの購入モデルから仮想コア ベースの購入モデルへデータベースを移行することは、DTU ベースの購入モデルの Standard および Premium データベース間のアップグレードまたはダウングレードとほぼ同じです。

### <a name="migration-of-databases-with-geo-replication-links"></a>geo レプリケーション リンクを含むデータベースの移行

DTU ベースのモデルから仮想コアベースのモデルへの移行は、Standard データベースと Premium データベースの間で geo レプリケーション関係をアップグレードまたはダウングレードするのと似ています。 geo レプリケーションを終了する必要はありませんが、ユーザーは、シーケンス処理ルールに従う必要があります。 アップグレードの場合は、最初にセカンダリ データベースをアップグレードしてから、プライマリをアップグレードする必要があります。 ダウングレードは逆の順序で行います。つまり、最初にプライマリ データベースをダウングレードしてから、セカンダリをダウングレードします。

2 つのエラスティック プールの間で geo レプリケーションを使用している場合は、一方のプールをプライマリとして指定し、もう一方をセカンダリとして指定することをお勧めします。 その場合、エラスティック プールの移行でも同じガイダンスが使用されます。  ただし、技術的には、1 つのエラスティック プールに、プライマリ データベースとセカンダリ データベースの両方を含めることができます。 この場合、正しく移行するには、使用率の高い方のプールを "プライマリ" と見なし、適宜シーケンス処理ルールに従います。  

次の表に、具体的な移行シナリオのガイダンスを示します。

|現在のサービス レベル|移行先のサービス レベル|移行の種類|ユーザー操作|
|---|---|---|---|
|標準|汎用|ラテラル|任意の順序で移行できますが、適切な仮想コア サイズを確保する必要があります*|
|Premium|Business Critical|ラテラル|任意の順序で移行できますが、適切な仮想コア サイズを確保する必要があります*|
|標準|Business Critical|アップグレード|セカンダリを最初に移行する必要があります|
|Business Critical|標準|ダウングレード|プライマリを最初に移行する必要があります|
|Premium|汎用|ダウングレード|プライマリを最初に移行する必要があります|
|汎用|Premium|アップグレード|セカンダリを最初に移行する必要があります|
|Business Critical|汎用|ダウングレード|プライマリを最初に移行する必要があります|
|汎用|Business Critical|アップグレード|セカンダリを最初に移行する必要があります|
||||

\* Standard レベルでは 100 DTU ごとに 1 つ以上の仮想コア、Premium レベルでは 125 DTU ごとに 1 つ以上の仮想コアが必要です

### <a name="migration-of-failover-groups"></a>フェールオーバー グループの移行

複数のデータベースが含まれるフェールオーバー グループの移行では、プライマリ データベースとセカンダリ データベースを個別に移行する必要があります。 その処理中は、同じ考慮事項とシーケンス処理ルールが適用されます。 データベースが仮想コアベースのモデルに変換された後も、フェールオーバー グループには、同じポリシー設定が引き続き有効になります。

### <a name="creation-of-a-geo-replication-secondary"></a>geo レプリケーション セカンダリの作成

プライマリと同じサービス レベルを使用して、geo セカンダリのみを作成できます。 ログ生成率が高いデータベースの場合、プライマリと同じコンピューティング サイズでセカンダリを作成することをお勧めします。 単一プライマリ データベースに対してエラスティック プールに geo セカンダリを作成する場合、そのプールの `maxVCore` 設定を、プライマリ データベースのコンピューティング サイズと一致させることをお勧めします。 geo セカンダリの作成先エラスティック プールと、そのプライマリがあるエラスティック プールが異なる場合、その 2 つのプールの `maxVCore` 設定を同じにすることをお勧めします

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>データベースのコピーを使用した、DTU ベースのデータベースから仮想コアベースのデータベースへの変換

DTU ベース コンピューティング サイズのデータベースを、仮想コアベース コンピューティング サイズのデータベースにコピーする場合、コピー先のコンピューティング サイズが、コピー元データベースの最大データベース サイズをサポートしている限り、制限や特別なシーケンス処理は伴いません。 データベースのコピーによって、コピー操作を開始した時点のデータ スナップショットが作成されるので、コピー元とコピー先の間でデータ同期が行われません。

## <a name="next-steps"></a>次の手順

- 単一データベースに対して選択できる具体的なコンピューティング サイズとストレージ サイズの詳細については、[単一データベースに対する SQL Database の仮想コアベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)に関するページを参照してください。
- エラスティック プールに対して選択できる具体的なコンピューティング サイズとストレージ サイズの詳細については、[エラスティック プールに対する SQL Database 仮想コア ベースのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)に関するページを参照してください。
