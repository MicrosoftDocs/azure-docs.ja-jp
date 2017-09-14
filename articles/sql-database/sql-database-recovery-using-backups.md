---
title: "バックアップから Azure SQL Database を復元する | Microsoft Docs"
description: "Azure SQL Database を特定の時点 (最長 35 日間) にロール バックすることができる、ポイントインタイム リストアについて説明します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: monicar
ms.assetid: fd1d334d-a035-4a55-9446-d1cf750d9cf7
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/25/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: df6e4bba9290c6129c9cba1440bb0c903aacc3c8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>データベースの自動バックアップを使用した Azure SQL Database の復旧
SQL Database は、[自動データベース バックアップ](sql-database-automated-backups.md)および[長期保存でのバックアップ](sql-database-long-term-retention.md)を使用して、データベース復旧のためのこれらのオプションを提供します。 データベース バックアップは、次のものに復元できます。

* 保有期間内の特定の時点に復旧された、同じ論理サーバー上の新しいデータベース。 
* 削除済みデータベースの削除時に復旧された、同じ論理サーバー上のデータベース。
* geo レプリケートされている BLOB ストレージ (RA-GRS) の最新の日次バックアップの時点に復旧された、任意のリージョンの論理サーバー上の新しいデータベース。

> [!IMPORTANT]
> 復元中に既存のデータベースを上書きすることはできません。
>

復元されたデータベースでは、次の条件下で、追加のストレージ コストが生じます。 
- データベースの最大サイズが 500 GB より大きい場合に、P11–P15 を S4-S12 または P1–P6 に復元する。
- データベースの最大サイズが 250 GB より大きい場合に、P1–P6 または PRS1–PRS6 を S4-S12 に復元する。

余分なコストがかかるのは復元されるデータベースの最大サイズがパフォーマンス レベルに含まれるストレージの量を超えるためであり、含まれる量を超えてプロビジョニングされる余分なストレージに追加料金がかかります。  追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。  実際に使われる領域の量が含まれるストレージの量より少ない場合、データベースの最大サイズを含まれる量に減らすことで、この追加コストを回避できます。 データベース ストレージのサイズおよびデータベースの最大サイズを変更する方法について詳しくは、次を参照してください。[単一データベースのリソース制限に関する記事](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels)をご覧ください。  

> [!NOTE]
> [データベースのコピー](sql-database-copy.md)を作成するときは、[自動データベース バックアップ](sql-database-automated-backups.md)が使われます。 
>


## <a name="recovery-time"></a>復旧時間
データベースの自動バックアップを使用してデータベースを復元する復旧時間は、さまざまな要因によって影響を受けます。 

* データベースのサイズ
* データベースのパフォーマンス レベル
* 関連するトランザクション ログ数
* 復元時点に復旧するために再生の必要があるアクティビティ量
* 別のリージョンへの復元の場合は、ネットワーク帯域幅 
* ターゲット リージョンで処理される、同時実行される復元要求の数 
  
  大規模なデータベースや、アクティビティ数が多いデータベースの場合、復元に数時間かかることがあります。 あるリージョンでの停止が長引いた場合、多数の geo リストア要求が他のリージョンによって処理されることがあります。 多数の要求がある場合、そのリージョンでのデータベースの復旧時間が長くなる可能性があります。 ほとんどのデータベースの復元は、12 時間以内に完了します。
  
一括復元を実行する機能は組み込まれていません。 このタスクを達成する 1 つの方法として、たとえば、 [Azure SQL Database: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) スクリプトがあります。

> [!IMPORTANT]
> 自動バックアップを使って復旧するには、サブスクリプションにおける SQL Server の共同作成者ロールのメンバーまたはサブスクリプション所有者である必要があります。 復旧には、Azure Portal、PowerShell、または REST API を使用できます。 Transact-SQL は使用できません。 
> 

## <a name="point-in-time-restore"></a>ポイントインタイム リストア

Azure Portal、[PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.sql/restore-azurermsqldatabase)、または [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) を使用して、既存のデータベースを同じ論理サーバー上の新しいデータベースとして以前の時点に復元できます。 

> [!TIP]
> データベースのポイントインタイム リストアの実行方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用した SQL データベースの復元](scripts/sql-database-restore-database-powershell.md)に関するページを参照してください。
>

任意のサービス レベルまたはパフォーマンス レベルに、1 つのデータベースとして、またはエラスティック プールにデータベースを復元できます。 データベースを復元する論理サーバーまたはエラスティック プールに十分なリソースがあることを確認します。 完了すると、復元されたデータベースは完全にアクセス可能な通常のオンライン データベースになります。 復元されたデータベースは、サービス レベルとパフォーマンス レベルに基づいて通常料金が発生します。 データベースの復元が完了するまで、料金は発生しません。

通常、以前の時点へのデータベースの復元は、復旧の目的で行います。 その場合、復元されたデータベースを元のデータベースの代わりとして扱うか、データを取得して元のデータベースの更新に使用できます。 

* ***データベースの置換***: 復元されたデータベースを元のデータベースの代わりに使用する場合は、パフォーマンス レベルやサービス レベルが適切であることを確認し、必要に応じてデータベースをスケールしてください。 T-SQL の ALTER DATABASE コマンドを使用して、元のデータベース名を変更し、復元したデータベースに元の名前を付けます。 
* ***データの復旧***: ユーザー エラーまたはアプリケーション エラーからの復旧のために、復元されたデータベースからデータを取得する場合は、復元されたデータベースから元のデータベースにデータを抽出するのに必要なデータ復旧のスクリプトを書き込み、実行する必要があります。 復元操作が完了するまでに時間がかかる可能性がありますが、復元プロセスを通して、復元しているデータベースがデータベース一覧に表示されます。 復元中にデータベースを削除すると、復元操作は取り消され、復元が完了していないデータベースの料金はかかりません。 

