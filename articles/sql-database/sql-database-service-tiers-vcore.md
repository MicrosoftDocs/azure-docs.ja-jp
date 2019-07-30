---
title: Azure SQL Database サービス - 仮想コア | Microsoft Docs
description: 仮想コアベースの購入モデルでは、コンピューティング リソースとストレージ リソースを独立にスケーリングし、オンプレミスのパフォーマンスと一致させて、価格を最適化することができます。
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
ms.date: 06/26/2019
ms.openlocfilehash: e9d1ce3bcd3bf958be0a7837e8416300af03f5a2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449745"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>仮想コア サービス レベルの中から選択し、DTU サービス レベルから移行する

仮想コア (vCore) ベースの購入モデルでは、コンピューティング リソースとストレージ リソースを独立にスケーリングし、オンプレミスのパフォーマンスと一致させて、価格を最適化することができます。 また、ハードウェアの世代を選択することもできます。

- **Gen4**: Intel E5-2673 v3 (Haswell) 2.4 GHz プロセッサに基づく最大 24 個の論理 CPU、仮想コア = 1 PP (物理コア)、7 GB/コア、接続されている SSD
- **Gen5**: Intel E5-2673 v4 (Broadwell) 2.3 GHz プロセッサに基づく最大 80 個の論理 CPU、仮想コア = 1 LP (ハイパースレッド)、5.1 GB/コア、高速 eNVM SSD

Gen4 ハードウェアでは、仮想コアあたり大幅に多くのメモリが提供されます。 一方、Gen5 ハードウェアでは、コンピューティング リソースをはるかに高くまでスケールアップできます。

> [!IMPORTANT]
> 新しい Gen4 データベースは、AustraliaEast リージョンでサポートされなくなりました。
> [!NOTE]
> DTU ベースのサービス レベルについては、[DTU ベースの購入モデルのサービス レベル](sql-database-service-tiers-dtu.md)に関するページを参照してください。 DTU ベースの購入モデルと仮想コアベースの購入モデルのサービス レベルの違いについては、[Azure SQL Database の購入モデル](sql-database-purchase-models.md)に関するページを参照してください。

## <a name="service-tier-characteristics"></a>サービス レベルの特性

仮想コアベースの購入モデルには、General Purpose、Hyperscale、および Business Critical の 3 つのサービス レベルが用意されています。 これらのサービス レベルは、さまざまなコンピューティング サイズ、高可用性の設計、障害分離の方法、ストレージの種類とサイズ、および I/O 範囲によって区別されます。

バックアップ用に必要なストレージと保有期間を個別に構成する必要があります。 バックアップの保有期間を設定するには、Azure Portal を開いて (データベースではなく) サーバーに移動した後、 **[Manage Backups] (バックアップの管理)**  >  **[ポリシーの構成]**  >  **[Point In Time Restore Configuration] (ポイントインタイム リストア構成)**  >  **[7 ～ 35 日]** に移動します。

次の表は、これらの 3 つのレベルの違いについて説明しています。

