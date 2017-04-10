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
ms.date: 02/20/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 751e1eaf65da889ab5c7dc26145c017682a12a4d
ms.lasthandoff: 03/28/2017


---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>データベースの自動バックアップを使用した Azure SQL Database の復旧
SQL Database は、[自動データベース バックアップ](sql-database-automated-backups.md)を使用したデータベース復旧の 3 つのオプションを提供します。 データベース バックアップの[保有期間](sql-database-service-tiers.md)中は、以下のデータべースにデータベースを復旧できます。

* 保有期間内の特定の時点に復旧された、同じ論理サーバー上の新しいデータベース。 
* 削除済みデータベースの削除時に復旧された、同じ論理サーバー上のデータベース。
* geo レプリケートされている BLOB ストレージ (RA-GRS) の最新の日次バックアップの時点に復旧された、任意のリージョンの論理サーバー上の新しいデータベース。

> [!TIP]
> チュートリアルについては、「[データの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery-portal.md)」を参照してください。
>

[自動データベース バックアップ](sql-database-automated-backups.md)を使用して、任意のリージョンの論理サーバーに[データベースのコピー](sql-database-copy.md)を作成することもできます。 また SQL Database サーバーを構成して、最大で 10 年間、Azure Recovery Services コンテナーに Azure SQL Database のバックアップを格納することができます。 [バックアップの長期保有期間](sql-database-long-term-retention.md)に関するページを参照してください。

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

[Azure Portal](sql-database-point-in-time-restore-portal.md)、[PowerShell](scripts/sql-database-restore-database-powershell.md)、または [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) を使用して、同じ論理サーバー上の新しいデータベースとして、既存のデータベースを以前の時点に復元することができます。 

> [!IMPORTANT]
> 復元中に既存のデータベースを上書きすることはできません。
>

任意のサービス レベルまたはパフォーマンス レベルに、1 つのデータベースとして、またはエラスティック プールにデータベースを復元できます。 データベースを復元する論理サーバーまたはエラスティック プールに十分なリソースがあることを確認します。 完了すると、復元されたデータベースは完全にアクセス可能な通常のオンライン データベースになります。 復元されたデータベースは、サービス レベルとパフォーマンス レベルに基づいて通常料金が発生します。 データベースの復元が完了するまで、料金は発生しません。

通常、以前の時点へのデータベースの復元は、復旧の目的で行います。 その場合、復元されたデータベースを元のデータベースの代わりとして扱うか、データを取得して元のデータベースの更新に使用できます。 

* ***データベースの置換***: 復元されたデータベースを元のデータベースの代わりに使用する場合は、パフォーマンス レベルやサービス レベルが適切であることを確認し、必要に応じてデータベースをスケールしてください。 T-SQL の ALTER DATABASE コマンドを使用して、元のデータベース名を変更し、復元したデータベースに元の名前を付けます。 
* ***データの復旧***: ユーザー エラーまたはアプリケーション エラーからの復旧のために、復元されたデータベースからデータを取得する場合は、復元されたデータベースから元のデータベースにデータを抽出するのに必要なデータ復旧のスクリプトを書き込み、実行する必要があります。 復元操作が完了するまでに時間がかかる可能性がありますが、復元プロセスを通して、復元しているデータベースがデータベース一覧に表示されます。 復元中にデータベースを削除すると、復元操作は取り消され、復元が完了していないデータベースの料金はかかりません。 

## <a name="deleted-database-restore"></a>削除されたデータベースの復元
[Azure Portal](sql-database-restore-deleted-database-portal.md)、[PowerShell](scripts/sql-database-restore-database-powershell.md)、または [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) を使用して、削除済みデータベースを、削除された時点の内容で同じ論理サーバー上に復元できます。 

> [!IMPORTANT]
> Azure SQL Database サーバー インスタンスを削除すると、そのデータベースもすべて削除されます。これを回復することはできません。 現時点では、削除されたサーバーを復元するためのサポートはありません。
> 

## <a name="geo-restore"></a>地理リストア
geo レプリケートされた最新の完全バックアップおよび差分バックアップから任意の Azure リージョン内の任意のサーバーで SQL データベースを復元することができます。 geo リストアではソースとして geo 冗長バックアップが使用され、障害によってデータベースまたはデータセンターにアクセスできない場合でも、geo リストアを使用してデータベースを復旧できます。 [Azure Portal](sql-database-geo-restore-portal.md)、[PowerShell](sql-database-geo-restore-powershell.md)、または [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) を使用することができます。 

geo リストアは、データベースがホストされているリージョンでのインシデントが原因でデータベースが利用できない場合の既定の復旧オプションです。 リージョン内の大規模なインシデントにより、データベース アプリケーションが使用できなくなった場合、geo レプリケートされたバックアップから他の任意のリージョン内のサーバーにデータベースを復元できます。 差分バックアップが取得される時刻と、別のリージョンの Azure BLOB にその差分バックアップが geo レプリケートされる時刻には時間差があります。 この時間差は最大 1 時間なので、障害が発生した場合、最大 1 時間分のデータが損失する可能性があります。 次の図は、別のリージョン内の利用可能な最新のバックアップからのデータベースの復元を示しています。

![地理リストア](./media/sql-database-geo-restore/geo-restore-2.png)

geo リストアを使用して障害から復旧する方法の詳細については、 [障害からの復旧](sql-database-disaster-recovery.md)

> [!IMPORTANT]
> バックアップからの復旧は、RPO と推定復旧時間 (ERT) が最も長い、SQL Database で使用できる障害復旧ソリューションで最も基本的なことです。 最大サイズが 2 GB の Basic データベースでは、geo リストアは ERT が 12 時間である妥当な障害復旧ソリューションを提供します。 さらに大きい Standard または Premium データベースでは、短い復旧間隔が必要な場合、またはデータ損失の可能性を下げる場合は、アクティブ geo レプリケーションの使用を検討する必要があります。 アクティブ geo レプリケーションでは、継続的にレプリケートされるセカンダリへのフェールオーバーを開始することだけが必要なので、RPO と ERT が大きく短縮されます。 詳細については、 [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。
> 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>自動バックアップを使用したプログラム実行の復旧
前に説明したように、データベースの復旧は、Azure Portal のほかに、Azure PowerShell または REST API を使用してプログラムで実行できます。 次の表では、使用できるコマンド セットについて説明します。

### <a name="powershell"></a>PowerShell
| コマンドレット | 説明 |
| --- | --- |
| [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx) |1 つまたは複数のデータベースを取得します。 |
| [Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx) |復元する削除済みデータベースを取得します。 |
| [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) |データベースの geo 冗長バックアップを取得します。 |
| [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) |SQL Database を復元します。 |
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
* Azure Portal を使用して、Azure Recovery Services コンテナー内で長期保存されている自動バックアップを構成、管理、および復旧する場合、[Azure Portal を使用した長期バックアップ保存の管理](sql-database-manage-long-term-backup-retention-portal.md)に関する記事を参照してください。 
* PowerShell を使用して、Azure Recovery Services コンテナー内で長期保存されている自動バックアップを構成、管理、および復旧する場合、[PowerShell を使用した長期バックアップ保存の管理](sql-database-manage-long-term-backup-retention-powershell.md)に関する記事を参照してください。
* より迅速な復旧オプションについては、 [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)  
* 自動バックアップを使用したアーカイブについては、 [データベースのコピー](sql-database-copy.md)