### <a name="azure-portal"></a>Azure ポータル

Azure Portal を使用してある時点に復旧するには、データベースのページを開き、ツールバーの **[復元]** をクリックします。

![ポイントインタイム リストア](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

## <a name="deleted-database-restore"></a>削除されたデータベースの復元
Azure Portal、[PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.sql/restore-azurermsqldatabase)、または [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) を使用して、削除されたデータベースを同じ論理サーバー上の削除されたデータベースの削除時刻に復元できます。 

> [!TIP]
> 削除されたデータベースの復元方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用した SQL データベースの復元](scripts/sql-database-restore-database-powershell.md)に関するページを参照してください。
>

> [!IMPORTANT]
> Azure SQL Database サーバー インスタンスを削除すると、そのデータベースもすべて削除されます。これを回復することはできません。 現時点では、削除されたサーバーを復元するためのサポートはありません。
> 

### <a name="azure-portal"></a>Azure ポータル

Azure Portal を使用して、削除されたみデータベースをその[保有期間](sql-database-service-tiers.md)中に復旧するには、サーバーのページを開き、[操作] 領域で **[削除済みデータベース]** をクリックします。

![削除されたデータベースの復元 1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)


![削除されたデータベースの復元 2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

## <a name="geo-restore"></a>geo リストア
geo レプリケートされた最新の完全バックアップおよび差分バックアップから任意の Azure リージョン内の任意のサーバーで SQL データベースを復元することができます。 geo リストアではソースとして geo 冗長バックアップが使用され、障害によってデータベースまたはデータセンターにアクセスできない場合でも、geo リストアを使用してデータベースを復旧できます。 

geo リストアは、データベースがホストされているリージョンでのインシデントが原因でデータベースが利用できない場合の既定の復旧オプションです。 リージョン内の大規模なインシデントにより、データベース アプリケーションが使用できなくなった場合、geo レプリケートされたバックアップから他の任意のリージョン内のサーバーにデータベースを復元できます。 差分バックアップが取得される時刻と、別のリージョンの Azure BLOB にその差分バックアップが geo レプリケートされる時刻には時間差があります。 この時間差は最大 1 時間なので、障害が発生した場合、最大 1 時間分のデータが損失する可能性があります。 次の図は、別のリージョン内の利用可能な最新のバックアップからのデータベースの復元を示しています。

![geo リストア](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> geo リストアの実行方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用した SQL データベースの復元](scripts/sql-database-restore-database-powershell.md)に関するページを参照してください。
> 

geo セカンダリでのポイントインタイム リストアは、現在はサポートされていません。 ポイントインタイム リストアは、プライマリ データベースでのみ実行できます。 geo リストアを使用して障害から復旧する方法の詳細については、[障害からの復旧](sql-database-disaster-recovery.md)に関するページを参照してください。

> [!IMPORTANT]
> バックアップからの復旧は、RPO と推定復旧時間 (ERT) が最も長い、SQL Database で使用できる障害復旧ソリューションで最も基本的なことです。 Basic データベースを使用しているソリューションでは、多くの場合、ERT が 12 時間である geo リストアが妥当なソリューションです。 短時間で復旧する必要がある大型の Standard または Premium データベースでは、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)の使用を検討する必要があります。 アクティブ geo レプリケーションでは、継続的にレプリケートされるセカンダリへのフェールオーバーを開始することだけが必要なので、RPO と ERT が大きく短縮されます。 ビジネスを継続するための選択の詳細については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページを参照してください。
> 

### <a name="azure-portal"></a>Azure Portal

Azure Portal を使用して、データベースをその[保有期間](sql-database-service-tiers.md)中に geo リストアするには、[SQL データベース] ページを開き、**[追加]** をクリックします。 **[ソースの選択]** テキスト ボックスで、**[バックアップ]** を選択します。 任意のリージョン内のサーバー上で復旧を実行するバックアップを指定します。 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>自動バックアップを使用したプログラム実行の復旧
前に説明したように、データベースの復旧は、Azure Portal のほかに、Azure PowerShell または REST API を使用してプログラムで実行できます。 次の表では、使用できるコマンド セットについて説明します。

### <a name="powershell"></a>PowerShell
| コマンドレット | 説明 |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |1 つまたは複数のデータベースを取得します。 |
| [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | 復元する削除済みデータベースを取得します。 |
| [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |データベースの geo 冗長バックアップを取得します。 |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |SQL Database を復元します。 |
|  | |

### <a name="rest-api"></a>REST API
| API | 説明 |
| --- | --- |
| [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |データベースを復元します |
| [Get Create or Update Database Status](https://msdn.microsoft.com/library/azure/mt643934.aspx) |復元操作中にステータスを返します |
|  | |

## <a name="summary"></a>概要
自動バックアップは、ユーザー エラーやアプリケーション エラー、偶発的なデータベースの削除、および長期間にわたる障害からデータベースを保護します。 この組み込みの機能は、すべてのサービス レベルとパフォーマンス レベルで使用できます。 

## <a name="next-steps"></a>次のステップ
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)
* Azure SQL Database 自動バックアップの詳細については、「 [SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)
* バックアップの長期保存については、[バックアップの長期保存](sql-database-long-term-retention.md)に関する記事を参照してください。
* Azure Portal を使用して、Azure Recovery Services コンテナー内の自動バックアップの長期保存を構成したり、管理したり、それから復元したりするには、[バックアップの長期保有期間の構成および使用](sql-database-long-term-backup-retention-configure.md)を参照してください。 
* より迅速な復旧オプションについては、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。  

