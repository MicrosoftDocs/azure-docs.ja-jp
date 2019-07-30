---
title: Azure SQL Database サーバーのリソース制限 | Microsoft Docs
description: この記事では、単一データベースとエラスティック プール用の Azure SQL Database サーバーのリソース制限について概要を示します。 また、これらのリソース制限に達した場合、または制限を超えた場合の動作に関する情報も提供します。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
manager: craigg
ms.date: 04/18/2019
ms.openlocfilehash: 4e4c0a6cd25587b33c06526b57e6acdbebb69c8b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445641"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Azure SQL Database サーバーの SQL Database リソース制限

この記事では、単一データベースとエラスティック プールを管理する SQL Database サーバーの SQL Database リソース制限について概要を示します。 また、これらのリソース制限に達した場合、または制限を超えた場合の動作に関する情報も提供します。

> [!NOTE]
> マネージド インスタンスの制限については、[マネージド インスタンスに関する SQL Database のリソース制限](sql-database-managed-instance-resource-limits.md)に関するページを参照してください。

## <a name="maximum-resource-limits"></a>最大リソース制限

| Resource | 制限 |
| :--- | :--- |
| サーバーあたりのデータベース数 | 5000 |
| 任意のリージョンにおけるサブスクリプションあたりの既定のサーバー数 | 20 |
| 任意のリージョンにおけるサブスクリプションあたりの最大サーバー数 | 200 |  
| サーバーあたりの DTU/eDTU クォータ | 54,000 |  
| サーバー/インスタンスあたりの仮想コア クォータ | 540 |
| サーバーあたりの最大プール | DTU または仮想コアの数によって制限されます。 たとえば、各プールが 1000 DTU の場合、1 つのサーバーで 54 プールをサポートできます。|
|||

> [!NOTE]
> 既定量よりも多い DTU/eDTU クオータ、仮想コア クラスター、またはサーバーを取得する場合は、Azure portal で、目的のサブスクリプションに対して、発行の種類を [クオータ] として新しいサポート要求を送信できます。 サーバーあたりの DTU/eDTU クオータとデータベース制限には、サーバーあたりのエラスティック プールの数が含まれます。
> [!IMPORTANT]
> データベースの数が SQL Database サーバーあたりの制限に近づくと、次の状況が発生します。
> - マスター データベースに対して実行するクエリの待機時間が増えます。  これには、リソース使用率統計情報のビューも含まれます (sys.resource_stats など)。
> - サーバー内のデータベースの列挙を要する、管理操作やポータル ビュー ポイント表示の待機時間が長くなります。

### <a name="storage-size"></a>ストレージ サイズ
- 単一データベース リソースについては、[DTU ベースのリソース制限](sql-database-dtu-resource-limits-single-databases.md)に関するページ、または価格レベルごとのストレージ サイズ制限についての[仮想コアベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md)に関するページのいずれかを参照してください。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>データベース リソースが制限に達したときの影響

### <a name="compute-dtus-and-edtus--vcores"></a>コンピューティング (DTU と eDTU/vCores)

(DTU と、eDTU または vCores によって測定された) データベース コンピューティングの使用率が高くなると、クエリの待機時間が増加し、タイムアウトすることさえあります。このような状況下では、クエリはサービスによってキューに格納されることがあり、リソースが空くと実行用のリソースを提供されます。
高いコンピューティング使用率が発生する場合、次のような軽減オプションがあります。