||**汎用**|**Business Critical**|**Hyperscale**|
|---|---|---|---|
|最適な用途|ほとんどのビジネス ワークロード。 予算重視で、バランスのとれた、スケーラブルなコンピューティングおよびストレージ オプションを提供します。|I/O 要件の高いビジネス アプリケーション。 複数の分離されたレプリカを使用して、障害に対する最も高い回復力を提供します。|高度にスケーラブルなストレージと読み取りスケールの要件を持つほとんどのビジネス ワークロード。|
|Compute|**プロビジョニングされるコンピューティング**:<br/>Gen4:1 ～ 24 の仮想コア<br/>Gen5:2 ～ 80 の仮想コア<br/>**サーバーレス コンピューティング**:<br/>Gen5:0.5 ～ 4 の仮想コア|**プロビジョニングされるコンピューティング**:<br/>Gen4:1 ～ 24 の仮想コア<br/>Gen5:2 ～ 80 の仮想コア|**プロビジョニングされるコンピューティング**:<br/>Gen4:1 ～ 24 の仮想コア<br/>Gen5:2 ～ 80 の仮想コア|
|メモリ|**プロビジョニングされるコンピューティング**:<br/>Gen4:仮想コアあたり 7 GB<br/>Gen5:仮想コアあたり 5.1 GB<br/>**サーバーレス コンピューティング**:<br/>Gen5:仮想コアあたり 3 GB|**プロビジョニングされるコンピューティング**:<br/>Gen4:仮想コアあたり 7 GB<br/>Gen5:仮想コアあたり 5.1 GB |**プロビジョニングされるコンピューティング**:<br/>Gen4:仮想コアあたり 7 GB<br/>Gen5:仮想コアあたり 5.1 GB|
|Storage|リモート ストレージを使用します。<br/>**1 つのデータベースがプロビジョニングされたコンピューティング**:<br/>5 GB – 4 TB<br/>**1 つのデータベースのサーバーレス コンピューティング**:<br/>5 GB から 1 TB<br/>**マネージド インスタンス**:32 GB ～ 8 TB |ローカル SSD ストレージを使用します。<br/>**1 つのデータベースがプロビジョニングされたコンピューティング**:<br/>5 GB – 4 TB<br/>**マネージド インスタンス**:<br/>32 GB ～ 4 TB |必要に応じた、ストレージの柔軟な自動拡張。 最大 100 TB のストレージをサポートします。 ローカル バッファー プール キャッシュとローカル データ ストレージにローカル SSD ストレージを使用します。 最終的な長期間のデータ ストアとして Azure リモート ストレージを使用します。 |
|I/O スループット (概算)|**単一データベース**:仮想コアあたり 500 IOPS (最大 7000 IOPS)。<br/>**マネージド インスタンス**:[ファイルのサイズ](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)に依存します。|コアあたり 5000 IOPS (最大 200,000 IOPS)|Hyperscale は、複数のレベルのキャッシュが存在する複数レベル アーキテクチャです。 有効な IOPS はワークロードによって異なります。|
|可用性|1 レプリカ、読み取りスケール レプリカなし|3 レプリカ、1 [読み取りスケール レプリカ](sql-database-read-scale-out.md)、<br/>ゾーン冗長高可用性 (HA)|1 読み取り/書き込みレプリカ、および 0 ～ 4 [ 読み取りスケール レプリカ ](sql-database-read-scale-out.md)|
|バックアップ|[ 読み取りアクセス geo 冗長ストレージ (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7 ～ 35 日 (既定では 7 日)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7 ～ 35 日 (既定では 7 日)|Azure リモート ストレージでのスナップショット ベースのバックアップ。 このようなスナップショットを使用して復元することで、高速復元が可能になります。 バックアップは瞬時であり、コンピューティング I/O パフォーマンスには影響を与えません。 復元は高速であり、データ サイズに左右される操作ではありません (数時間または数日ではなく、数分で完了)。|
|メモリ内|サポートされていません|サポートされています|サポートされていません|
|||

> [!NOTE]
> Azure 無料アカウントと組み合わせて Basic サービス レベルで無料の Azure SQL データベースを取得できます。 詳細については、「[Azure の無料アカウントでマネージド クラウド データベースを作成する](https://azure.microsoft.com/free/services/sql-database/)」を参照してください。

- 仮想コアのリソース制限の詳細については、[単一データベースでの仮想コアのリソース制限](sql-database-vcore-resource-limits-single-databases.md)および[マネージド インスタンスでの仮想コアのリソース制限](sql-database-managed-instance.md#vcore-based-purchasing-model)に関するページを参照してください。
- General Purpose および Business Critical サービス レベルの詳細については、[General Purpose および Business Critical サービス レベル](sql-database-service-tiers-general-purpose-business-critical.md)に関するページを参照してください。
- 仮想コアベースの購入モデルでの Hyperscale サービス レベルの詳細については、[Hyperscale サービス レベル](sql-database-service-tier-hyperscale.md)に関するページを参照してください。  

## <a name="azure-hybrid-benefit"></a>Azure ハイブリッド特典

仮想コアベースの購入モデルのプロビジョニングされたコンピューティング レベルでは、[SQL Server 向けの Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用して、既存のライセンスを SQL Database の割引料金で交換できます。 この Azure 特典では、ソフトウェア アシュアランスを含むオンプレミスの SQL Server ライセンスを使用して、Azure SQL Database について最大 30 % 節約できます。

![価格](./media/sql-database-service-tiers/pricing.png)

Azure ハイブリッド特典では、SQL データベース エンジン自体には既存の SQL Server ライセンスを使用して、基になる Azure インフラストラクチャに対してのみ支払うか (基本コンピューティングの価格)、または基になるインフラストラクチャと SQL Server ライセンスの両方に対して支払うか (ライセンス込みの価格) を選択できます。

Azure Portal または次のいずれかの API を使用して、ライセンス モデルを選択または変更できます。

- PowerShell を使用してライセンスの種類を設定または更新するには:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

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

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>DTU ベースのモデルから仮想コア ベースのモデルに移行する

### <a name="migrate-a-database"></a>データベースの移行

DTU ベースの購入モデルから仮想コアベースの購入モデルへのデータベースの移行は、DTU ベースの購入モデルでの Standard および Premium サービス レベル間のアップグレードまたはダウングレードに似ています。

### <a name="migrate-databases-with-geo-replication-links"></a>geo レプリケーション リンクを含むデータベースを移行する

DTU ベースのモデルから仮想コアベースの購入モデルへの移行は、Standard および Premium サービス レベルでのデータベース間の geo レプリケーションのリレーションシップのアップグレードまたはダウングレードに似ています。 移行中に geo レプリケーションを停止する必要はありませんが、次のシーケンス処理のルールに従う必要があります。

- アップグレードの場合は、最初にセカンダリ データベースをアップグレードしてから、プライマリをアップグレードする必要があります。
- ダウングレードは逆の順序で行います。つまり、最初にプライマリ データベースをダウングレードしてから、セカンダリをダウングレードします。

2 つのエラスティック プール間で geo レプリケーションを使用している場合は、1 つのプールをプライマリとして、もう一方をセカンダリとして指定することをお勧めします。 その場合、エラスティック プールを移行するときは、同じシーケンス処理のガイダンスを使用する必要があります。 ただし、プライマリ データベースとセカンダリ データベースの両方を含むエラスティック プールがある場合は、使用率が高い方のプールをプライマリとして扱い、それに応じてシーケンス処理のルールに従ってください。  

次の表は、特定の移行シナリオのためのガイダンスを示しています。

|現在のサービス レベル|移行先のサービス レベル|移行の種類|ユーザー操作|
|---|---|---|---|
|標準|汎用|ラテラル|任意の順序で移行できますが、適切な仮想コア サイズを確保する必要があります*|
|Premium|Business Critical|ラテラル|任意の順序で移行できますが、適切な仮想コア サイズを確保する必要があります*|
|Standard|Business Critical|アップグレード|セカンダリを最初に移行する必要があります|
|Business Critical|Standard|ダウングレード|プライマリを最初に移行する必要があります|
|Premium|汎用|ダウングレード|プライマリを最初に移行する必要があります|
|汎用|Premium|アップグレード|セカンダリを最初に移行する必要があります|
|Business Critical|汎用|ダウングレード|プライマリを最初に移行する必要があります|
|汎用|Business Critical|アップグレード|セカンダリを最初に移行する必要があります|
||||

\* Standard レベルでは 100 DTU ごとに少なくとも 1 つの仮想コアが必要であり、Premium レベルでは 125 DTU ごとに少なくとも 1 つの仮想コアが必要です。

### <a name="migrate-failover-groups"></a>フェールオーバー グループを移行する

複数のデータベースが含まれるフェールオーバー グループの移行では、プライマリ データベースとセカンダリ データベースを個別に移行する必要があります。 その処理中は、同じ考慮事項とシーケンス処理ルールが適用されます。 データベースが仮想コアベースの購入モデルに変換された後も、フェールオーバー グループでは同じポリシー設定が引き続き有効です。

### <a name="create-a-geo-replication-secondary-database"></a>geo レプリケーションのセカンダリ データベースを作成する

geo レプリケーションのセカンダリ データベース (geo セカンダリ) は、プライマリ データベースに対して使用したのと同じサービス レベルを使用してのみ作成できます。 ログ生成速度が速いデータベースの場合は、プライマリと同じコンピューティング サイズを持つ geo セカンダリを作成することをお勧めします。

geo セカンダリを単一プライマリ データベースのエラスティック プール内に作成している場合は、そのプールの `maxVCore` 設定がプライマリ データベースのコンピューティング サイズに一致していることを確認してください。 プライマリの geo セカンダリを別のエラスティック プール内に作成している場合は、そのプールに同じ `maxVCore` 設定を割り当てることをお勧めします。

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>データベースのコピーを使用して DTU ベースのデータベースを仮想コア ベースのデータベースに変換する

DTU ベース コンピューティング サイズのデータベースを、仮想コアベース コンピューティング サイズのデータベースにコピーする場合、コピー先のコンピューティング サイズが、コピー元データベースの最大データベース サイズをサポートしている限り、制限や特別なシーケンス処理は伴いません。 データベースのコピーでは、コピー操作が開始された時点のデータのスナップショットが作成され、ソースとターゲットの間でデータは同期されません。

## <a name="next-steps"></a>次の手順

- 単一データベースに対して使用できる特定のコンピューティング サイズとストレージ サイズの選択については、[単一データベースに対する SQL Database の仮想コア ベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md)に関するページを参照してください。
- エラスティック プールに対して使用できる特定のコンピューティング サイズとストレージ サイズの選択については、[エラスティック プールに対する SQL Database の仮想コア ベースのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)に関するページを参照してください。