- データベースまたエラスティック プールのコンピューティング サイズを上げて、より多くのコンピューティング リソースをデータベースに提供します。 [シングルトンのリソースの拡大縮小に関する記事](sql-database-single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](sql-database-elastic-pool-scale.md)を参照してください。
- クエリを最適化して、各クエリのリソース使用率を引き下げます。 詳しくは、「[クエリの調整とヒント](sql-database-performance-guidance.md#query-tuning-and-hinting)」をご覧ください。

### <a name="storage"></a>Storage

使用済みのデータベース容量が最大サイズの上限に達すると、データのサイズが増えるデータベースの挿入および更新は失敗し、クライアントは[エラー メッセージ](sql-database-develop-error-messages.md)を受け取ります。 データベースの SELECTS と DELETES は引き続き成功します。

高い容量使用率が発生する場合、次のような軽減オプションがあります。

- データベースまたはエラスティック プールの最大サイズを増やすか、より多くの記憶域を追加します。 [シングルトンのリソースの拡大縮小に関する記事](sql-database-single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](sql-database-elastic-pool-scale.md)を参照してください。
- データベースがエラスティック プール内にある場合は、もう 1 つの方法として、データベースをプールの外に移動し、ストレージ領域が他のデータベースと共有されないようにすることもできます。
- 未使用領域を再利用できるようにデータベースを縮小します。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

### <a name="sessions-and-workers-requests"></a>セッションとワーカー (要求)

セッションおよびワーカーの最大数は、サービス レベルとコンピューティング サイズ (DTU と eDTU) によって決まります。 セッションまたはワーカーが上限に達した場合、新しい要求は拒否され、クライアントはエラー メッセージを受け取ります。 利用可能な接続の数はアプリケーションで制御できますが、同時ワーカーの数は推定または制御が困難なことがよくあります。 データベース リソースが上限に達し、クエリを長時間実行したためにワーカーが滞留するピーク負荷期間は特にそうです。

セッションまたはワーカーの使用率が高い場合は、次のような軽減オプションがあります。

- データベースまたはエラスティック プールのサービス レベルまたはコンピューティング サイズを高くします。 [シングルトンのリソースの拡大縮小に関する記事](sql-database-single-database-scale.md)と、[エラスティック プールのリソースの拡大縮小に関する記事](sql-database-elastic-pool-scale.md)を参照してください。
- ワーカー使用率上昇の原因がコンピューティング リソースの競合である場合は、クエリを最適化して各クエリのリソース使用率を下げます。 詳しくは、「[クエリの調整とヒント](sql-database-performance-guidance.md#query-tuning-and-hinting)」をご覧ください。

## <a name="transaction-log-rate-governance"></a>トランザクション ログ速度ガバナンス 
トランザクション ログ速度ガバナンスは、一括挿入、SELECT INTO、インデックス作成などのワークロードの高いインジェクション速度を制限するために使用される、Azure SQL Database 内のプロセスです。 こうした制限は追跡され、1 秒未満のレベルでログ レコード生成速度に適用されて、データ ファイルに対して発行できる IO の数に関係なく、スループットが制限されます。  現在、トランザクション ログ生成速度は、ハードウェアに依存するポイントまで直線的にスケールアップされます。許容される最大ログ速度は、仮想コア購入モデルで 96 MB/秒です。 

> [!NOTE]
> トランザクション ログ ファイルへの実際の物理的な IO は、管理または制限されません。 

ログ速度をさまざまなシナリオで実現し維持できるとともに、ユーザー負荷への影響を最小限に抑えながらシステム全体の機能を維持できように、ログ速度が設定されます。 ログ速度ガバナンスにより、トランザクション ログ バックアップは、発行された復元可能性 SLA 内で維持されます。  また、このガバナンスにより、セカンダリ レプリカ上の過剰なバックログが回避されます。

ログ レコードが生成されると、各操作が評価され、望ましい最大ログ速度 (MB/秒) を維持するために遅延する必要があるかどうかが判断されます。 ログ レコードがストレージにフラッシュされる際には、遅延は追加されません。ログ速度ガバナンスは、ログ速度生成プロセス自体に適用されます。

実行時に適用される実際のログ生成速度は、フィードバック メカニズムによっても影響される可能性があります。この場合、システムを安定化するために許容ログ速度が一時的に低下します。 ログ領域の不足状態を回避しようとするログ ファイル領域管理と、可用性グループのレプリケーション メカニズムにより、全体的なシステムの制限が一時的に低くなる可能性があります。 

ログ速度ガバナー トラフィックの構成は、次の種類の待機を使用して表示されます ([sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV で公開されます)。

| 待機の種類 | メモ |
| :--- | :--- |
| LOG_RATE_GOVERNOR | データベース制限 |
| POOL_LOG_RATE_GOVERNOR | プール制限 |
| INSTANCE_LOG_RATE_GOVERNOR | インスタンス制限 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | フィードバック制御、Premium/Business Critical での可用性グループの物理的なレプリケーションが維持されていない |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | フィードバック制御、ログ領域の不足を回避するために速度を制限 |
|||

望ましいスケーラビリティを損なうログ速度制限が発生した場合は、次のオプションを検討してください。
- 最大 96 MB/秒のログ速度を実現するために、より大きなレベルにスケールアップします。 
- 読み込まれるデータが一時的なデータである場合、つまり、ETL プロセスでのステージング データである場合は、tempdb に読み込むことができます (この場合、ログ記録が最小限に抑えられます)。 
- 分析シナリオでは、クラスター化列ストアの対象テーブルに読み込みます。 この場合は圧縮されるため、必要なログ速度が小さくなります。 この手法では CPU 使用率が増加し、クラスター化列ストア インデックスからメリットを得られるデータ セットにのみ適用できます。 

## <a name="next-steps"></a>次の手順

- Azure の一般的な制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」をご覧ください。
- DTU と eDTU については、「[データベース トランザクション ユニット (DTU) とエラスティック データベース トランザクション ユニット (eDTU) の説明](sql-database-purchase-models.md#dtu-based-purchasing-model)」をご覧ください。
- tempdb のサイズ制限については、「[SQL Database の Tempdb データベース](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)」をご覧ください。
